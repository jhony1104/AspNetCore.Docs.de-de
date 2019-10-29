---
title: Einführung in Razor Pages in ASP.NET Core
author: Rick-Anderson
description: Erfahren Sie, wie Razor Pages in ASP.NET Core codierungsseitige Szenarios einfacher und produktiver gestalten als MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 10/07/2019
uid: razor-pages/index
ms.openlocfilehash: 61e15b9b9b8f84de36621c301ecb9d33b21dff88
ms.sourcegitcommit: 16cf016035f0c9acf3ff0ad874c56f82e013d415
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73034280"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="5eb2f-103">Einführung in Razor Pages in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5eb2f-103">Introduction to Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5eb2f-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="5eb2f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="5eb2f-105">Razor Pages kann im Vergleich zu Controllern und Ansichten das Programmieren seitenbasierter Anwendungen vereinfachen und die Produktivität erhöhen.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-105">Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="5eb2f-106">Ein Tutorial, in dem der Model-View-Controller-Ansatz verwendet wird, finden Sie unter [Erste Schritte mit ASP.NET Core MVC und Visual Studio](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="5eb2f-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="5eb2f-107">Dieses Dokument bietet eine Einführung in Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="5eb2f-108">Es handelt sich nicht um ein Schritt-für-Schritt-Tutorial.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="5eb2f-109">Wenn es Ihnen Probleme bereitet, die Ausführungen in einigen Abschnitten nachzuvollziehen, lesen Sie [Erste Schritte mit Razor-Seiten in ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="5eb2f-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="5eb2f-110">Eine Übersicht über ASP.NET Core finden Sie unter [Einführung in ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="5eb2f-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5eb2f-111">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="5eb2f-111">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5eb2f-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5eb2f-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5eb2f-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5eb2f-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5eb2f-114">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="5eb2f-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="5eb2f-115">Erstellen eines Razor Pages-Projekts</span><span class="sxs-lookup"><span data-stu-id="5eb2f-115">Create a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5eb2f-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5eb2f-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5eb2f-117">Ausführliche Informationen zum Erstellen eines Razor Pages-Projekts finden Sie unter [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="5eb2f-117">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5eb2f-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5eb2f-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5eb2f-119">Führen Sie `dotnet new webapp` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-119">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5eb2f-120">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="5eb2f-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5eb2f-121">Führen Sie `dotnet new webapp` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-121">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="5eb2f-122">Öffnen Sie die generierte Datei *.csproj* aus Visual Studio für Mac.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-122">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="5eb2f-123">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5eb2f-123">Razor Pages</span></span>

<span data-ttu-id="5eb2f-124">Razor Pages ist in *Startup.cs* aktiviert:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-124">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12)]

<span data-ttu-id="5eb2f-125">Sehen Sie sich diese einfache Seite an: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="5eb2f-125">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="5eb2f-126">Der vorherige Code ähnelt sehr einer [Razor-Ansichtsdatei](xref:tutorials/first-mvc-app/adding-view), die in einer ASP.NET Core-App mit Controllern und Ansichten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-126">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="5eb2f-127">Der Unterschied besteht in der [@page](xref:mvc/views/razor#page)-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-127">What makes it different is the [@page](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="5eb2f-128">`@page` macht die Datei zu einer MVC-Aktion, d.h. dass Anfragen direkt ohne einen Controller verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-128">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="5eb2f-129">`@page` muss die erste Razor-Anweisung auf einer Seite sein.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-129">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="5eb2f-130">`@page` wirkt sich auf das Verhalten aller anderen [Razor](xref:mvc/views/razor)-Konstrukte aus.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-130">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="5eb2f-131">Razor Pages-Dateinamen haben das Suffix *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-131">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="5eb2f-132">Eine ähnliche Seite, die die `PageModel`-Klasse verwendet, wird in den folgenden zwei Dateien angezeigt.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-132">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="5eb2f-133">Die Datei *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-133">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="5eb2f-134">Das Seitenmodell *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-134">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="5eb2f-135">Die `PageModel`-Klassendatei hat standardmäßig den gleichen Namen wie die Datei mit Razor Pages, nur dass außerdem *cs* angefügt wird.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-135">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="5eb2f-136">Die vorherige Datei mit Razor Pages lautet beispielsweise *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-136">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="5eb2f-137">Die Datei mit der `PageModel`-Klasse heißt *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-137">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="5eb2f-138">Die Zuordnungen von URL-Pfaden zu Seiten werden durch den Speicherort der Seite im Dateisystem bestimmt.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-138">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="5eb2f-139">Die folgende Tabelle zeigt einen Pfad zu Razor Pages und die entsprechende URL:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-139">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="5eb2f-140">Dateiname und Pfad</span><span class="sxs-lookup"><span data-stu-id="5eb2f-140">File name and path</span></span>               | <span data-ttu-id="5eb2f-141">Entsprechende URL</span><span class="sxs-lookup"><span data-stu-id="5eb2f-141">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="5eb2f-142">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5eb2f-142">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="5eb2f-143">`/` oder `/Index`</span><span class="sxs-lookup"><span data-stu-id="5eb2f-143">`/` or `/Index`</span></span> |
| <span data-ttu-id="5eb2f-144">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5eb2f-144">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="5eb2f-145">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5eb2f-145">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="5eb2f-146">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5eb2f-146">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="5eb2f-147">`/Store` oder `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="5eb2f-147">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="5eb2f-148">Notizen:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-148">Notes:</span></span>

* <span data-ttu-id="5eb2f-149">Die Runtime sucht standardmäßig im Ordner *Pages* (Seiten) nach Dateien mit Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-149">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="5eb2f-150">Wenn eine Seite nicht in einer URL enthalten ist, ist `Index` die Standardseite.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-150">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="5eb2f-151">Schreiben eines einfachen Formulars</span><span class="sxs-lookup"><span data-stu-id="5eb2f-151">Write a basic form</span></span>

<span data-ttu-id="5eb2f-152">Razor Pages ist darauf ausgelegt, allgemeine Muster, die mit Webbrowsern verwendet werden können, beim Erstellen einer App leichter implementieren zu können.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-152">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="5eb2f-153">Die [Modellbindung](xref:mvc/models/model-binding), [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) und alle HTML-Hilfsprogramme *funktionieren nur* mit den Eigenschaften, die in einer Klasse der Razor Pages definiert wurden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-153">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="5eb2f-154">Nehmen wir z.B. eine Seite, die ein allgemeines Kontaktformular für das `Contact`-Modell implementiert:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-154">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="5eb2f-155">Für die Beispiele in diesem Dokument wird `DbContext` in der Datei [Startup.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) initialisiert.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-155">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="5eb2f-156">Das Datenmodell:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-156">The data model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="5eb2f-157">Der db-Kontext:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-157">The db context:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="5eb2f-158">Die Umgebungsdatei *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-158">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="5eb2f-159">Das Seitenmodell *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-159">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="5eb2f-160">Die `PageModel` -Klasse heißt standardmäßig `<PageName>Model` und befindet sich im selben Namespace wie die Seite.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-160">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="5eb2f-161">Mit der Klasse `PageModel` kann die Logik einer Seite von deren Darstellung getrennt werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-161">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="5eb2f-162">Sie definiert Seitenhandler für Anforderungen, die an die Seite geschickt wurden, und für zum Rendern der Seite verwendete Daten.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-162">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="5eb2f-163">Diese Trennung ermöglicht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-163">This separation allows:</span></span>

* <span data-ttu-id="5eb2f-164">Verwalten von Seitenabhängigkeiten mithilfe von [Dependency Injection](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="5eb2f-164">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="5eb2f-165">Komponententests</span><span class="sxs-lookup"><span data-stu-id="5eb2f-165">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="5eb2f-166">Die Seite hat eine `OnPostAsync`-*Handlermethode*, die auf `POST`-Anforderungen ausgeführt wird (wenn ein Benutzer das Formular sendet).</span><span class="sxs-lookup"><span data-stu-id="5eb2f-166">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="5eb2f-167">Für alle HTTP-Verben können Handlermethoden hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-167">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="5eb2f-168">Die am häufigsten verwendeten Handler sind:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-168">The most common handlers are:</span></span>

* <span data-ttu-id="5eb2f-169">`OnGet`, um den für eine Seite erforderlichen Status zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-169">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="5eb2f-170">Im vorangehenden Code wird die Razor-Seite *CreateModel.cshtml* durch die `OnGet`-Methode dargestellt.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-170">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="5eb2f-171">`OnPost`, um Formularübermittlungen zu behandeln</span><span class="sxs-lookup"><span data-stu-id="5eb2f-171">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="5eb2f-172">Das Namenssuffix `Async` ist optional. Es wird jedoch standardmäßig häufig für asynchrone Funktionen verwendet.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-172">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="5eb2f-173">Der vorhergehende Code ist typisch für Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-173">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="5eb2f-174">Wenn Sie mit ASP.NET-Apps vertraut sind, die Controller und Ansichten verwenden, werden Ihnen folgende Fakten bekannt vorkommen:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-174">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="5eb2f-175">Der `OnPostAsync`-Code im vorangehenden Beispiel ähnelt dem typischen Controllercode.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-175">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="5eb2f-176">Die meisten primitiven MVC-Typen wie solche für [Modellbindungen](xref:mvc/models/model-binding), [Validierungen](xref:mvc/models/validation) und Aktionsergebnisse werden in Controllern und Razor Pages auf dieselbe Weise eingesetzt.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-176">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="5eb2f-177">Die vorherige `OnPostAsync`-Methode:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-177">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="5eb2f-178">Der grundlegende Ablauf von `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-178">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="5eb2f-179">Prüfen auf Validierungsfehler</span><span class="sxs-lookup"><span data-stu-id="5eb2f-179">Check for validation errors.</span></span>

* <span data-ttu-id="5eb2f-180">Wenn keine Fehler vorliegen, werden die Daten gespeichert und weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-180">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="5eb2f-181">Wenn es Fehler gibt, zeigen Sie die Seite erneut mit den Validierungsmeldungen an.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-181">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="5eb2f-182">denn Validierungsfehler werden oftmals auf dem Client erkannt und nie an den Server übermittelt.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-182">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="5eb2f-183">Die Umgebungsdatei *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-183">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="5eb2f-184">Der von *Pages/Create.cshtml* gerenderte HTML-Code sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-184">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="5eb2f-185">Im vorherigen Code gilt für die Formularübermittlung mittels POST Folgendes:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-185">In the previous code, posting the form:</span></span>

* <span data-ttu-id="5eb2f-186">Bei gültigen Daten:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-186">With valid data:</span></span>

  * <span data-ttu-id="5eb2f-187">Die `OnPostAsync`-Handlermethode ruft die <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*>-Hilfsmethode auf.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-187">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="5eb2f-188">`RedirectToPage` gibt eine Instanz von <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult> zurück.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-188">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="5eb2f-189">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-189">`RedirectToPage`:</span></span>

    * <span data-ttu-id="5eb2f-190">ist ein Aktionsergebnis.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-190">Is an action result.</span></span>
    * <span data-ttu-id="5eb2f-191">ähnelt `RedirectToAction` oder `RedirectToRoute` (wird in Controllern und Ansichten verwendet).</span><span class="sxs-lookup"><span data-stu-id="5eb2f-191">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="5eb2f-192">ist an Seiten angepasst.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-192">Is customized for pages.</span></span> <span data-ttu-id="5eb2f-193">Im vorhergehenden Beispiel leitet es an die Stammindexseite (`/Index`) weiter.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-193">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="5eb2f-194">Informationen zu `RedirectToPage` finden Sie im Abschnitt [URL-Generierung für Seiten](#url_gen).</span><span class="sxs-lookup"><span data-stu-id="5eb2f-194">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="5eb2f-195">Bei Validierungsfehlern, die an den Server übermittelt werden:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-195">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="5eb2f-196">Die `OnPostAsync`-Handlermethode ruft die <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*>-Hilfsmethode auf.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-196">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="5eb2f-197">`Page` gibt eine Instanz von <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> zurück.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-197">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="5eb2f-198">Der Vorgang, bei dem `Page` zurückgegeben wird, ähnelt dem Vorgang, bei dem Aktionen im Controller `View` zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-198">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="5eb2f-199">`PageResult` ist der Standardrückgabetyp für eine Handlermethode.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-199">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="5eb2f-200">Eine Handlermethode, die `void` zurückgibt, rendert die Seite.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-200">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="5eb2f-201">Wenn im vorangehenden Beispiel das Formular mithilfe von POST übermittelt und dabei kein Wert angegeben wird, gibt [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) „false“ zurück.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-201">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="5eb2f-202">In diesem Beispiel werden keine Validierungsfehler auf dem Client angezeigt.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-202">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="5eb2f-203">Die Verarbeitung von Validierungsfehlern wird weiter unten in diesem Artikel behandelt.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-203">Validation error handing is covered later in this document.</span></span>

  [!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="5eb2f-204">Bei Validierungsfehlern, die durch eine clientseitige Validierung erkannt werden:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-204">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="5eb2f-205">Die Daten werden **nicht** per POST an den Server gesendet.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-205">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="5eb2f-206">Die clientseitige Validierung wird weiter unten in diesem Artikel erläutert.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-206">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="5eb2f-207">Die Eigenschaft `Customer` verwendet das [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute)-Attribut, um die Modellbindung zu aktivieren:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-207">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="5eb2f-208">`[BindProperty]` sollte **nicht** in Modellen mit Eigenschaften verwendet werden, die vom Client nicht geändert werden dürfen.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-208">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="5eb2f-209">Weitere Informationen finden Sie unter [Overposting](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="5eb2f-209">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="5eb2f-210">Razor Pages binden Eigenschaften standardmäßig nur an Nicht-`GET`-Verben.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-210">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="5eb2f-211">Durch die Bindung an Eigenschaften entfällt das Schreiben von Code, mit dem HTTP-Daten in den Modelltyp konvertiert werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-211">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="5eb2f-212">Die Bindung reduziert den Code mithilfe der gleichen Eigenschaft, um Formularfelder (`<input asp-for="Customer.Name">`) zu rendern und die Eingabe zu akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-212">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="5eb2f-213">Sehen Sie sich die Ansichtsdatei *Pages/Create.cshtml* an:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-213">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="5eb2f-214">Im vorangehenden Code bindet das [Eingabetag-Hilfsprogramm](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` das HTML-Element `<input>` an den Modellausdruck `Customer.Name`.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-214">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="5eb2f-215">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) stellt Taghilfsprogramme zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-215">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="5eb2f-216">Auf der Startseite</span><span class="sxs-lookup"><span data-stu-id="5eb2f-216">The home page</span></span>

<span data-ttu-id="5eb2f-217">*Index.cshtml* ist die Homepage:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-217">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="5eb2f-218">Die zugeordnete `PageModel`-Klasse (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="5eb2f-218">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="5eb2f-219">Die Datei *Index.cshtml* enthält das folgende Markup:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-219">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="5eb2f-220">Das `<a /a>` [Anchor-Taghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) verwendet das `asp-route-{value}`-Attribut, um einen Link zur Seite „Edit“ (Bearbeiten) zu generieren.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-220">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="5eb2f-221">Der Link enthält die Routendaten mit der Kontakt-ID.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-221">The link contains route data with the contact ID.</span></span> <span data-ttu-id="5eb2f-222">Beispielsweise `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-222">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="5eb2f-223">[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) ermöglichen serverseitigem Code das Mitwirken am Erstellen und Rendern von HTML-Elementen in Razor-Dateien.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-223">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="5eb2f-224">Die Datei *Index.cshtml* enthält das Markup zum Erstellen der Schaltfläche „delete“ (Löschen) für jeden Kundenkontakt:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-224">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="5eb2f-225">Der gerenderte HTML-Code sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-225">The rendered HTML:</span></span>

```HTML
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="5eb2f-226">Wenn die „delete“-Schaltfläche in HTML gerendert wird, enthält das zugehörige [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction)-Element Parameter für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-226">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="5eb2f-227">die Kundenkontakt-ID, die durch das `asp-route-id`-Attribut angegeben wird</span><span class="sxs-lookup"><span data-stu-id="5eb2f-227">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="5eb2f-228">den `handler`, der durch das `asp-page-handler`-Attribut angegeben wird</span><span class="sxs-lookup"><span data-stu-id="5eb2f-228">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="5eb2f-229">Wenn die Schaltfläche ausgewählt wird, wird eine `POST`-Anforderung an den Server gesendet.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-229">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="5eb2f-230">Durch Konvention wird der Name der Handlermethode auf Grundlage des Werts des `handler`-Parameters gemäß dem Schema `OnPost[handler]Async` ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-230">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="5eb2f-231">Da der `handler` in diesem Beispiel `delete` ist, wird die Handlermethode `OnPostDeleteAsync` verwendet, um die `POST`-Anforderung zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-231">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="5eb2f-232">Wenn `asp-page-handler` auf einen anderen Wert (z. B. `remove`) festgelegt wird, wird eine Handlermethode namens `OnPostRemoveAsync` ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-232">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="5eb2f-233">Die `OnPostDeleteAsync`-Methode:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-233">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="5eb2f-234">ruft die `id` der Abfragezeichenfolge ab.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-234">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="5eb2f-235">Fragt mit `FindAsync` die Datenbank nach dem Kundenkontakt ab.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-235">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="5eb2f-236">Wenn der Kundenkontakt gefunden wird, wird er entfernt, und die Datenbank wird aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-236">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="5eb2f-237">Ruft <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> auf, um die Stammindexseite (`/Index`) umzuleiten.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-237">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="5eb2f-238">Die Datei „Edit.cshtml“</span><span class="sxs-lookup"><span data-stu-id="5eb2f-238">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="5eb2f-239">Die erste Zeile enthält die `@page "{id:int}"`-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-239">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="5eb2f-240">Die Routingbeschränkung `"{id:int}"` weist die Seite an, die Anforderungen für die Seite zu akzeptieren, die `int`-Routingdaten enthalten.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-240">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="5eb2f-241">Wenn eine Anforderung an die Seite bestimmte Routingdaten nicht enthält, die in einen `int` konvertiert werden können, gibt die Runtime einen Fehler vom Typ „HTTP 404: Nicht gefunden“ zurück.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-241">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="5eb2f-242">Um die ID optional zu machen, fügen Sie `?` an die Routeneinschränkung an:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-242">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="5eb2f-243">Die Datei *Edit.cshtml.cs* sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-243">The *Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="5eb2f-244">Validierung</span><span class="sxs-lookup"><span data-stu-id="5eb2f-244">Validation</span></span>

<span data-ttu-id="5eb2f-245">Für Validierungsregeln gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-245">Validation rules:</span></span>

* <span data-ttu-id="5eb2f-246">Sie werden deklarativ in der Modellklasse angegeben.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-246">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="5eb2f-247">Sie werden überall in der App erzwungen.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-247">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="5eb2f-248">Der Namespace <xref:System.ComponentModel.DataAnnotations> stellt eine Gruppe integrierter Validierungsattribute bereit, die deklarativ auf eine Klasse oder Eigenschaft angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-248">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="5eb2f-249">„DataAnnotations“ enthält auch Formatierungsattribute wie [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute), die bei der Formatierung helfen und keinerlei Validierung bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-249">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="5eb2f-250">Sehen Sie sich das `Customer`-Modell an:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-250">Consider the `Customer` model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="5eb2f-251">Für die folgende Ansichtsdatei *Create.cshtml* gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-251">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="5eb2f-252">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-252">The preceding code:</span></span>

* <span data-ttu-id="5eb2f-253">umfasst jQuery-Skripts einschließlich solcher zur Validierung.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-253">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="5eb2f-254">verwendet die [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) `<div />` und `<span />` zur Aktivierung von Folgendem:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-254">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="5eb2f-255">clientseitiger Validierung.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-255">Client-side validation.</span></span>
  * <span data-ttu-id="5eb2f-256">Rendering von Validierungsfehlern.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-256">Validation error rendering.</span></span>

* <span data-ttu-id="5eb2f-257">wird der folgende HTML-Code generiert:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-257">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="5eb2f-258">Wenn Sie das Formular „Create“ (Erstellen) ohne einen Wert für den Namen mit POST übermitteln, wird die Fehlermeldung „The Name field is required.“ (Für das Namensfeld muss ein Wert angegeben werden.)</span><span class="sxs-lookup"><span data-stu-id="5eb2f-258">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="5eb2f-259">auf dem Formular angezeigt.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-259">on the form.</span></span> <span data-ttu-id="5eb2f-260">Wenn JavaScript auf dem Client aktiviert ist, zeigt der Browser den Fehler an, ohne dass Daten per POST an den Server gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-260">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="5eb2f-261">Das `[StringLength(10)]`-Attribut generiert `data-val-length-max="10"` für den gerenderten HTML-Code.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-261">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="5eb2f-262">`data-val-length-max` verhindert, dass im Browser ein Wert eingegeben wird, der die angegebene Maximallänge überschreitet.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-262">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="5eb2f-263">Wenn ein Tool wie [Fiddler](https://www.telerik.com/fiddler) zum Bearbeiten und erneuten Senden einer POST-Anforderung verwendet wird</span><span class="sxs-lookup"><span data-stu-id="5eb2f-263">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="5eb2f-264">und die Länge des Namens 10 Zeichen überschreitet,</span><span class="sxs-lookup"><span data-stu-id="5eb2f-264">With the name longer than 10.</span></span>
* <span data-ttu-id="5eb2f-265">geschieht Folgendes: Die Fehlermeldung „The field Name must be a string with a maximum length of 10.“ (Das Namensfeld muss eine Zeichenfolge mit maximal 10 Zeichen enthalten.)</span><span class="sxs-lookup"><span data-stu-id="5eb2f-265">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="5eb2f-266">wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-266">is returned.</span></span>

<span data-ttu-id="5eb2f-267">Sehen Sie sich das folgende `Movie`-Modell an:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-267">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="5eb2f-268">Die Validierungsattribute geben das Verhalten an, das für die Modelleigenschaften erzwungen werden soll:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-268">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="5eb2f-269">Die Attribute `Required` und `MinimumLength` geben an, dass eine Eigenschaft einen Wert haben muss. Ein Benutzer kann allerdings ein Leerzeichen eingeben, um diese Anforderung zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-269">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="5eb2f-270">Das Attribut `RegularExpression` wird verwendet, um einzuschränken, welche Zeichen eingegeben werden dürfen.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-270">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="5eb2f-271">Für „Genre“ im Code oben gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-271">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="5eb2f-272">Es dürfen nur Buchstaben enthalten sein.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-272">Must only use letters.</span></span>
  * <span data-ttu-id="5eb2f-273">Der erste Buchstabe muss ein Großbuchstabe sein.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-273">The first letter is required to be uppercase.</span></span> <span data-ttu-id="5eb2f-274">Leerzeichen, Zahlen und Sonderzeichen sind nicht zulässig.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-274">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="5eb2f-275">Für `RegularExpression`-„Rating“ (Bewertung) gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-275">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="5eb2f-276">Das erste Zeichen muss ein Großbuchstabe sein.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-276">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="5eb2f-277">Sonderzeichen und Zahlen sind als darauffolgende Zeichen zulässig.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-277">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="5eb2f-278">„PG-13“ ist als Bewertung („Rating“) gültig, nicht jedoch als „Genre“.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-278">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="5eb2f-279">Das Attribut `Range` schränkt einen Wert auf einen bestimmten Bereich ein.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-279">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="5eb2f-280">Mit dem Attribut `StringLength` kann die maximale Länge einer Zeichenfolgeneigenschaft und optional die minimale Länge festlegt werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-280">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="5eb2f-281">Werttypen (wie `decimal`, `int`, `float`, `DateTime`) sind grundsätzlich erforderlich und benötigen nicht das Attribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-281">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="5eb2f-282">Auf der Seite „Create“ (Erstellen) für das `Movie`-Modell werden ungültige Werte und die daraus resultierenden Fehler angezeigt:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-282">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![Ansichtsformular „Movie“ mit mehreren clientseitigen jQuery-Validierungsfehlern](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="5eb2f-284">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-284">For more information, see:</span></span>

* [<span data-ttu-id="5eb2f-285">Hinzufügen von Validierungscode zur Movie-App</span><span class="sxs-lookup"><span data-stu-id="5eb2f-285">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="5eb2f-286">[Modellvalidierung in ASP.NET Core](xref:mvc/models/validation)</span><span class="sxs-lookup"><span data-stu-id="5eb2f-286">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="5eb2f-287">Verarbeiten von HEAD-Anforderungen mit einem OnGet-Handlerfallback</span><span class="sxs-lookup"><span data-stu-id="5eb2f-287">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="5eb2f-288">`HEAD`-Anforderungen ermöglichen das Abrufen der Header für eine bestimmte Ressource.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-288">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="5eb2f-289">Im Gegensatz zu `GET`-Anforderungen geben `HEAD`-Anforderungen keinen Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-289">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="5eb2f-290">Normalerweise wird ein `OnHead`-Handler erstellt und für `HEAD`-Anforderungen aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-290">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="5eb2f-291">Razor Pages ruft den `OnGet`-Handler auf, wenn kein `OnHead`-Handler definiert ist.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-291">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="5eb2f-292">XSRF/CSRF und Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5eb2f-292">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="5eb2f-293">Razor Pages wird durch [Validierungsmaßnahmen vor XSRF/CSRF-Angriffen geschützt](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="5eb2f-293">Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="5eb2f-294">Das [Formulartag-Hilfsprogramm](xref:mvc/views/working-with-forms#the-form-tag-helper) injiziert Anti-XSRF/CSRF-Token in HTML-Formularelemente.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-294">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="5eb2f-295">Verwenden von Layouts, Teilansichten, Vorlagen und Taghilfsprogrammen mit Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5eb2f-295">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="5eb2f-296">Razor Pages beinhaltet alle Funktionen der Razor-Anzeige-Engine.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-296">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="5eb2f-297">Layouts, Teilansichten, Vorlagen, Taghilfsprogramme, *_ViewStart.cshtml* und *_ViewImports.cshtml* funktionieren auf die gleiche Weise wie für herkömmliche Razor-Ansichten.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-297">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="5eb2f-298">Strukturieren Sie diese Seite mit einigen dieser praktischen Funktionen.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-298">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="5eb2f-299">Fügen Sie der *Pages/Shared/_Layout.cshtml* eine [Layoutseite](xref:mvc/views/layout) hinzu:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-299">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="5eb2f-300">Das [Layout](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="5eb2f-300">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="5eb2f-301">Steuert das Layout der einzelnen Seiten, es sei denn, das Layout wird für eine Seite deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-301">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="5eb2f-302">Importiert HTML-Strukturen, z.B. JavaScript und Stylesheets.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-302">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="5eb2f-303">Der Inhalt der Razor-Seite wird gerendert, wenn `@RenderBody()` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-303">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="5eb2f-304">Weitere Informationen finden Sie unter [Layoutseite](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="5eb2f-304">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="5eb2f-305">Die Eigenschaft [Layout](xref:mvc/views/layout#specifying-a-layout) wird in *Pages/_ViewStart.cshtml* festgelegt:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-305">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="5eb2f-306">Das Layout befindet sich im Ordner *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-306">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="5eb2f-307">Seiten suchen hierarchisch nach anderen Ansichten (Layouts, Vorlagen oder Teilansichten) und beginnen im gleichen Ordner wie die aktuelle Seite.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-307">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="5eb2f-308">Ein Layout im Ordner *Pages/Shared* kann von jeder Razor-Seite aus unter dem Ordner *Pages* verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-308">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="5eb2f-309">Die Layoutdatei sollte im Ordner *Pages/Shared* gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-309">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="5eb2f-310">Wir empfehlen Ihnen, die Layoutdatei **nicht** im Ordner *Views/Shared* (Ansichten/Freigegeben) zu platzieren.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-310">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="5eb2f-311">*Views/Shared* ist ein MVC-Ansichtsmuster.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-311">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="5eb2f-312">Razor Pages basieren auf der Ordnerhierarchie, nicht auf Pfadkonventionen.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-312">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="5eb2f-313">Die Ansichtensuche in einer Razor Page enthält den Ordner *Pages*.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-313">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="5eb2f-314">Die Layouts, Vorlagen und Teilansichten, die mit MVC-Controllern und herkömmlichen Razor-Ansichten verwendet werden, *funktionieren problemlos*.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-314">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="5eb2f-315">Fügen Sie eine Datei *Pages/_ViewImports.cshtml* hinzu:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-315">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="5eb2f-316">`@namespace` wird weiter unten im Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-316">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="5eb2f-317">Die `@addTagHelper`-Anweisung bringt die [integrierten Taghilfsprogramme](xref:mvc/views/tag-helpers/builtin-th/Index) zu allen Seiten in der Ordner *Pages*.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-317">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="5eb2f-318">Die `@namespace`-Anweisung wird wie folgt für eine Seite festgelegt:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-318">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="5eb2f-319">Die `@namespace`-Anweisung legt den Namespace für die Seite fest.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-319">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="5eb2f-320">Die `@model`-Anweisung muss den Namespace nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-320">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="5eb2f-321">Wenn sich die `@namespace`-Anweisung in *_ViewImports.cshtml* befindet, stellt der angegebene Namespace das Präfix für den generierten Namespace auf der Seite bereit, die die `@namespace`-Anweisung importiert.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-321">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="5eb2f-322">Der Rest der generierten Namespaces (der Suffixteil) ist der durch Punkte getrennte relative Pfad zwischen dem Ordner mit *_ViewImports.cshtml* und dem Ordner, der die Seite enthält.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-322">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="5eb2f-323">Die `PageModel`-Klasse in *Pages/Customers/Edit.cshtml.cs* legt den Namespace z.B. explizit fest:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-323">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="5eb2f-324">Die Datei *Pages/_ViewImports.cshtml* legt den folgenden Namespace fest:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-324">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="5eb2f-325">Der generierte Namespace für die Razor Page *Pages/Customers/Edit.cshtml* ist identisch mit der `PageModel`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-325">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="5eb2f-326">`@namespace` *funktioniert auch mit konventionellen Razor-Ansichten.*</span><span class="sxs-lookup"><span data-stu-id="5eb2f-326">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="5eb2f-327">Sehen Sie sich die Ansichtsdatei *Pages/Create.cshtml* an:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-327">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="5eb2f-328">Die aktualisierte Ansichtsdatei *Pages/Create.cshtml* mit *_ViewImports.cshtml* und der vorherigen Layoutdatei sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-328">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="5eb2f-329">Im vorangehenden Code werden von *_ViewImports.cshtml* der Namespace und die Taghilfsprogramme importiert.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-329">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="5eb2f-330">Die JavaScript-Dateien werden von der Layoutdatei importiert.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-330">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="5eb2f-331">Die [Razor Pages-Startprojekt](#rpvs17) enthält die Seite *Pages/_ValidationScriptsPartial.cshtml*, die die clientseitige Validierung bindet.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-331">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="5eb2f-332">Weitere Informationen zu Teilansichten finden Sie unter <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-332">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="5eb2f-333">URL-Generierung für Seiten</span><span class="sxs-lookup"><span data-stu-id="5eb2f-333">URL generation for Pages</span></span>

<span data-ttu-id="5eb2f-334">Die zuvor gezeigte `Create`-Seite verwendet `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-334">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="5eb2f-335">Die App hat die folgende Datei/Ordner-Struktur:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-335">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="5eb2f-336">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="5eb2f-336">*/Pages*</span></span>

  * <span data-ttu-id="5eb2f-337">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5eb2f-337">*Index.cshtml*</span></span>
  * <span data-ttu-id="5eb2f-338">*Privacy.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5eb2f-338">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="5eb2f-339">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="5eb2f-339">*/Customers*</span></span>

    * <span data-ttu-id="5eb2f-340">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5eb2f-340">*Create.cshtml*</span></span>
    * <span data-ttu-id="5eb2f-341">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5eb2f-341">*Edit.cshtml*</span></span>
    * <span data-ttu-id="5eb2f-342">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5eb2f-342">*Index.cshtml*</span></span>

<span data-ttu-id="5eb2f-343">Die Seiten *Pages/Customers/Create.cshtml* und *Pages/Customers/Edit.cshtml* führen bei Erfolg eine Umleitung zu *Pages/Customers/Index.cshtml* durch.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-343">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="5eb2f-344">Die Zeichenfolge `./Index` stellt einen relativen Seitennamen dar, der für den Zugriff auf die vorherige Seite verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-344">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="5eb2f-345">Sie wird für das Generieren von URIs für die Seite *Pages/Customers/Index.cshtml* verwendet.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-345">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="5eb2f-346">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-346">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="5eb2f-347">Der absolute Seitenname `/Index` wird zum Generieren der URLs für die Seite *Pages/Index.cshtml* verwendet.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-347">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="5eb2f-348">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-348">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="5eb2f-349">Der Seitenname ist der Pfad zu der Seite vom Stammordner */Pages* (einschließlich eines vorangestellten `/`, z.B. `/Index`).</span><span class="sxs-lookup"><span data-stu-id="5eb2f-349">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="5eb2f-350">Die oben stehenden Beispiele für eine URL-Generierung bieten erweiterte Optionen und Funktionen, durch die Sie URLs nicht mehr hartcodieren müssen.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-350">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="5eb2f-351">Bei der URL-Generierung wird [Routing](xref:mvc/controllers/routing) verwendet. Außerdem können damit Parameter generiert und entsprechend der Definition der Route im Zielpfad codiert werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-351">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="5eb2f-352">Die URL-Generierung für Seiten unterstützt relative Namen.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-352">URL generation for pages supports relative names.</span></span> <span data-ttu-id="5eb2f-353">In der folgenden Tabelle wird dargestellt, welche Indexseite durch verschiedene `RedirectToPage`-Parameter in *Pages/Customers/Create.cshtml* ausgewählt wird.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-353">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="5eb2f-354">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="5eb2f-354">RedirectToPage(x)</span></span>| <span data-ttu-id="5eb2f-355">Seite</span><span class="sxs-lookup"><span data-stu-id="5eb2f-355">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="5eb2f-356">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="5eb2f-356">RedirectToPage("/Index")</span></span> | <span data-ttu-id="5eb2f-357">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="5eb2f-357">*Pages/Index*</span></span> |
| <span data-ttu-id="5eb2f-358">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="5eb2f-358">RedirectToPage("./Index");</span></span> | <span data-ttu-id="5eb2f-359">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="5eb2f-359">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="5eb2f-360">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="5eb2f-360">RedirectToPage("../Index")</span></span> | <span data-ttu-id="5eb2f-361">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="5eb2f-361">*Pages/Index*</span></span> |
| <span data-ttu-id="5eb2f-362">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="5eb2f-362">RedirectToPage("Index")</span></span>  | <span data-ttu-id="5eb2f-363">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="5eb2f-363">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="5eb2f-364">`RedirectToPage("Index")`, `RedirectToPage("./Index")` und `RedirectToPage("../Index")` sind *relative Namen*.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-364">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="5eb2f-365">Der `RedirectToPage`-Parameter wird mit dem Pfad der aktuellen Seite *kombiniert*, um den Namen der Zielseite zu berechnen.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-365">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="5eb2f-366">Das Verknüpfen relativer Namen eignet sich beim Erstellen von Websites mit einer komplexen Struktur.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-366">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="5eb2f-367">Wenn durch relative Namen Seiten in einem Ordner verknüpft werden, hat das folgende Vorteile:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-367">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="5eb2f-368">Relative Links funktionieren weiterhin, wenn ein Ordner umbenannt wird.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-368">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="5eb2f-369">Links funktionieren weiterhin, da sie keinen Ordnernamen enthalten.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-369">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="5eb2f-370">Um auf eine Seite in einem anderen [Bereich](xref:mvc/controllers/areas) umzuleiten, geben Sie den Bereich an:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-370">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="5eb2f-371">Weitere Informationen finden Sie unter <xref:mvc/controllers/areas> und <xref:razor-pages/razor-pages-conventions>.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-371">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="5eb2f-372">Attribut „ViewData“</span><span class="sxs-lookup"><span data-stu-id="5eb2f-372">ViewData attribute</span></span>

<span data-ttu-id="5eb2f-373">Daten können mit <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute> an eine Seite übermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-373">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="5eb2f-374">Für Eigenschaften mit dem `[ViewData]`-Attribut werden die Werte in <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> gespeichert und daraus geladen.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-374">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="5eb2f-375">Im folgenden Beispiel wendet `AboutModel` das `[ViewData]`-Attribut auf die `Title`-Eigenschaft an:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-375">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="5eb2f-376">Greifen Sie auf der Infoseite auf die Eigenschaft `Title` als Modelleigenschaft zu:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-376">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="5eb2f-377">Im Layout wird der Titel aus dem ViewData-Wörterbuch gelesen:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-377">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="5eb2f-378">TempData</span><span class="sxs-lookup"><span data-stu-id="5eb2f-378">TempData</span></span>

<span data-ttu-id="5eb2f-379">ASP.NET Core macht <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> verfügbar.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-379">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="5eb2f-380">Diese Eigenschaft speichert Daten, bis sie gelesen wurden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-380">This property stores data until it's read.</span></span> <span data-ttu-id="5eb2f-381">Die Methoden <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> und <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> können verwendet werden, um die Daten zu überprüfen, ohne sie zu löschen.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-381">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="5eb2f-382">`TempData` eignet sich für die Umleitung, wenn Daten für mehr als eine Anforderung benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-382">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="5eb2f-383">Im folgenden Code wird der Wert von `Message` mit `TempData` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-383">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="5eb2f-384">Das folgende Markup in der Datei *Pages/Customers/Index.cshtml* zeigt den Wert von `Message` mit `TempData` an.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-384">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="5eb2f-385">Das Seitenmodell *Pages/Customers/Index.cshtml.cs* wendet das `[TempData]`-Attribut auf die Eigenschaft `Message` an.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-385">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```cs
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="5eb2f-386">Weitere Informationen finden Sie unter [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="5eb2f-386">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="5eb2f-387">Mehrere Handler pro Seite</span><span class="sxs-lookup"><span data-stu-id="5eb2f-387">Multiple handlers per page</span></span>

<span data-ttu-id="5eb2f-388">Die folgende Seite generiert mit dem `asp-page-handler`-Taghilfsprogramm Markup für zwei Handler:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-388">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="5eb2f-389">Das Formular im vorherigen Beispiel hat zwei Sendeschaltflächen, und jede verwendet `FormActionTagHelper`, um an eine andere URL zu übermitteln.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-389">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="5eb2f-390">Das `asp-page-handler`-Attribut ist eine Ergänzung für `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-390">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="5eb2f-391">`asp-page-handler` generiert URLs, die als Übermittlungsziel jeweils die durch eine Seite festgelegte Handlermethode verwenden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-391">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="5eb2f-392">`asp-page` wird nicht angegeben, weil das Beispiel mit der aktuellen Seite verknüpft.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-392">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="5eb2f-393">Das Seitenmodell:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-393">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="5eb2f-394">Der vorherige Code verwendet *benannte Handlermethoden*.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-394">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="5eb2f-395">Benannte Handlermethoden werden aus dem Text im Namen nach `On<HTTP Verb>` und vor `Async` (falls vorhanden) erstellt.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-395">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="5eb2f-396">Im vorherigen Beispiel sind OnPost**JoinList**Async und OnPost**JoinListUC**Async die Seitenmethoden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-396">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="5eb2f-397">Wenn Sie *OnPost* und *Async* entfernen, lauten die Handlernamen `JoinList` und `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-397">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="5eb2f-398">Mit dem vorherigen Code lautet der URL-Pfad, der an `OnPostJoinListAsync` übermittelt, `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-398">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="5eb2f-399">Der URL-Pfad, der an `OnPostJoinListUCAsync` übermittelt, lautet `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-399">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="5eb2f-400">Benutzerdefinierte Routen</span><span class="sxs-lookup"><span data-stu-id="5eb2f-400">Custom routes</span></span>

<span data-ttu-id="5eb2f-401">Verwenden Sie die `@page`-Anweisung für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-401">Use the `@page` directive to:</span></span>

* <span data-ttu-id="5eb2f-402">Das Angeben einer benutzerdefinierten Route zu einer Seite.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-402">Specify a custom route to a page.</span></span> <span data-ttu-id="5eb2f-403">Die Route zur Seite „Info“ kann mit `@page "/Some/Other/Path"` beispielsweise auf `/Some/Other/Path` festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-403">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="5eb2f-404">Das Anfügen von Segmenten an die Standardroute einer Seite.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-404">Append segments to a page's default route.</span></span> <span data-ttu-id="5eb2f-405">Mit `@page "item"` kann beispielsweise ein item-Segment an die Standardroute der Seite angefügt werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-405">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="5eb2f-406">Das Anfügen von Parametern an die Standardroute einer Seite.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-406">Append parameters to a page's default route.</span></span> <span data-ttu-id="5eb2f-407">Mit `@page "{id}"` kann beispielsweise ein ID-Parameter (`id`) für eine Seite angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-407">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="5eb2f-408">Es wird ein relativer Pfad zum Stamm unterstützt, der durch eine Tilde (`~`) festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-408">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="5eb2f-409">`@page "~/Some/Other/Path"` entspricht beispielsweise `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-409">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="5eb2f-410">Sie können die Abfragezeichenfolge `?handler=JoinList` in der URL in ein Routensegment (`/JoinList`) ändern, indem Sie die Routenvorlage `@page "{handler?}"` angeben.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-410">You can change the query string `?handler=JoinList` in the URL to a route segment `/JoinList` by specifying the route template `@page "{handler?}"`.</span></span>

<span data-ttu-id="5eb2f-411">Wenn Sie nicht möchten, dass die Abfragezeichenfolge `?handler=JoinList` in der URL enthalten ist, können Sie die Route ändern, damit der Handlername im Pfadteil der URL eingefügt wird.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-411">If you don't like the query string `?handler=JoinList` in the URL, you can change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="5eb2f-412">Sie können die Route anpassen, indem Sie eine Routenvorlage in doppelten Anführungszeichen nach der `@page`-Anweisung hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-412">You can customize the route by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="5eb2f-413">Mit dem vorherigen Code lautet der URL-Pfad, der an `OnPostJoinListAsync` übermittelt, `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-413">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="5eb2f-414">Der URL-Pfad, der an `OnPostJoinListUCAsync` übermittelt, lautet `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-414">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="5eb2f-415">Das `?` nach `handler` bedeutet, dass der Routenparameter optional ist.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-415">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="5eb2f-416">Erweiterte Konfigurationen und Einstellungen</span><span class="sxs-lookup"><span data-stu-id="5eb2f-416">Advanced configuration and settings</span></span>

<span data-ttu-id="5eb2f-417">Die Konfigurationen und Einstellungen in den folgenden Abschnitten sind für die meisten Apps nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-417">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="5eb2f-418">Verwenden Sie die Erweiterungsmethode <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>, um die erweiterten Optionen zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-418">To configure advanced options, use the extension method <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="5eb2f-419">Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>, um das Stammverzeichnis für Seiten festzulegen oder Anwendungsmodellkonventionen für Seiten hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-419">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="5eb2f-420">Weitere Informationen zu Konventionen finden Sie unter [Razor Pages-Autorisierungskonventionen](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="5eb2f-420">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="5eb2f-421">Informationen zum Vorkompilieren von Ansichten finden Sie unter [Razor-Ansichtenkompilierung](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="5eb2f-421">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="5eb2f-422">Festlegen des Inhaltsstammverzeichnisses für Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5eb2f-422">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="5eb2f-423">Standardmäßig lautet das Stammverzeichnis für Razor Pages */Pages*.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-423">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="5eb2f-424">Fügen Sie <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> hinzu, um anzugeben, dass sich Ihre Razor Pages im [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) der App befinden:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-424">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="5eb2f-425">Festlegen eines benutzerdefinierten Stammverzeichnisses für Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5eb2f-425">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="5eb2f-426">Fügen Sie <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> hinzu, um anzugeben, dass sich Ihre Razor-Seiten in einem benutzerdefinierten Stammverzeichnis der App befinden. Geben Sie dabei einen relativen Pfad an:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-426">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="5eb2f-427">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="5eb2f-427">Additional resources</span></span>

* <span data-ttu-id="5eb2f-428">Lesen Sie auch den Artikel [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start), der auf dieser Einführung aufbaut.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-428">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction</span></span>
* [<span data-ttu-id="5eb2f-429">Herunterladen und Anzeigen des Beispielcodes</span><span class="sxs-lookup"><span data-stu-id="5eb2f-429">Download or view sample code</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5eb2f-430">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="5eb2f-430">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="5eb2f-431">Razor Pages ist ein neuer Bestandteil von ASP.NET Core MVC, mit dem codierungsseitige Szenarios einfacher und produktiver werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-431">Razor Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="5eb2f-432">Ein Tutorial, in dem der Model-View-Controller-Ansatz verwendet wird, finden Sie unter [Erste Schritte mit ASP.NET Core MVC und Visual Studio](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="5eb2f-432">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="5eb2f-433">Dieses Dokument bietet eine Einführung in Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-433">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="5eb2f-434">Es handelt sich nicht um ein Schritt-für-Schritt-Tutorial.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-434">It's not a step by step tutorial.</span></span> <span data-ttu-id="5eb2f-435">Wenn es Ihnen Probleme bereitet, die Ausführungen in einigen Abschnitten nachzuvollziehen, lesen Sie [Erste Schritte mit Razor-Seiten in ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="5eb2f-435">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="5eb2f-436">Eine Übersicht über ASP.NET Core finden Sie unter [Einführung in ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="5eb2f-436">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5eb2f-437">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="5eb2f-437">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5eb2f-438">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5eb2f-438">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5eb2f-439">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5eb2f-439">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5eb2f-440">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="5eb2f-440">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="5eb2f-441">Erstellen eines Razor Pages-Projekts</span><span class="sxs-lookup"><span data-stu-id="5eb2f-441">Create a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5eb2f-442">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5eb2f-442">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5eb2f-443">Ausführliche Informationen zum Erstellen eines Razor Pages-Projekts finden Sie unter [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="5eb2f-443">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5eb2f-444">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="5eb2f-444">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5eb2f-445">Führen Sie `dotnet new webapp` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-445">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="5eb2f-446">Öffnen Sie die generierte Datei *.csproj* aus Visual Studio für Mac.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-446">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5eb2f-447">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5eb2f-447">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5eb2f-448">Führen Sie `dotnet new webapp` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-448">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="5eb2f-449">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5eb2f-449">Razor Pages</span></span>

<span data-ttu-id="5eb2f-450">Razor Pages ist in *Startup.cs* aktiviert:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-450">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="5eb2f-451">Sehen Sie sich diese einfache Seite an: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="5eb2f-451">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="5eb2f-452">Der vorherige Code ähnelt sehr einer [Razor-Ansichtsdatei](xref:tutorials/first-mvc-app/adding-view), die in einer ASP.NET Core-App mit Controllern und Ansichten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-452">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="5eb2f-453">Der Unterschied besteht in der `@page`-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-453">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="5eb2f-454">`@page` macht die Datei zu einer MVC-Aktion, d.h. dass Anfragen direkt ohne einen Controller verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-454">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="5eb2f-455">`@page` muss die erste Razor-Anweisung auf einer Seite sein.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-455">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="5eb2f-456">`@page` wirkt sich auf das Verhalten aller anderen Razor-Konstrukte aus.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-456">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="5eb2f-457">Eine ähnliche Seite, die die `PageModel`-Klasse verwendet, wird in den folgenden zwei Dateien angezeigt.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-457">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="5eb2f-458">Die Datei *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-458">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="5eb2f-459">Das Seitenmodell *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-459">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="5eb2f-460">Die `PageModel`-Klassendatei hat standardmäßig den gleichen Namen wie die Datei mit Razor Pages, nur dass außerdem *cs* angefügt wird.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-460">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="5eb2f-461">Die vorherige Datei mit Razor Pages lautet beispielsweise *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-461">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="5eb2f-462">Die Datei mit der `PageModel`-Klasse heißt *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-462">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="5eb2f-463">Die Zuordnungen von URL-Pfaden zu Seiten werden durch den Speicherort der Seite im Dateisystem bestimmt.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-463">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="5eb2f-464">Die folgende Tabelle zeigt einen Pfad zu Razor Pages und die entsprechende URL:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-464">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="5eb2f-465">Dateiname und Pfad</span><span class="sxs-lookup"><span data-stu-id="5eb2f-465">File name and path</span></span>               | <span data-ttu-id="5eb2f-466">Entsprechende URL</span><span class="sxs-lookup"><span data-stu-id="5eb2f-466">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="5eb2f-467">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5eb2f-467">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="5eb2f-468">`/` oder `/Index`</span><span class="sxs-lookup"><span data-stu-id="5eb2f-468">`/` or `/Index`</span></span> |
| <span data-ttu-id="5eb2f-469">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5eb2f-469">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="5eb2f-470">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5eb2f-470">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="5eb2f-471">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5eb2f-471">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="5eb2f-472">`/Store` oder `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="5eb2f-472">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="5eb2f-473">Notizen:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-473">Notes:</span></span>

* <span data-ttu-id="5eb2f-474">Die Runtime sucht standardmäßig im Ordner *Pages* (Seiten) nach Dateien mit Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-474">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="5eb2f-475">Wenn eine Seite nicht in einer URL enthalten ist, ist `Index` die Standardseite.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-475">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="5eb2f-476">Schreiben eines einfachen Formulars</span><span class="sxs-lookup"><span data-stu-id="5eb2f-476">Write a basic form</span></span>

<span data-ttu-id="5eb2f-477">Razor Pages ist darauf ausgelegt, allgemeine Muster, die mit Webbrowsern verwendet werden können, beim Erstellen einer App leichter implementieren zu können.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-477">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="5eb2f-478">Die [Modellbindung](xref:mvc/models/model-binding), [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) und alle HTML-Hilfsprogramme *funktionieren nur* mit den Eigenschaften, die in einer Klasse der Razor Pages definiert wurden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-478">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="5eb2f-479">Nehmen wir z.B. eine Seite, die ein allgemeines Kontaktformular für das `Contact`-Modell implementiert:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-479">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="5eb2f-480">Für die Beispiele in diesem Dokument wird `DbContext` in der Datei [Startup.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) initialisiert.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-480">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="5eb2f-481">Das Datenmodell:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-481">The data model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="5eb2f-482">Der db-Kontext:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-482">The db context:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="5eb2f-483">Die Umgebungsdatei *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-483">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="5eb2f-484">Das Seitenmodell *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-484">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="5eb2f-485">Die `PageModel` -Klasse heißt standardmäßig `<PageName>Model` und befindet sich im selben Namespace wie die Seite.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-485">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="5eb2f-486">Mit der Klasse `PageModel` kann die Logik einer Seite von deren Darstellung getrennt werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-486">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="5eb2f-487">Sie definiert Seitenhandler für Anforderungen, die an die Seite geschickt wurden, und für zum Rendern der Seite verwendete Daten.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-487">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="5eb2f-488">Diese Trennung ermöglicht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-488">This separation allows:</span></span>

* <span data-ttu-id="5eb2f-489">Verwalten von Seitenabhängigkeiten mithilfe von [Dependency Injection](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="5eb2f-489">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="5eb2f-490">[Komponententests](xref:test/razor-pages-tests) für Seiten</span><span class="sxs-lookup"><span data-stu-id="5eb2f-490">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="5eb2f-491">Die Seite hat eine `OnPostAsync`-*Handlermethode*, die auf `POST`-Anforderungen ausgeführt wird (wenn ein Benutzer das Formular sendet).</span><span class="sxs-lookup"><span data-stu-id="5eb2f-491">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="5eb2f-492">Sie können Handlermethoden für alle HTTP-Verben hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-492">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="5eb2f-493">Die am häufigsten verwendeten Handler sind:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-493">The most common handlers are:</span></span>

* <span data-ttu-id="5eb2f-494">`OnGet`, um den für eine Seite erforderlichen Status zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-494">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="5eb2f-495">[OnGet](#OnGet)-Beispiel</span><span class="sxs-lookup"><span data-stu-id="5eb2f-495">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="5eb2f-496">`OnPost`, um Formularübermittlungen zu behandeln</span><span class="sxs-lookup"><span data-stu-id="5eb2f-496">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="5eb2f-497">Das Namenssuffix `Async` ist optional. Es wird jedoch standardmäßig häufig für asynchrone Funktionen verwendet.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-497">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="5eb2f-498">Der vorhergehende Code ist typisch für Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-498">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="5eb2f-499">Wenn Sie mit ASP.NET-Apps vertraut sind, die Controller und Ansichten verwenden, werden Ihnen folgende Fakten bekannt vorkommen:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-499">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="5eb2f-500">Der `OnPostAsync`-Code im vorangehenden Beispiel ähnelt dem typischen Controllercode.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-500">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="5eb2f-501">Die meisten primitiven MVC-Typen wie solche für [Modellbindungen](xref:mvc/models/model-binding), [Validierungen](xref:mvc/models/validation), [Validierungen](xref:mvc/models/validation) und Aktionsergebnisse werden gemeinsam verwendet.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-501">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="5eb2f-502">Die vorherige `OnPostAsync`-Methode:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-502">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="5eb2f-503">Der grundlegende Ablauf von `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-503">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="5eb2f-504">Prüfen auf Validierungsfehler</span><span class="sxs-lookup"><span data-stu-id="5eb2f-504">Check for validation errors.</span></span>

* <span data-ttu-id="5eb2f-505">Wenn keine Fehler vorliegen, werden die Daten gespeichert und weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-505">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="5eb2f-506">Wenn es Fehler gibt, zeigen Sie die Seite erneut mit den Validierungsmeldungen an.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-506">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="5eb2f-507">Die clientseitige Validierung ist identisch mit herkömmlichen ASP.NET Core MVC-Anwendungen,</span><span class="sxs-lookup"><span data-stu-id="5eb2f-507">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="5eb2f-508">denn Validierungsfehler werden oftmals auf dem Client erkannt und nie an den Server übermittelt.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-508">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="5eb2f-509">Wenn die Daten erfolgreich eingegeben wurden, ruft die `OnPostAsync`-Handlermethode die `RedirectToPage`-Hilfsmethode auf, um eine Instanz von `RedirectToPageResult` zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-509">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="5eb2f-510">`RedirectToPage` ist ein neues Aktionsergebnis und ähnelt `RedirectToAction` oder `RedirectToRoute`, ist aber für Seiten angepasst.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-510">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="5eb2f-511">Im vorhergehenden Beispiel leitet es an die Stammindexseite (`/Index`) weiter.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-511">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="5eb2f-512">Informationen zu `RedirectToPage` finden Sie im Abschnitt [URL-Generierung für Seiten](#url_gen).</span><span class="sxs-lookup"><span data-stu-id="5eb2f-512">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="5eb2f-513">Wenn das übermittelte Formular Validierungsfehler enthält (die an den Server übergeben wurden), ruft die `OnPostAsync`-Handlermethode die `Page`-Hilfsmethode auf.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-513">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="5eb2f-514">`Page` gibt eine Instanz von `PageResult` zurück.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-514">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="5eb2f-515">Der Vorgang, bei dem `Page` zurückgegeben wird, ähnelt dem Vorgang, bei dem Aktionen im Controller `View` zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-515">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="5eb2f-516">`PageResult` ist der Standardrückgabetyp für eine Handlermethode.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-516">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="5eb2f-517">Eine Handlermethode, die `void` zurückgibt, rendert die Seite.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-517">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="5eb2f-518">Die Eigenschaft `Customer` verwendet das `[BindProperty]`-Attribut, um die Modellbindung zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-518">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="5eb2f-519">Razor Pages binden Eigenschaften standardmäßig nur an Nicht-`GET`-Verben.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-519">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="5eb2f-520">Durch die Bindung an Eigenschaften können Sie den Umfang von Codes reduzieren, den Sie schreiben müssen.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-520">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="5eb2f-521">Die Bindung reduziert den Code mithilfe der gleichen Eigenschaft, um Formularfelder (`<input asp-for="Customer.Name">`) zu rendern und die Eingabe zu akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-521">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="5eb2f-522">Die Startseite (*Index.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="5eb2f-522">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="5eb2f-523">Die zugeordnete `PageModel`-Klasse (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="5eb2f-523">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="5eb2f-524">Die Datei *Index.cshtml* enthält das folgende Markup, um einen Bearbeitungslink für jeden Kontakt zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-524">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="5eb2f-525">Das `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor-Taghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) verwendet das `asp-route-{value}`-Attribut, um einen Link zur Seite „Edit“ (Bearbeiten) zu generieren.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-525">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="5eb2f-526">Der Link enthält die Routendaten mit der Kontakt-ID.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-526">The link contains route data with the contact ID.</span></span> <span data-ttu-id="5eb2f-527">Beispielsweise `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-527">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="5eb2f-528">[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) ermöglichen serverseitigem Code das Mitwirken am Erstellen und Rendern von HTML-Elementen in Razor-Dateien.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-528">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="5eb2f-529">Taghilfsprogramme werden durch `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` aktiviert.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-529">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="5eb2f-530">Die Datei *Pages/Edit.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-530">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="5eb2f-531">Die erste Zeile enthält die `@page "{id:int}"`-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-531">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="5eb2f-532">Die Routingbeschränkung `"{id:int}"` weist die Seite an, die Anforderungen für die Seite zu akzeptieren, die `int`-Routingdaten enthalten.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-532">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="5eb2f-533">Wenn eine Anforderung an die Seite bestimmte Routingdaten nicht enthält, die in einen `int` konvertiert werden können, gibt die Runtime einen Fehler vom Typ „HTTP 404: Nicht gefunden“ zurück.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-533">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="5eb2f-534">Um die ID optional zu machen, fügen Sie `?` an die Routeneinschränkung an:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-534">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="5eb2f-535">Die Datei *Pages/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-535">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="5eb2f-536">Die Datei *index.cshtml* enthält auch Markup zum Erstellen der Schaltfläche „Löschen“ für jeden benutzerdefinierten Kontakt:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-536">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="5eb2f-537">Wenn die „Löschen“-Schaltfläche in HTML gerendert wird, enthält ihr `formaction`-Element Parameter für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-537">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="5eb2f-538">Die benutzerdefinierte Kontakt-ID, die vom `asp-route-id`-Attribut angegeben wird</span><span class="sxs-lookup"><span data-stu-id="5eb2f-538">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="5eb2f-539">Der `handler`, der vom `asp-page-handler`-Attribut angegeben wird</span><span class="sxs-lookup"><span data-stu-id="5eb2f-539">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="5eb2f-540">Hier sehen Sie ein Beispiel für eine gerenderte „Löschen“-Schaltfläche mit einer benutzerdefinierten Kontakt-ID von `1`:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-540">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="5eb2f-541">Wenn die Schaltfläche ausgewählt wird, wird eine `POST`-Anforderung an den Server gesendet.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-541">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="5eb2f-542">Durch Konvention wird der Name der Handlermethode auf Grundlage des Werts des `handler`-Parameters gemäß dem Schema `OnPost[handler]Async` ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-542">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="5eb2f-543">Da der `handler` in diesem Beispiel `delete` ist, wird die Handlermethode `OnPostDeleteAsync` verwendet, um die `POST`-Anforderung zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-543">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="5eb2f-544">Wenn `asp-page-handler` auf einen anderen Wert (z. B. `remove`) festgelegt wird, wird eine Handlermethode namens `OnPostRemoveAsync` ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-544">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="5eb2f-545">Im folgenden Code wird der `OnPostDeleteAsync`-Handler definiert:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-545">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="5eb2f-546">Die `OnPostDeleteAsync`-Methode:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-546">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="5eb2f-547">Akzeptiert die `id` der Abfragezeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-547">Accepts the `id` from the query string.</span></span> <span data-ttu-id="5eb2f-548">Wenn die Anweisung der Seite *Index.cshtml* die Routingeinschränkung `"{id:int?}"` enthielte, würde `id` aus den Routendaten abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-548">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="5eb2f-549">Die Routendaten für `id` werden beispielsweise wie folgt im URI angegeben: `https://localhost:5001/Customers/2`.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-549">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="5eb2f-550">Fragt mit `FindAsync` die Datenbank nach dem Kundenkontakt ab.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-550">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="5eb2f-551">Wenn der Kundenkontakt gefunden wird, wird er aus der Liste der Kundenkontakte entfernt.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-551">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="5eb2f-552">Die Datenbank wurde aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-552">The database is updated.</span></span>
* <span data-ttu-id="5eb2f-553">Ruft `RedirectToPage` auf, um die Stammindexseite (`/Index`) umzuleiten.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-553">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="5eb2f-554">Markieren von Eigenschaften als „Required“ (Erforderlich)</span><span class="sxs-lookup"><span data-stu-id="5eb2f-554">Mark page properties as required</span></span>

<span data-ttu-id="5eb2f-555">Eigenschaften in einem `PageModel` können als [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) markiert werden:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-555">Properties on a `PageModel` can be decorated with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="5eb2f-556">Weitere Informationen finden Sie unter [Modellvalidierung](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="5eb2f-556">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="5eb2f-557">Verarbeiten von HEAD-Anforderungen mit einem OnGet-Handlerfallback</span><span class="sxs-lookup"><span data-stu-id="5eb2f-557">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="5eb2f-558">`HEAD`-Anforderungen ermöglichen Ihnen das Abrufen des Headers für eine bestimmte Ressource.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-558">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="5eb2f-559">Im Gegensatz zu `GET`-Anforderungen geben `HEAD`-Anforderungen keinen Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-559">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="5eb2f-560">Normalerweise wird ein `OnHead`-Handler erstellt und für `HEAD`-Anforderungen aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-560">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="5eb2f-561">In ASP.NET Core 2.1 oder höher greift Razor Pages auf den `OnGet`-Handler zurück, wenn kein `OnHead`-Handler definiert ist.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-561">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="5eb2f-562">Dieses Verhalten wird durch den Aufruf von [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices` ermöglicht:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-562">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="5eb2f-563">Die Standardvorlagen generieren den `SetCompatibilityVersion`-Aufruf in ASP.NET Core 2.1 und 2.2.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-563">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="5eb2f-564">Tatsächlich setzt `SetCompatibilityVersion` die Razor Pages-Option `AllowMappingHeadRequestsToGetHandler` auf `true`.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-564">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="5eb2f-565">Sie müssen nicht alle Verhalten in `SetCompatibilityVersion` aktivieren, sondern können sich nur *bestimmte* Verhalten aussuchen.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-565">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="5eb2f-566">Der folgende Code gibt an, dass `HEAD`-Anforderungen dem `OnGet`-Handler zugeordnet werden dürfen:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-566">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="5eb2f-567">XSRF/CSRF und Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5eb2f-567">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="5eb2f-568">Sie müssen keinen Code für die [Antifälschungsvalidierung](xref:security/anti-request-forgery) schreiben.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-568">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="5eb2f-569">Die Generierung und Validierung von Antifälschungstoken ist automatisch in Razor Pages enthalten.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-569">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="5eb2f-570">Verwenden von Layouts, Teilansichten, Vorlagen und Taghilfsprogrammen mit Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5eb2f-570">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="5eb2f-571">Razor Pages beinhaltet alle Funktionen der Razor-Anzeige-Engine.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-571">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="5eb2f-572">Layouts, Teilansichten, Vorlagen, Taghilfsprogramme, *_ViewStart.cshtml*, *_ViewImports.cshtml* funktionieren auf die gleiche Weise wie für herkömmliche Razor-Ansichten.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-572">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="5eb2f-573">Strukturieren Sie diese Seite mit einigen dieser praktischen Funktionen.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-573">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="5eb2f-574">Fügen Sie der *Pages/Shared/_Layout.cshtml* eine [Layoutseite](xref:mvc/views/layout) hinzu:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-574">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="5eb2f-575">Das [Layout](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="5eb2f-575">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="5eb2f-576">Steuert das Layout der einzelnen Seiten, es sei denn, das Layout wird für eine Seite deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-576">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="5eb2f-577">Importiert HTML-Strukturen, z.B. JavaScript und Stylesheets.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-577">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="5eb2f-578">Weitere Informationen finden Sie unter [Layoutseite](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="5eb2f-578">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="5eb2f-579">Die Eigenschaft [Layout](xref:mvc/views/layout#specifying-a-layout) wird in *Pages/_ViewStart.cshtml* festgelegt:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-579">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="5eb2f-580">Das Layout befindet sich im Ordner *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-580">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="5eb2f-581">Seiten suchen hierarchisch nach anderen Ansichten (Layouts, Vorlagen oder Teilansichten) und beginnen im gleichen Ordner wie die aktuelle Seite.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-581">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="5eb2f-582">Ein Layout im Ordner *Pages/Shared* kann von jeder Razor-Seite aus unter dem Ordner *Pages* verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-582">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="5eb2f-583">Die Layoutdatei sollte im Ordner *Pages/Shared* gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-583">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="5eb2f-584">Wir empfehlen Ihnen, die Layoutdatei **nicht** im Ordner *Views/Shared* (Ansichten/Freigegeben) zu platzieren.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-584">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="5eb2f-585">*Views/Shared* ist ein MVC-Ansichtsmuster.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-585">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="5eb2f-586">Razor Pages basieren auf der Ordnerhierarchie, nicht auf Pfadkonventionen.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-586">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="5eb2f-587">Die Ansichtensuche in einer Razor Page enthält den Ordner *Pages*.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-587">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="5eb2f-588">Die Layouts, Vorlagen und Teilansichten, die Sie mit MVC-Controllern und herkömmlichen Razor-Ansichten verwenden, *funktionieren einfach so*.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-588">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="5eb2f-589">Fügen Sie eine Datei *Pages/_ViewImports.cshtml* hinzu:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-589">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="5eb2f-590">`@namespace` wird weiter unten im Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-590">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="5eb2f-591">Die `@addTagHelper`-Anweisung bringt die [integrierten Taghilfsprogramme](xref:mvc/views/tag-helpers/builtin-th/Index) zu allen Seiten in der Ordner *Pages*.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-591">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="5eb2f-592">Wenn die `@namespace`-Anweisung explizit auf eine Seite angewendet wird:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-592">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="5eb2f-593">Die Anweisung legt den Namespace für die Seite fest.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-593">The directive sets the namespace for the page.</span></span> <span data-ttu-id="5eb2f-594">Die `@model`-Anweisung muss den Namespace nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-594">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="5eb2f-595">Wenn sich die `@namespace`-Anweisung in *_ViewImports.cshtml* befindet, stellt der angegebene Namespace das Präfix für den generierten Namespace auf der Seite bereit, die die `@namespace`-Anweisung importiert.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-595">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="5eb2f-596">Der Rest der generierten Namespaces (der Suffixteil) ist der durch Punkte getrennte relative Pfad zwischen dem Ordner mit *_ViewImports.cshtml* und dem Ordner, der die Seite enthält.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-596">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="5eb2f-597">Die `PageModel`-Klasse in *Pages/Customers/Edit.cshtml.cs* legt den Namespace z.B. explizit fest:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-597">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="5eb2f-598">Die Datei *Pages/_ViewImports.cshtml* legt den folgenden Namespace fest:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-598">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="5eb2f-599">Der generierte Namespace für die Razor Page *Pages/Customers/Edit.cshtml* ist identisch mit der `PageModel`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-599">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="5eb2f-600">`@namespace` *funktioniert auch mit konventionellen Razor-Ansichten.*</span><span class="sxs-lookup"><span data-stu-id="5eb2f-600">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="5eb2f-601">Die ursprüngliche Umgebungsdatei *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-601">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="5eb2f-602">Die aktualisierte Umgebungsdatei *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-602">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="5eb2f-603">Die [Razor Pages-Startprojekt](#rpvs17) enthält die Seite *Pages/_ValidationScriptsPartial.cshtml*, die die clientseitige Validierung bindet.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-603">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="5eb2f-604">Weitere Informationen zu Teilansichten finden Sie unter <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-604">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="5eb2f-605">URL-Generierung für Seiten</span><span class="sxs-lookup"><span data-stu-id="5eb2f-605">URL generation for Pages</span></span>

<span data-ttu-id="5eb2f-606">Die zuvor gezeigte `Create`-Seite verwendet `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-606">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="5eb2f-607">Die App hat die folgende Datei/Ordner-Struktur:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-607">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="5eb2f-608">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="5eb2f-608">*/Pages*</span></span>

  * <span data-ttu-id="5eb2f-609">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5eb2f-609">*Index.cshtml*</span></span>
  * <span data-ttu-id="5eb2f-610">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="5eb2f-610">*/Customers*</span></span>

    * <span data-ttu-id="5eb2f-611">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5eb2f-611">*Create.cshtml*</span></span>
    * <span data-ttu-id="5eb2f-612">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5eb2f-612">*Edit.cshtml*</span></span>
    * <span data-ttu-id="5eb2f-613">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="5eb2f-613">*Index.cshtml*</span></span>

<span data-ttu-id="5eb2f-614">Die Seiten *Pages/Customers/Create.cshtml* und *Pages/Customers/Edit.cshtml* leiten bei Erfolg an *Pages/Index.cshtml* weiter.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-614">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="5eb2f-615">Die Zeichenfolge `/Index` ist Teil des URI, der auf die vorhergehende Seite zugreifen soll.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-615">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="5eb2f-616">Die Zeichenfolge `/Index` kann für das Generieren von URIs für die Seite *Pages/Index.cshtml* verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-616">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="5eb2f-617">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-617">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="5eb2f-618">Der Seitenname ist der Pfad zu der Seite vom Stammordner */Pages* (einschließlich eines vorangestellten `/`, z.B. `/Index`).</span><span class="sxs-lookup"><span data-stu-id="5eb2f-618">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="5eb2f-619">Die oben stehenden Beispiele für eine URL-Generierung bieten erweiterte Optionen und Funktionen, durch die Sie URLs nicht mehr hartcodieren müssen.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-619">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="5eb2f-620">Bei der URL-Generierung wird [Routing](xref:mvc/controllers/routing) verwendet. Außerdem können damit Parameter generiert und entsprechend der Definition der Route im Zielpfad codiert werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-620">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="5eb2f-621">Die URL-Generierung für Seiten unterstützt relative Namen.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-621">URL generation for pages supports relative names.</span></span> <span data-ttu-id="5eb2f-622">In der folgenden Tabelle wird dargestellt, welche Indexseite für verschiedene `RedirectToPage`-Parameter aus *Pages/Customers/Create.cshtml* ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-622">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="5eb2f-623">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="5eb2f-623">RedirectToPage(x)</span></span>| <span data-ttu-id="5eb2f-624">Seite</span><span class="sxs-lookup"><span data-stu-id="5eb2f-624">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="5eb2f-625">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="5eb2f-625">RedirectToPage("/Index")</span></span> | <span data-ttu-id="5eb2f-626">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="5eb2f-626">*Pages/Index*</span></span> |
| <span data-ttu-id="5eb2f-627">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="5eb2f-627">RedirectToPage("./Index");</span></span> | <span data-ttu-id="5eb2f-628">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="5eb2f-628">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="5eb2f-629">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="5eb2f-629">RedirectToPage("../Index")</span></span> | <span data-ttu-id="5eb2f-630">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="5eb2f-630">*Pages/Index*</span></span> |
| <span data-ttu-id="5eb2f-631">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="5eb2f-631">RedirectToPage("Index")</span></span>  | <span data-ttu-id="5eb2f-632">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="5eb2f-632">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="5eb2f-633">`RedirectToPage("Index")`, `RedirectToPage("./Index")` und `RedirectToPage("../Index")` sind *relative Namen*.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-633">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="5eb2f-634">Der `RedirectToPage`-Parameter wird mit dem Pfad der aktuellen Seite *kombiniert*, um den Namen der Zielseite zu berechnen.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-634">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="5eb2f-635">Das Verknüpfen relativer Namen eignet sich beim Erstellen von Websites mit einer komplexen Struktur.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-635">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="5eb2f-636">Wenn Sie relative Namen verwenden, um Seiten in einem Ordner zu verknüpfen, können Sie diesen Ordner umbenennen.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-636">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="5eb2f-637">Alle Links funktionieren weiterhin, da sie nicht den Namen des Ordners enthalten.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-637">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="5eb2f-638">Um auf eine Seite in einem anderen [Bereich](xref:mvc/controllers/areas) umzuleiten, geben Sie den Bereich an:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-638">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="5eb2f-639">Weitere Informationen finden Sie unter <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-639">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="5eb2f-640">Attribut „ViewData“</span><span class="sxs-lookup"><span data-stu-id="5eb2f-640">ViewData attribute</span></span>

<span data-ttu-id="5eb2f-641">Daten können mit [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute) an eine Seite übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-641">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="5eb2f-642">Die Werte der Eigenschaften auf Controllern oder Razor Pages-Modellen, die mit `[ViewData]` versehen sind, werden in [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) gespeichert und daraus geladen.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-642">Properties on controllers or Razor Page models decorated with `[ViewData]` have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="5eb2f-643">Im folgenden Beispiel enthält `AboutModel` die Eigenschaft `Title`, die mit `[ViewData]` versehen ist.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-643">In the following example, the `AboutModel` contains a `Title` property decorated with `[ViewData]`.</span></span> <span data-ttu-id="5eb2f-644">Die Eigenschaft `Title` wird auf den Titel der Infoseite festgelegt:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-644">The `Title` property is set to the title of the About page:</span></span>

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

<span data-ttu-id="5eb2f-645">Greifen Sie auf der Infoseite auf die Eigenschaft `Title` als Modelleigenschaft zu:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-645">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="5eb2f-646">Im Layout wird der Titel aus dem ViewData-Wörterbuch gelesen:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-646">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="5eb2f-647">TempData</span><span class="sxs-lookup"><span data-stu-id="5eb2f-647">TempData</span></span>

<span data-ttu-id="5eb2f-648">ASP.NET Core macht die Eigenschaft [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) auf einem [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller) verfügbar.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-648">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="5eb2f-649">Diese Eigenschaft speichert Daten, bis sie gelesen wurden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-649">This property stores data until it's read.</span></span> <span data-ttu-id="5eb2f-650">Die Methoden `Keep` und `Peek` können verwendet werden, um die Daten zu überprüfen, ohne sie zu löschen.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-650">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="5eb2f-651">`TempData` eignet sich für die Weiterleitung, wenn Daten für mehr als eine Anforderung benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-651">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="5eb2f-652">Im folgenden Code wird der Wert von `Message` mit `TempData` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-652">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="5eb2f-653">Das folgende Markup in der Datei *Pages/Customers/Index.cshtml* zeigt den Wert von `Message` mit `TempData` an.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-653">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="5eb2f-654">Das Seitenmodell *Pages/Customers/Index.cshtml.cs* wendet das `[TempData]`-Attribut auf die Eigenschaft `Message` an.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-654">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```cs
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="5eb2f-655">Weitere Informationen finden Sie unter [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="5eb2f-655">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="5eb2f-656">Mehrere Handler pro Seite</span><span class="sxs-lookup"><span data-stu-id="5eb2f-656">Multiple handlers per page</span></span>

<span data-ttu-id="5eb2f-657">Die folgende Seite generiert mit dem `asp-page-handler`-Taghilfsprogramm Markup für zwei Handler:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-657">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="5eb2f-658">Das Formular im vorherigen Beispiel hat zwei Sendeschaltflächen, und jede verwendet `FormActionTagHelper`, um an eine andere URL zu übermitteln.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-658">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="5eb2f-659">Das `asp-page-handler`-Attribut ist eine Ergänzung für `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-659">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="5eb2f-660">`asp-page-handler` generiert URLs, die als Übermittlungsziel jeweils die durch eine Seite festgelegte Handlermethode verwenden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-660">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="5eb2f-661">`asp-page` wird nicht angegeben, weil das Beispiel mit der aktuellen Seite verknüpft.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-661">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="5eb2f-662">Das Seitenmodell:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-662">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="5eb2f-663">Der vorherige Code verwendet *benannte Handlermethoden*.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-663">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="5eb2f-664">Benannte Handlermethoden werden aus dem Text im Namen nach `On<HTTP Verb>` und vor `Async` (falls vorhanden) erstellt.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-664">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="5eb2f-665">Im vorherigen Beispiel sind OnPost**JoinList**Async und OnPost**JoinListUC**Async die Seitenmethoden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-665">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="5eb2f-666">Wenn Sie *OnPost* und *Async* entfernen, lauten die Handlernamen `JoinList` und `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-666">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="5eb2f-667">Mit dem vorherigen Code lautet der URL-Pfad, der an `OnPostJoinListAsync` übermittelt, `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-667">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="5eb2f-668">Der URL-Pfad, der an `OnPostJoinListUCAsync` übermittelt, lautet `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-668">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="5eb2f-669">Benutzerdefinierte Routen</span><span class="sxs-lookup"><span data-stu-id="5eb2f-669">Custom routes</span></span>

<span data-ttu-id="5eb2f-670">Verwenden Sie die `@page`-Anweisung für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-670">Use the `@page` directive to:</span></span>

* <span data-ttu-id="5eb2f-671">Das Angeben einer benutzerdefinierten Route zu einer Seite.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-671">Specify a custom route to a page.</span></span> <span data-ttu-id="5eb2f-672">Die Route zur Seite „Info“ kann mit `@page "/Some/Other/Path"` beispielsweise auf `/Some/Other/Path` festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-672">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="5eb2f-673">Das Anfügen von Segmenten an die Standardroute einer Seite.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-673">Append segments to a page's default route.</span></span> <span data-ttu-id="5eb2f-674">Mit `@page "item"` kann beispielsweise ein item-Segment an die Standardroute der Seite angefügt werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-674">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="5eb2f-675">Das Anfügen von Parametern an die Standardroute einer Seite.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-675">Append parameters to a page's default route.</span></span> <span data-ttu-id="5eb2f-676">Mit `@page "{id}"` kann beispielsweise ein ID-Parameter (`id`) für eine Seite angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-676">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="5eb2f-677">Es wird ein relativer Pfad zum Stamm unterstützt, der durch eine Tilde (`~`) festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-677">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="5eb2f-678">`@page "~/Some/Other/Path"` entspricht beispielsweise `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-678">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="5eb2f-679">Sie können die Abfragezeichenfolge `?handler=JoinList` in der URL in ein Routensegment (`/JoinList`) ändern, indem Sie die Routenvorlage `@page "{handler?}"` angeben.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-679">You can change the query string `?handler=JoinList` in the URL to a route segment `/JoinList` by specifying the route template `@page "{handler?}"`.</span></span>

<span data-ttu-id="5eb2f-680">Wenn Sie nicht möchten, dass die Abfragezeichenfolge `?handler=JoinList` in der URL enthalten ist, können Sie die Route ändern, damit der Handlername im Pfadteil der URL eingefügt wird.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-680">If you don't like the query string `?handler=JoinList` in the URL, you can change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="5eb2f-681">Sie können die Route anpassen, indem Sie eine Routenvorlage in doppelten Anführungszeichen nach der `@page`-Anweisung hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-681">You can customize the route by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="5eb2f-682">Mit dem vorherigen Code lautet der URL-Pfad, der an `OnPostJoinListAsync` übermittelt, `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-682">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="5eb2f-683">Der URL-Pfad, der an `OnPostJoinListUCAsync` übermittelt, lautet `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-683">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="5eb2f-684">Das `?` nach `handler` bedeutet, dass der Routenparameter optional ist.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-684">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="5eb2f-685">Konfiguration und Einstellungen</span><span class="sxs-lookup"><span data-stu-id="5eb2f-685">Configuration and settings</span></span>

<span data-ttu-id="5eb2f-686">Um die erweiterten Optionen zu konfigurieren, verwenden Sie die Erweiterungsmethode `AddRazorPagesOptions` auf dem MVC-Generator:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-686">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="5eb2f-687">Derzeit können Sie `RazorPagesOptions` verwenden, um das Stammverzeichnis für Seiten festzulegen oder Anwendungsmodellkonventionen für Seiten hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-687">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="5eb2f-688">Auf diese Weise wird in Zukunft eine höhere Erweiterbarkeit erreicht.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-688">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="5eb2f-689">Informationen zum Vorkompilieren von Ansichten finden Sie unter [Razor view compilation and precompilation in ASP.NET Core (Razor-Ansichtenkompilierung und Vorkompilierung in ASP.NET)](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="5eb2f-689">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="5eb2f-690">[Laden Sie Beispielcode herunter, oder zeigen Sie ihn an](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="5eb2f-690">[Download or view sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="5eb2f-691">Lesen Sie auch den Artikel [Erste Schritte mit Razor-Seiten](xref:tutorials/razor-pages/razor-pages-start), der auf dieser Einführung aufbaut.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-691">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="5eb2f-692">Festlegen des Inhaltsstammverzeichnisses für Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5eb2f-692">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="5eb2f-693">Standardmäßig lautet das Stammverzeichnis für Razor Pages */Pages*.</span><span class="sxs-lookup"><span data-stu-id="5eb2f-693">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="5eb2f-694">Fügen Sie [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) zu [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) hinzu, um anzugeben, dass sich Ihre Razor Pages im [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) der App befinden:</span><span class="sxs-lookup"><span data-stu-id="5eb2f-694">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="5eb2f-695">Festlegen eines benutzerdefinierten Stammverzeichnisses für Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5eb2f-695">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="5eb2f-696">Fügen Sie [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) zu [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) hinzu, um anzugeben, dass Ihre Razor Pages sich in einem benutzerdefinierten Stammverzeichnis in der App befinden (geben Sie einen relativen Pfad an):</span><span class="sxs-lookup"><span data-stu-id="5eb2f-696">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="5eb2f-697">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="5eb2f-697">Additional resources</span></span>

* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
