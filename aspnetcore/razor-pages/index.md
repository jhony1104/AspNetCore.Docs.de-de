---
title: Einführung in Razor Pages in ASP.NET Core
author: Rick-Anderson
description: Erfahren Sie, wie Razor Pages in ASP.NET Core codierungsseitige Szenarios einfacher und produktiver gestalten als MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
uid: razor-pages/index
ms.openlocfilehash: 30e2cde03236bae4c3ca06a91c56586d8c9f2bff
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447450"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="f8a13-103">Einführung in Razor Pages in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f8a13-103">Introduction to Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f8a13-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="f8a13-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="f8a13-105">Razor Pages kann im Vergleich zu Controllern und Ansichten das Programmieren seitenbasierter Anwendungen vereinfachen und die Produktivität erhöhen.</span><span class="sxs-lookup"><span data-stu-id="f8a13-105">Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="f8a13-106">Ein Tutorial, in dem der Model-View-Controller-Ansatz verwendet wird, finden Sie unter [Erste Schritte mit ASP.NET Core MVC und Visual Studio](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="f8a13-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="f8a13-107">Dieses Dokument bietet eine Einführung in Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="f8a13-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="f8a13-108">Es handelt sich nicht um ein Schritt-für-Schritt-Tutorial.</span><span class="sxs-lookup"><span data-stu-id="f8a13-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="f8a13-109">Wenn es Ihnen Probleme bereitet, die Ausführungen in einigen Abschnitten nachzuvollziehen, lesen Sie [Erste Schritte mit Razor-Seiten in ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="f8a13-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="f8a13-110">Eine Übersicht über ASP.NET Core finden Sie unter [Einführung in ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="f8a13-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f8a13-111">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="f8a13-111">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f8a13-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8a13-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f8a13-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f8a13-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f8a13-114">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f8a13-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="f8a13-115">Erstellen eines Razor Pages-Projekts</span><span class="sxs-lookup"><span data-stu-id="f8a13-115">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f8a13-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8a13-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f8a13-117">Ausführliche Informationen zum Erstellen eines Razor Pages-Projekts finden Sie unter [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="f8a13-117">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f8a13-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f8a13-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f8a13-119">Führen Sie `dotnet new webapp` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="f8a13-119">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f8a13-120">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f8a13-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f8a13-121">Führen Sie `dotnet new webapp` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="f8a13-121">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="f8a13-122">Öffnen Sie die generierte Datei *.csproj* aus Visual Studio für Mac.</span><span class="sxs-lookup"><span data-stu-id="f8a13-122">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="f8a13-123">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="f8a13-123">Razor Pages</span></span>

<span data-ttu-id="f8a13-124">Razor Pages ist in *Startup.cs* aktiviert:</span><span class="sxs-lookup"><span data-stu-id="f8a13-124">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="f8a13-125">Sehen Sie sich diese einfache Seite an: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="f8a13-125">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="f8a13-126">Der vorherige Code ähnelt sehr einer [Razor-Ansichtsdatei](xref:tutorials/first-mvc-app/adding-view), die in einer ASP.NET Core-App mit Controllern und Ansichten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f8a13-126">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="f8a13-127">Der Unterschied besteht in der [`@page`](xref:mvc/views/razor#page)-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="f8a13-127">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="f8a13-128">`@page` macht die Datei zu einer MVC-Aktion, d.h. dass Anfragen direkt ohne einen Controller verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-128">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="f8a13-129">`@page` muss die erste Razor-Anweisung auf einer Seite sein.</span><span class="sxs-lookup"><span data-stu-id="f8a13-129">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="f8a13-130">`@page` wirkt sich auf das Verhalten aller anderen [Razor](xref:mvc/views/razor)-Konstrukte aus.</span><span class="sxs-lookup"><span data-stu-id="f8a13-130">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="f8a13-131">Razor Pages-Dateinamen haben das Suffix *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f8a13-131">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="f8a13-132">Eine ähnliche Seite, die die `PageModel`-Klasse verwendet, wird in den folgenden zwei Dateien angezeigt.</span><span class="sxs-lookup"><span data-stu-id="f8a13-132">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="f8a13-133">Die Datei *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f8a13-133">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="f8a13-134">Das Seitenmodell *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="f8a13-134">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="f8a13-135">Die `PageModel`-Klassendatei hat standardmäßig den gleichen Namen wie die Datei mit Razor Pages, nur dass außerdem *cs* angefügt wird.</span><span class="sxs-lookup"><span data-stu-id="f8a13-135">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="f8a13-136">Die vorherige Datei mit Razor Pages lautet beispielsweise *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f8a13-136">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="f8a13-137">Die Datei mit der `PageModel`-Klasse heißt *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="f8a13-137">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="f8a13-138">Die Zuordnungen von URL-Pfaden zu Seiten werden durch den Speicherort der Seite im Dateisystem bestimmt.</span><span class="sxs-lookup"><span data-stu-id="f8a13-138">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="f8a13-139">Die folgende Tabelle zeigt einen Pfad zu Razor Pages und die entsprechende URL:</span><span class="sxs-lookup"><span data-stu-id="f8a13-139">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="f8a13-140">Dateiname und Pfad</span><span class="sxs-lookup"><span data-stu-id="f8a13-140">File name and path</span></span>               | <span data-ttu-id="f8a13-141">Entsprechende URL</span><span class="sxs-lookup"><span data-stu-id="f8a13-141">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="f8a13-142">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f8a13-142">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="f8a13-143">`/` oder `/Index`</span><span class="sxs-lookup"><span data-stu-id="f8a13-143">`/` or `/Index`</span></span> |
| <span data-ttu-id="f8a13-144">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f8a13-144">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="f8a13-145">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f8a13-145">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="f8a13-146">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f8a13-146">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="f8a13-147">`/Store` oder `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="f8a13-147">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="f8a13-148">Notizen:</span><span class="sxs-lookup"><span data-stu-id="f8a13-148">Notes:</span></span>

* <span data-ttu-id="f8a13-149">Die Runtime sucht standardmäßig im Ordner *Pages* (Seiten) nach Dateien mit Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="f8a13-149">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="f8a13-150">Wenn eine Seite nicht in einer URL enthalten ist, ist `Index` die Standardseite.</span><span class="sxs-lookup"><span data-stu-id="f8a13-150">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="f8a13-151">Schreiben eines einfachen Formulars</span><span class="sxs-lookup"><span data-stu-id="f8a13-151">Write a basic form</span></span>

<span data-ttu-id="f8a13-152">Razor Pages ist darauf ausgelegt, allgemeine Muster, die mit Webbrowsern verwendet werden können, beim Erstellen einer App leichter implementieren zu können.</span><span class="sxs-lookup"><span data-stu-id="f8a13-152">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="f8a13-153">Die [Modellbindung](xref:mvc/models/model-binding), [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) und alle HTML-Hilfsprogramme *funktionieren nur* mit den Eigenschaften, die in einer Klasse der Razor Pages definiert wurden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-153">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="f8a13-154">Nehmen wir z.B. eine Seite, die ein allgemeines Kontaktformular für das `Contact`-Modell implementiert:</span><span class="sxs-lookup"><span data-stu-id="f8a13-154">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="f8a13-155">Für die Beispiele in diesem Dokument wird `DbContext` in der Datei [Startup.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) initialisiert.</span><span class="sxs-lookup"><span data-stu-id="f8a13-155">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="f8a13-156">Das Datenmodell:</span><span class="sxs-lookup"><span data-stu-id="f8a13-156">The data model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="f8a13-157">Der db-Kontext:</span><span class="sxs-lookup"><span data-stu-id="f8a13-157">The db context:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="f8a13-158">Die Umgebungsdatei *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f8a13-158">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="f8a13-159">Das Seitenmodell *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="f8a13-159">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="f8a13-160">Die `PageModel` -Klasse heißt standardmäßig `<PageName>Model` und befindet sich im selben Namespace wie die Seite.</span><span class="sxs-lookup"><span data-stu-id="f8a13-160">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="f8a13-161">Mit der Klasse `PageModel` kann die Logik einer Seite von deren Darstellung getrennt werden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-161">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="f8a13-162">Sie definiert Seitenhandler für Anforderungen, die an die Seite geschickt wurden, und für zum Rendern der Seite verwendete Daten.</span><span class="sxs-lookup"><span data-stu-id="f8a13-162">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="f8a13-163">Diese Trennung ermöglicht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="f8a13-163">This separation allows:</span></span>

* <span data-ttu-id="f8a13-164">Verwalten von Seitenabhängigkeiten mithilfe von [Dependency Injection](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="f8a13-164">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="f8a13-165">Komponententests</span><span class="sxs-lookup"><span data-stu-id="f8a13-165">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="f8a13-166">Die Seite verfügt über eine `OnPostAsync`-*Handlermethode*, die bei `POST`-Anforderungen ausgeführt wird (wenn ein Benutzer das Formular sendet).</span><span class="sxs-lookup"><span data-stu-id="f8a13-166">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="f8a13-167">Für alle HTTP-Verben können Handlermethoden hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-167">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="f8a13-168">Die am häufigsten verwendeten Handler sind:</span><span class="sxs-lookup"><span data-stu-id="f8a13-168">The most common handlers are:</span></span>

* <span data-ttu-id="f8a13-169">`OnGet`, um den für eine Seite erforderlichen Status zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="f8a13-169">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="f8a13-170">Im vorangehenden Code wird die Razor-Seite *CreateModel.cshtml* durch die `OnGet`-Methode dargestellt.</span><span class="sxs-lookup"><span data-stu-id="f8a13-170">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="f8a13-171">`OnPost`, um Formularübermittlungen zu behandeln</span><span class="sxs-lookup"><span data-stu-id="f8a13-171">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="f8a13-172">Das Namenssuffix `Async` ist optional. Es wird jedoch standardmäßig häufig für asynchrone Funktionen verwendet.</span><span class="sxs-lookup"><span data-stu-id="f8a13-172">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="f8a13-173">Der vorhergehende Code ist typisch für Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="f8a13-173">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="f8a13-174">Wenn Sie mit ASP.NET-Apps vertraut sind, die Controller und Ansichten verwenden, werden Ihnen folgende Fakten bekannt vorkommen:</span><span class="sxs-lookup"><span data-stu-id="f8a13-174">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="f8a13-175">Der `OnPostAsync`-Code im vorangehenden Beispiel ähnelt dem typischen Controllercode.</span><span class="sxs-lookup"><span data-stu-id="f8a13-175">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="f8a13-176">Die meisten primitiven MVC-Typen wie solche für [Modellbindungen](xref:mvc/models/model-binding), [Validierungen](xref:mvc/models/validation) und Aktionsergebnisse werden in Controllern und Razor Pages auf dieselbe Weise eingesetzt.</span><span class="sxs-lookup"><span data-stu-id="f8a13-176">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="f8a13-177">Die vorherige `OnPostAsync`-Methode:</span><span class="sxs-lookup"><span data-stu-id="f8a13-177">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="f8a13-178">Der grundlegende Ablauf von `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="f8a13-178">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="f8a13-179">Prüfen auf Validierungsfehler</span><span class="sxs-lookup"><span data-stu-id="f8a13-179">Check for validation errors.</span></span>

* <span data-ttu-id="f8a13-180">Wenn keine Fehler vorliegen, werden die Daten gespeichert und weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="f8a13-180">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="f8a13-181">Wenn es Fehler gibt, zeigen Sie die Seite erneut mit den Validierungsmeldungen an.</span><span class="sxs-lookup"><span data-stu-id="f8a13-181">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="f8a13-182">denn Validierungsfehler werden oftmals auf dem Client erkannt und nie an den Server übermittelt.</span><span class="sxs-lookup"><span data-stu-id="f8a13-182">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="f8a13-183">Die Umgebungsdatei *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f8a13-183">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="f8a13-184">Der von *Pages/Create.cshtml* gerenderte HTML-Code sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="f8a13-184">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="f8a13-185">Im vorherigen Code gilt für die Formularübermittlung mittels POST Folgendes:</span><span class="sxs-lookup"><span data-stu-id="f8a13-185">In the previous code, posting the form:</span></span>

* <span data-ttu-id="f8a13-186">Bei gültigen Daten:</span><span class="sxs-lookup"><span data-stu-id="f8a13-186">With valid data:</span></span>

  * <span data-ttu-id="f8a13-187">Die `OnPostAsync`-Handlermethode ruft die <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*>-Hilfsmethode auf.</span><span class="sxs-lookup"><span data-stu-id="f8a13-187">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="f8a13-188">`RedirectToPage` gibt eine Instanz von <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult> zurück.</span><span class="sxs-lookup"><span data-stu-id="f8a13-188">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="f8a13-189">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="f8a13-189">`RedirectToPage`:</span></span>

    * <span data-ttu-id="f8a13-190">ist ein Aktionsergebnis.</span><span class="sxs-lookup"><span data-stu-id="f8a13-190">Is an action result.</span></span>
    * <span data-ttu-id="f8a13-191">ähnelt `RedirectToAction` oder `RedirectToRoute` (wird in Controllern und Ansichten verwendet).</span><span class="sxs-lookup"><span data-stu-id="f8a13-191">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="f8a13-192">ist an Seiten angepasst.</span><span class="sxs-lookup"><span data-stu-id="f8a13-192">Is customized for pages.</span></span> <span data-ttu-id="f8a13-193">Im vorhergehenden Beispiel leitet es an die Stammindexseite (`/Index`) weiter.</span><span class="sxs-lookup"><span data-stu-id="f8a13-193">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="f8a13-194">Informationen zu `RedirectToPage` finden Sie im Abschnitt [URL-Generierung für Seiten](#url_gen).</span><span class="sxs-lookup"><span data-stu-id="f8a13-194">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="f8a13-195">Bei Validierungsfehlern, die an den Server übermittelt werden:</span><span class="sxs-lookup"><span data-stu-id="f8a13-195">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="f8a13-196">Die `OnPostAsync`-Handlermethode ruft die <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*>-Hilfsmethode auf.</span><span class="sxs-lookup"><span data-stu-id="f8a13-196">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="f8a13-197">`Page` gibt eine Instanz von <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> zurück.</span><span class="sxs-lookup"><span data-stu-id="f8a13-197">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="f8a13-198">Der Vorgang, bei dem `Page` zurückgegeben wird, ähnelt dem Vorgang, bei dem Aktionen im Controller `View` zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="f8a13-198">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="f8a13-199">`PageResult` ist der Standardrückgabetyp für eine Handlermethode.</span><span class="sxs-lookup"><span data-stu-id="f8a13-199">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="f8a13-200">Eine Handlermethode, die `void` zurückgibt, rendert die Seite.</span><span class="sxs-lookup"><span data-stu-id="f8a13-200">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="f8a13-201">Wenn im vorangehenden Beispiel das Formular mithilfe von POST übermittelt und dabei kein Wert angegeben wird, gibt [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) „false“ zurück.</span><span class="sxs-lookup"><span data-stu-id="f8a13-201">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="f8a13-202">In diesem Beispiel werden keine Validierungsfehler auf dem Client angezeigt.</span><span class="sxs-lookup"><span data-stu-id="f8a13-202">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="f8a13-203">Die Verarbeitung von Validierungsfehlern wird weiter unten in diesem Artikel behandelt.</span><span class="sxs-lookup"><span data-stu-id="f8a13-203">Validation error handing is covered later in this document.</span></span>

  [!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="f8a13-204">Bei Validierungsfehlern, die durch eine clientseitige Validierung erkannt werden:</span><span class="sxs-lookup"><span data-stu-id="f8a13-204">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="f8a13-205">Die Daten werden **nicht** per POST an den Server gesendet.</span><span class="sxs-lookup"><span data-stu-id="f8a13-205">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="f8a13-206">Die clientseitige Validierung wird weiter unten in diesem Artikel erläutert.</span><span class="sxs-lookup"><span data-stu-id="f8a13-206">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="f8a13-207">Die Eigenschaft `Customer` verwendet das [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute)-Attribut, um die Modellbindung zu aktivieren:</span><span class="sxs-lookup"><span data-stu-id="f8a13-207">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="f8a13-208">`[BindProperty]` sollte **nicht** in Modellen mit Eigenschaften verwendet werden, die vom Client nicht geändert werden dürfen.</span><span class="sxs-lookup"><span data-stu-id="f8a13-208">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="f8a13-209">Weitere Informationen finden Sie unter [Overposting](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="f8a13-209">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="f8a13-210">Razor Pages binden Eigenschaften standardmäßig nur an Nicht-`GET`-Verben.</span><span class="sxs-lookup"><span data-stu-id="f8a13-210">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="f8a13-211">Durch die Bindung an Eigenschaften entfällt das Schreiben von Code, mit dem HTTP-Daten in den Modelltyp konvertiert werden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-211">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="f8a13-212">Die Bindung reduziert den Code mithilfe der gleichen Eigenschaft, um Formularfelder (`<input asp-for="Customer.Name">`) zu rendern und die Eingabe zu akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="f8a13-212">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="f8a13-213">Sehen Sie sich die Ansichtsdatei *Pages/Create.cshtml* an:</span><span class="sxs-lookup"><span data-stu-id="f8a13-213">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="f8a13-214">Im vorangehenden Code bindet das [Eingabetag-Hilfsprogramm](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` das HTML-Element `<input>` an den Modellausdruck `Customer.Name`.</span><span class="sxs-lookup"><span data-stu-id="f8a13-214">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="f8a13-215">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) stellt Taghilfsprogramme zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="f8a13-215">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="f8a13-216">Die Homepage</span><span class="sxs-lookup"><span data-stu-id="f8a13-216">The home page</span></span>

<span data-ttu-id="f8a13-217">*Index.cshtml* ist die Homepage:</span><span class="sxs-lookup"><span data-stu-id="f8a13-217">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="f8a13-218">Die zugeordnete `PageModel`-Klasse (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8a13-218">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="f8a13-219">Die Datei *Index.cshtml* enthält das folgende Markup:</span><span class="sxs-lookup"><span data-stu-id="f8a13-219">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="f8a13-220">Das [Anchor-Taghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `<a /a>` verwendet das `asp-route-{value}`-Attribut, um einen Link zur Bearbeitungsseite zu generieren.</span><span class="sxs-lookup"><span data-stu-id="f8a13-220">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="f8a13-221">Der Link enthält die Routendaten mit der Kontakt-ID.</span><span class="sxs-lookup"><span data-stu-id="f8a13-221">The link contains route data with the contact ID.</span></span> <span data-ttu-id="f8a13-222">Beispielsweise `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="f8a13-222">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="f8a13-223">[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) ermöglichen serverseitigem Code das Mitwirken am Erstellen und Rendern von HTML-Elementen in Razor-Dateien.</span><span class="sxs-lookup"><span data-stu-id="f8a13-223">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="f8a13-224">Die Datei *Index.cshtml* enthält das Markup zum Erstellen der Schaltfläche „delete“ (Löschen) für jeden Kundenkontakt:</span><span class="sxs-lookup"><span data-stu-id="f8a13-224">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="f8a13-225">Der gerenderte HTML-Code sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="f8a13-225">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="f8a13-226">Wenn die „delete“-Schaltfläche in HTML gerendert wird, enthält das zugehörige [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction)-Element Parameter für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="f8a13-226">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="f8a13-227">die Kundenkontakt-ID, die durch das `asp-route-id`-Attribut angegeben wird</span><span class="sxs-lookup"><span data-stu-id="f8a13-227">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="f8a13-228">den `handler`, der durch das `asp-page-handler`-Attribut angegeben wird</span><span class="sxs-lookup"><span data-stu-id="f8a13-228">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="f8a13-229">Wenn die Schaltfläche ausgewählt wird, wird eine `POST`-Anforderung an den Server gesendet.</span><span class="sxs-lookup"><span data-stu-id="f8a13-229">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="f8a13-230">Durch Konvention wird der Name der Handlermethode auf Grundlage des Werts des `handler`-Parameters gemäß dem Schema `OnPost[handler]Async` ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="f8a13-230">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="f8a13-231">Da der `handler` in diesem Beispiel `delete` ist, wird die Handlermethode `OnPostDeleteAsync` verwendet, um die `POST`-Anforderung zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="f8a13-231">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="f8a13-232">Wenn `asp-page-handler` auf einen anderen Wert (z. B. `remove`) festgelegt wird, wird eine Handlermethode namens `OnPostRemoveAsync` ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="f8a13-232">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="f8a13-233">Die `OnPostDeleteAsync`-Methode:</span><span class="sxs-lookup"><span data-stu-id="f8a13-233">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="f8a13-234">ruft die `id` der Abfragezeichenfolge ab.</span><span class="sxs-lookup"><span data-stu-id="f8a13-234">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="f8a13-235">Fragt mit `FindAsync` die Datenbank nach dem Kundenkontakt ab.</span><span class="sxs-lookup"><span data-stu-id="f8a13-235">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="f8a13-236">Wenn der Kundenkontakt gefunden wird, wird er entfernt, und die Datenbank wird aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="f8a13-236">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="f8a13-237">Ruft <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> auf, um die Stammindexseite (`/Index`) umzuleiten.</span><span class="sxs-lookup"><span data-stu-id="f8a13-237">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="f8a13-238">Die Datei „Edit.cshtml“</span><span class="sxs-lookup"><span data-stu-id="f8a13-238">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="f8a13-239">Die erste Zeile enthält die `@page "{id:int}"`-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="f8a13-239">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="f8a13-240">Die Routingbeschränkung `"{id:int}"` weist die Seite an, die Anforderungen für die Seite zu akzeptieren, die `int`-Routingdaten enthalten.</span><span class="sxs-lookup"><span data-stu-id="f8a13-240">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="f8a13-241">Wenn eine Anforderung an die Seite bestimmte Routingdaten nicht enthält, die in einen `int` konvertiert werden können, gibt die Runtime einen Fehler vom Typ „HTTP 404: Nicht gefunden“ zurück.</span><span class="sxs-lookup"><span data-stu-id="f8a13-241">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="f8a13-242">Um die ID optional zu machen, fügen Sie `?` an die Routeneinschränkung an:</span><span class="sxs-lookup"><span data-stu-id="f8a13-242">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="f8a13-243">Die Datei *Edit.cshtml.cs* sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="f8a13-243">The *Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="f8a13-244">Validierung</span><span class="sxs-lookup"><span data-stu-id="f8a13-244">Validation</span></span>

<span data-ttu-id="f8a13-245">Für Validierungsregeln gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="f8a13-245">Validation rules:</span></span>

* <span data-ttu-id="f8a13-246">Sie werden deklarativ in der Modellklasse angegeben.</span><span class="sxs-lookup"><span data-stu-id="f8a13-246">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="f8a13-247">Sie werden überall in der App erzwungen.</span><span class="sxs-lookup"><span data-stu-id="f8a13-247">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="f8a13-248">Der Namespace <xref:System.ComponentModel.DataAnnotations> stellt eine Gruppe integrierter Validierungsattribute bereit, die deklarativ auf eine Klasse oder Eigenschaft angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-248">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="f8a13-249">„DataAnnotations“ enthält auch Formatierungsattribute wie [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute), die bei der Formatierung helfen und keinerlei Validierung bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="f8a13-249">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="f8a13-250">Sehen Sie sich das `Customer`-Modell an:</span><span class="sxs-lookup"><span data-stu-id="f8a13-250">Consider the `Customer` model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="f8a13-251">Für die folgende Ansichtsdatei *Create.cshtml* gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="f8a13-251">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="f8a13-252">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="f8a13-252">The preceding code:</span></span>

* <span data-ttu-id="f8a13-253">umfasst jQuery-Skripts einschließlich solcher zur Validierung.</span><span class="sxs-lookup"><span data-stu-id="f8a13-253">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="f8a13-254">Verwendet die [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) `<div />` und `<span />`, um Folgendes zu ermöglichen:</span><span class="sxs-lookup"><span data-stu-id="f8a13-254">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="f8a13-255">clientseitiger Validierung.</span><span class="sxs-lookup"><span data-stu-id="f8a13-255">Client-side validation.</span></span>
  * <span data-ttu-id="f8a13-256">Rendering von Validierungsfehlern.</span><span class="sxs-lookup"><span data-stu-id="f8a13-256">Validation error rendering.</span></span>

* <span data-ttu-id="f8a13-257">wird der folgende HTML-Code generiert:</span><span class="sxs-lookup"><span data-stu-id="f8a13-257">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="f8a13-258">Wenn Sie das Formular „Create“ (Erstellen) ohne einen Wert für den Namen mit POST übermitteln, wird die Fehlermeldung „The Name field is required.“ (Für das Namensfeld muss ein Wert angegeben werden.)</span><span class="sxs-lookup"><span data-stu-id="f8a13-258">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="f8a13-259">auf dem Formular angezeigt.</span><span class="sxs-lookup"><span data-stu-id="f8a13-259">on the form.</span></span> <span data-ttu-id="f8a13-260">Wenn JavaScript auf dem Client aktiviert ist, zeigt der Browser den Fehler an, ohne dass Daten per POST an den Server gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-260">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="f8a13-261">Das `[StringLength(10)]`-Attribut generiert `data-val-length-max="10"` für den gerenderten HTML-Code.</span><span class="sxs-lookup"><span data-stu-id="f8a13-261">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="f8a13-262">`data-val-length-max` verhindert, dass im Browser ein Wert eingegeben wird, der die angegebene Maximallänge überschreitet.</span><span class="sxs-lookup"><span data-stu-id="f8a13-262">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="f8a13-263">Wenn ein Tool wie [Fiddler](https://www.telerik.com/fiddler) zum Bearbeiten und erneuten Senden einer POST-Anforderung verwendet wird</span><span class="sxs-lookup"><span data-stu-id="f8a13-263">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="f8a13-264">und die Länge des Namens 10 Zeichen überschreitet,</span><span class="sxs-lookup"><span data-stu-id="f8a13-264">With the name longer than 10.</span></span>
* <span data-ttu-id="f8a13-265">geschieht Folgendes: Die Fehlermeldung „The field Name must be a string with a maximum length of 10.“ (Das Namensfeld muss eine Zeichenfolge mit maximal 10 Zeichen enthalten.)</span><span class="sxs-lookup"><span data-stu-id="f8a13-265">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="f8a13-266">wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="f8a13-266">is returned.</span></span>

<span data-ttu-id="f8a13-267">Sehen Sie sich das folgende `Movie`-Modell an:</span><span class="sxs-lookup"><span data-stu-id="f8a13-267">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="f8a13-268">Die Validierungsattribute geben das Verhalten an, das für die Modelleigenschaften erzwungen werden soll:</span><span class="sxs-lookup"><span data-stu-id="f8a13-268">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="f8a13-269">Die Attribute `Required` und `MinimumLength` geben an, dass eine Eigenschaft einen Wert haben muss. Ein Benutzer kann allerdings ein Leerzeichen eingeben, um diese Anforderung zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="f8a13-269">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="f8a13-270">Das Attribut `RegularExpression` wird verwendet, um einzuschränken, welche Zeichen eingegeben werden dürfen.</span><span class="sxs-lookup"><span data-stu-id="f8a13-270">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="f8a13-271">Für „Genre“ im Code oben gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="f8a13-271">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="f8a13-272">Es dürfen nur Buchstaben enthalten sein.</span><span class="sxs-lookup"><span data-stu-id="f8a13-272">Must only use letters.</span></span>
  * <span data-ttu-id="f8a13-273">Der erste Buchstabe muss ein Großbuchstabe sein.</span><span class="sxs-lookup"><span data-stu-id="f8a13-273">The first letter is required to be uppercase.</span></span> <span data-ttu-id="f8a13-274">Leerzeichen, Zahlen und Sonderzeichen sind nicht zulässig.</span><span class="sxs-lookup"><span data-stu-id="f8a13-274">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="f8a13-275">Für `RegularExpression`-„Rating“ (Bewertung) gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="f8a13-275">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="f8a13-276">Das erste Zeichen muss ein Großbuchstabe sein.</span><span class="sxs-lookup"><span data-stu-id="f8a13-276">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="f8a13-277">Sonderzeichen und Zahlen sind als darauffolgende Zeichen zulässig.</span><span class="sxs-lookup"><span data-stu-id="f8a13-277">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="f8a13-278">„PG-13“ ist als Bewertung („Rating“) gültig, nicht jedoch als „Genre“.</span><span class="sxs-lookup"><span data-stu-id="f8a13-278">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="f8a13-279">Das Attribut `Range` schränkt einen Wert auf einen bestimmten Bereich ein.</span><span class="sxs-lookup"><span data-stu-id="f8a13-279">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="f8a13-280">Mit dem Attribut `StringLength` kann die maximale Länge einer Zeichenfolgeneigenschaft und optional die minimale Länge festlegt werden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-280">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="f8a13-281">Werttypen (wie `decimal`, `int`, `float`, `DateTime`) sind grundsätzlich erforderlich und benötigen nicht das Attribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="f8a13-281">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="f8a13-282">Auf der Seite „Create“ (Erstellen) für das `Movie`-Modell werden ungültige Werte und die daraus resultierenden Fehler angezeigt:</span><span class="sxs-lookup"><span data-stu-id="f8a13-282">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![Ansichtsformular „Movie“ mit mehreren clientseitigen jQuery-Validierungsfehlern](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="f8a13-284">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="f8a13-284">For more information, see:</span></span>

* [<span data-ttu-id="f8a13-285">Hinzufügen von Validierungscode zur Movie-App</span><span class="sxs-lookup"><span data-stu-id="f8a13-285">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="f8a13-286">[Modellvalidierung in ASP.NET Core](xref:mvc/models/validation)</span><span class="sxs-lookup"><span data-stu-id="f8a13-286">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="f8a13-287">Verarbeiten von HEAD-Anforderungen mit einem OnGet-Handlerfallback</span><span class="sxs-lookup"><span data-stu-id="f8a13-287">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="f8a13-288">`HEAD`-Anforderungen ermöglichen das Abrufen der Header für eine bestimmte Ressource.</span><span class="sxs-lookup"><span data-stu-id="f8a13-288">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="f8a13-289">Im Gegensatz zu `GET`-Anforderungen geben `HEAD`-Anforderungen keinen Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="f8a13-289">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="f8a13-290">Normalerweise wird ein `OnHead`-Handler erstellt und für `HEAD`-Anforderungen aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="f8a13-290">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="f8a13-291">Razor Pages ruft den `OnGet`-Handler auf, wenn kein `OnHead`-Handler definiert ist.</span><span class="sxs-lookup"><span data-stu-id="f8a13-291">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="f8a13-292">XSRF/CSRF und Razor Pages</span><span class="sxs-lookup"><span data-stu-id="f8a13-292">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="f8a13-293">Razor Pages wird durch [Validierungsmaßnahmen vor XSRF/CSRF-Angriffen geschützt](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="f8a13-293">Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="f8a13-294">Das [Formulartag-Hilfsprogramm](xref:mvc/views/working-with-forms#the-form-tag-helper) injiziert Anti-XSRF/CSRF-Token in HTML-Formularelemente.</span><span class="sxs-lookup"><span data-stu-id="f8a13-294">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="f8a13-295">Verwenden von Layouts, Teilansichten, Vorlagen und Taghilfsprogrammen mit Razor Pages</span><span class="sxs-lookup"><span data-stu-id="f8a13-295">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="f8a13-296">Razor Pages beinhaltet alle Funktionen der Razor-Anzeige-Engine.</span><span class="sxs-lookup"><span data-stu-id="f8a13-296">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="f8a13-297">Layouts, Teilansichten, Vorlagen, Taghilfsprogramme, *_ViewStart.cshtml* und *_ViewImports.cshtml* funktionieren auf die gleiche Weise wie für herkömmliche Razor-Ansichten.</span><span class="sxs-lookup"><span data-stu-id="f8a13-297">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="f8a13-298">Strukturieren Sie diese Seite mit einigen dieser praktischen Funktionen.</span><span class="sxs-lookup"><span data-stu-id="f8a13-298">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="f8a13-299">Fügen Sie der *Pages/Shared/_Layout.cshtml* eine [Layoutseite](xref:mvc/views/layout) hinzu:</span><span class="sxs-lookup"><span data-stu-id="f8a13-299">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="f8a13-300">Das [Layout](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="f8a13-300">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="f8a13-301">Steuert das Layout der einzelnen Seiten, es sei denn, das Layout wird für eine Seite deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="f8a13-301">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="f8a13-302">Importiert HTML-Strukturen, z.B. JavaScript und Stylesheets.</span><span class="sxs-lookup"><span data-stu-id="f8a13-302">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="f8a13-303">Der Inhalt der Razor-Seite wird gerendert, wenn `@RenderBody()` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="f8a13-303">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="f8a13-304">Weitere Informationen finden Sie unter [Layoutseite](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="f8a13-304">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="f8a13-305">Die Eigenschaft [Layout](xref:mvc/views/layout#specifying-a-layout) wird in *Pages/_ViewStart.cshtml* festgelegt:</span><span class="sxs-lookup"><span data-stu-id="f8a13-305">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="f8a13-306">Das Layout befindet sich im Ordner *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="f8a13-306">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="f8a13-307">Seiten suchen hierarchisch nach anderen Ansichten (Layouts, Vorlagen oder Teilansichten) und beginnen im gleichen Ordner wie die aktuelle Seite.</span><span class="sxs-lookup"><span data-stu-id="f8a13-307">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="f8a13-308">Ein Layout im Ordner *Pages/Shared* kann von jeder Razor-Seite aus unter dem Ordner *Pages* verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-308">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="f8a13-309">Die Layoutdatei sollte im Ordner *Pages/Shared* gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-309">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="f8a13-310">Wir empfehlen Ihnen, die Layoutdatei **nicht** im Ordner *Views/Shared* (Ansichten/Freigegeben) zu platzieren.</span><span class="sxs-lookup"><span data-stu-id="f8a13-310">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="f8a13-311">*Views/Shared* ist ein MVC-Ansichtsmuster.</span><span class="sxs-lookup"><span data-stu-id="f8a13-311">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="f8a13-312">Razor Pages basieren auf der Ordnerhierarchie, nicht auf Pfadkonventionen.</span><span class="sxs-lookup"><span data-stu-id="f8a13-312">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="f8a13-313">Die Ansichtensuche in einer Razor Page enthält den Ordner *Pages*.</span><span class="sxs-lookup"><span data-stu-id="f8a13-313">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="f8a13-314">Die Layouts, Vorlagen und Teilansichten, die mit MVC-Controllern und herkömmlichen Razor-Ansichten verwendet werden, *funktionieren problemlos*.</span><span class="sxs-lookup"><span data-stu-id="f8a13-314">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="f8a13-315">Fügen Sie eine Datei *Pages/_ViewImports.cshtml* hinzu:</span><span class="sxs-lookup"><span data-stu-id="f8a13-315">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="f8a13-316">`@namespace` wird weiter unten im Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="f8a13-316">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="f8a13-317">Die `@addTagHelper`-Anweisung bringt die [integrierten Taghilfsprogramme](xref:mvc/views/tag-helpers/builtin-th/Index) zu allen Seiten in der Ordner *Pages*.</span><span class="sxs-lookup"><span data-stu-id="f8a13-317">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="f8a13-318">Die `@namespace`-Anweisung wird wie folgt für eine Seite festgelegt:</span><span class="sxs-lookup"><span data-stu-id="f8a13-318">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="f8a13-319">Die `@namespace`-Anweisung legt den Namespace für die Seite fest.</span><span class="sxs-lookup"><span data-stu-id="f8a13-319">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="f8a13-320">Die `@model`-Anweisung muss den Namespace nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="f8a13-320">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="f8a13-321">Wenn sich die `@namespace`-Anweisung in *_ViewImports.cshtml* befindet, stellt der angegebene Namespace das Präfix für den generierten Namespace auf der Seite bereit, die die `@namespace`-Anweisung importiert.</span><span class="sxs-lookup"><span data-stu-id="f8a13-321">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="f8a13-322">Der Rest der generierten Namespaces (der Suffixteil) ist der durch Punkte getrennte relative Pfad zwischen dem Ordner mit *_ViewImports.cshtml* und dem Ordner, der die Seite enthält.</span><span class="sxs-lookup"><span data-stu-id="f8a13-322">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="f8a13-323">Die `PageModel`-Klasse in *Pages/Customers/Edit.cshtml.cs* legt den Namespace z.B. explizit fest:</span><span class="sxs-lookup"><span data-stu-id="f8a13-323">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="f8a13-324">Die Datei *Pages/_ViewImports.cshtml* legt den folgenden Namespace fest:</span><span class="sxs-lookup"><span data-stu-id="f8a13-324">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="f8a13-325">Der generierte Namespace für die Razor Page *Pages/Customers/Edit.cshtml* ist identisch mit der `PageModel`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="f8a13-325">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="f8a13-326">`@namespace` *funktioniert auch mit konventionellen Razor-Ansichten.*</span><span class="sxs-lookup"><span data-stu-id="f8a13-326">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="f8a13-327">Sehen Sie sich die Ansichtsdatei *Pages/Create.cshtml* an:</span><span class="sxs-lookup"><span data-stu-id="f8a13-327">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="f8a13-328">Die aktualisierte Ansichtsdatei *Pages/Create.cshtml* mit *_ViewImports.cshtml* und der vorherigen Layoutdatei sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="f8a13-328">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="f8a13-329">Im vorangehenden Code werden von *_ViewImports.cshtml* der Namespace und die Taghilfsprogramme importiert.</span><span class="sxs-lookup"><span data-stu-id="f8a13-329">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="f8a13-330">Die JavaScript-Dateien werden von der Layoutdatei importiert.</span><span class="sxs-lookup"><span data-stu-id="f8a13-330">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="f8a13-331">Die [Razor Pages-Startprojekt](#rpvs17) enthält die Seite *Pages/_ValidationScriptsPartial.cshtml*, die die clientseitige Validierung bindet.</span><span class="sxs-lookup"><span data-stu-id="f8a13-331">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="f8a13-332">Weitere Informationen zu Teilansichten finden Sie unter <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="f8a13-332">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="f8a13-333">URL-Generierung für Seiten</span><span class="sxs-lookup"><span data-stu-id="f8a13-333">URL generation for Pages</span></span>

<span data-ttu-id="f8a13-334">Die zuvor gezeigte `Create`-Seite verwendet `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="f8a13-334">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="f8a13-335">Die App hat die folgende Datei/Ordner-Struktur:</span><span class="sxs-lookup"><span data-stu-id="f8a13-335">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="f8a13-336">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="f8a13-336">*/Pages*</span></span>

  * <span data-ttu-id="f8a13-337">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f8a13-337">*Index.cshtml*</span></span>
  * <span data-ttu-id="f8a13-338">*Privacy.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f8a13-338">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="f8a13-339">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="f8a13-339">*/Customers*</span></span>

    * <span data-ttu-id="f8a13-340">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f8a13-340">*Create.cshtml*</span></span>
    * <span data-ttu-id="f8a13-341">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f8a13-341">*Edit.cshtml*</span></span>
    * <span data-ttu-id="f8a13-342">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f8a13-342">*Index.cshtml*</span></span>

<span data-ttu-id="f8a13-343">Die Seiten *Pages/Customers/Create.cshtml* und *Pages/Customers/Edit.cshtml* führen bei Erfolg eine Umleitung zu *Pages/Customers/Index.cshtml* durch.</span><span class="sxs-lookup"><span data-stu-id="f8a13-343">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="f8a13-344">Die Zeichenfolge `./Index` stellt einen relativen Seitennamen dar, der für den Zugriff auf die vorherige Seite verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f8a13-344">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="f8a13-345">Sie wird für das Generieren von URIs für die Seite *Pages/Customers/Index.cshtml* verwendet.</span><span class="sxs-lookup"><span data-stu-id="f8a13-345">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="f8a13-346">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f8a13-346">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="f8a13-347">Der absolute Seitenname `/Index` wird zum Generieren der URLs für die Seite *Pages/Index.cshtml* verwendet.</span><span class="sxs-lookup"><span data-stu-id="f8a13-347">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="f8a13-348">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f8a13-348">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="f8a13-349">Der Seitenname ist der Pfad zu der Seite vom Stammordner */Pages* (einschließlich eines vorangestellten `/`, z.B. `/Index`).</span><span class="sxs-lookup"><span data-stu-id="f8a13-349">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="f8a13-350">Die oben stehenden Beispiele für eine URL-Generierung bieten erweiterte Optionen und Funktionen, durch die Sie URLs nicht mehr hartcodieren müssen.</span><span class="sxs-lookup"><span data-stu-id="f8a13-350">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="f8a13-351">Bei der URL-Generierung wird [Routing](xref:mvc/controllers/routing) verwendet. Außerdem können damit Parameter generiert und entsprechend der Definition der Route im Zielpfad codiert werden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-351">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="f8a13-352">Die URL-Generierung für Seiten unterstützt relative Namen.</span><span class="sxs-lookup"><span data-stu-id="f8a13-352">URL generation for pages supports relative names.</span></span> <span data-ttu-id="f8a13-353">In der folgenden Tabelle wird dargestellt, welche Indexseite durch verschiedene `RedirectToPage`-Parameter in *Pages/Customers/Create.cshtml* ausgewählt wird.</span><span class="sxs-lookup"><span data-stu-id="f8a13-353">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="f8a13-354">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="f8a13-354">RedirectToPage(x)</span></span>| <span data-ttu-id="f8a13-355">Seite</span><span class="sxs-lookup"><span data-stu-id="f8a13-355">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="f8a13-356">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="f8a13-356">RedirectToPage("/Index")</span></span> | <span data-ttu-id="f8a13-357">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="f8a13-357">*Pages/Index*</span></span> |
| <span data-ttu-id="f8a13-358">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="f8a13-358">RedirectToPage("./Index");</span></span> | <span data-ttu-id="f8a13-359">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="f8a13-359">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="f8a13-360">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="f8a13-360">RedirectToPage("../Index")</span></span> | <span data-ttu-id="f8a13-361">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="f8a13-361">*Pages/Index*</span></span> |
| <span data-ttu-id="f8a13-362">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="f8a13-362">RedirectToPage("Index")</span></span>  | <span data-ttu-id="f8a13-363">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="f8a13-363">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="f8a13-364">`RedirectToPage("Index")`, `RedirectToPage("./Index")` und `RedirectToPage("../Index")` sind *relative Namen*.</span><span class="sxs-lookup"><span data-stu-id="f8a13-364">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="f8a13-365">Der `RedirectToPage`-Parameter wird mit dem Pfad der aktuellen Seite *kombiniert*, um den Namen der Zielseite zu berechnen.</span><span class="sxs-lookup"><span data-stu-id="f8a13-365">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="f8a13-366">Das Verknüpfen relativer Namen eignet sich beim Erstellen von Websites mit einer komplexen Struktur.</span><span class="sxs-lookup"><span data-stu-id="f8a13-366">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="f8a13-367">Wenn durch relative Namen Seiten in einem Ordner verknüpft werden, hat das folgende Vorteile:</span><span class="sxs-lookup"><span data-stu-id="f8a13-367">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="f8a13-368">Relative Links funktionieren weiterhin, wenn ein Ordner umbenannt wird.</span><span class="sxs-lookup"><span data-stu-id="f8a13-368">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="f8a13-369">Links funktionieren weiterhin, da sie keinen Ordnernamen enthalten.</span><span class="sxs-lookup"><span data-stu-id="f8a13-369">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="f8a13-370">Um auf eine Seite in einem anderen [Bereich](xref:mvc/controllers/areas) umzuleiten, geben Sie den Bereich an:</span><span class="sxs-lookup"><span data-stu-id="f8a13-370">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="f8a13-371">Weitere Informationen finden Sie unter <xref:mvc/controllers/areas> und <xref:razor-pages/razor-pages-conventions>.</span><span class="sxs-lookup"><span data-stu-id="f8a13-371">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="f8a13-372">Attribut „ViewData“</span><span class="sxs-lookup"><span data-stu-id="f8a13-372">ViewData attribute</span></span>

<span data-ttu-id="f8a13-373">Daten können mit <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute> an eine Seite übermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-373">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="f8a13-374">Für Eigenschaften mit dem `[ViewData]`-Attribut werden die Werte in <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> gespeichert und daraus geladen.</span><span class="sxs-lookup"><span data-stu-id="f8a13-374">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="f8a13-375">Im folgenden Beispiel wendet `AboutModel` das `[ViewData]`-Attribut auf die `Title`-Eigenschaft an:</span><span class="sxs-lookup"><span data-stu-id="f8a13-375">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

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

<span data-ttu-id="f8a13-376">Greifen Sie auf der Infoseite auf die Eigenschaft `Title` als Modelleigenschaft zu:</span><span class="sxs-lookup"><span data-stu-id="f8a13-376">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="f8a13-377">Im Layout wird der Titel aus dem ViewData-Wörterbuch gelesen:</span><span class="sxs-lookup"><span data-stu-id="f8a13-377">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="f8a13-378">TempData</span><span class="sxs-lookup"><span data-stu-id="f8a13-378">TempData</span></span>

<span data-ttu-id="f8a13-379">ASP.NET Core macht <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> verfügbar.</span><span class="sxs-lookup"><span data-stu-id="f8a13-379">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="f8a13-380">Diese Eigenschaft speichert Daten, bis sie gelesen wurden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-380">This property stores data until it's read.</span></span> <span data-ttu-id="f8a13-381">Die Methoden <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> und <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> können verwendet werden, um die Daten zu überprüfen, ohne sie zu löschen.</span><span class="sxs-lookup"><span data-stu-id="f8a13-381">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="f8a13-382">`TempData` eignet sich für die Umleitung, wenn Daten für mehr als eine Anforderung benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-382">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="f8a13-383">Im folgenden Code wird der Wert von `Message` mit `TempData` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="f8a13-383">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="f8a13-384">Das folgende Markup in der Datei *Pages/Customers/Index.cshtml* zeigt den Wert von `Message` mit `TempData` an.</span><span class="sxs-lookup"><span data-stu-id="f8a13-384">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="f8a13-385">Das Seitenmodell *Pages/Customers/Index.cshtml.cs* wendet das `[TempData]`-Attribut auf die Eigenschaft `Message` an.</span><span class="sxs-lookup"><span data-stu-id="f8a13-385">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="f8a13-386">Weitere Informationen finden Sie unter [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="f8a13-386">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="f8a13-387">Mehrere Handler pro Seite</span><span class="sxs-lookup"><span data-stu-id="f8a13-387">Multiple handlers per page</span></span>

<span data-ttu-id="f8a13-388">Die folgende Seite generiert mit dem `asp-page-handler`-Taghilfsprogramm Markup für zwei Handler:</span><span class="sxs-lookup"><span data-stu-id="f8a13-388">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="f8a13-389">Das Formular im vorherigen Beispiel hat zwei Sendeschaltflächen, und jede verwendet `FormActionTagHelper`, um an eine andere URL zu übermitteln.</span><span class="sxs-lookup"><span data-stu-id="f8a13-389">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="f8a13-390">Das `asp-page-handler`-Attribut ist eine Ergänzung für `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="f8a13-390">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="f8a13-391">`asp-page-handler` generiert URLs, die als Übermittlungsziel jeweils die durch eine Seite festgelegte Handlermethode verwenden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-391">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="f8a13-392">`asp-page` wird nicht angegeben, weil das Beispiel mit der aktuellen Seite verknüpft.</span><span class="sxs-lookup"><span data-stu-id="f8a13-392">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="f8a13-393">Das Seitenmodell:</span><span class="sxs-lookup"><span data-stu-id="f8a13-393">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="f8a13-394">Der vorherige Code verwendet *benannte Handlermethoden*.</span><span class="sxs-lookup"><span data-stu-id="f8a13-394">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="f8a13-395">Benannte Handlermethoden werden aus dem Text im Namen nach `On<HTTP Verb>` und vor `Async` (falls vorhanden) erstellt.</span><span class="sxs-lookup"><span data-stu-id="f8a13-395">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="f8a13-396">Im vorherigen Beispiel sind OnPost**JoinList**Async und OnPost**JoinListUC**Async die Seitenmethoden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-396">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="f8a13-397">Wenn Sie *OnPost* und *Async* entfernen, lauten die Handlernamen `JoinList` und `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="f8a13-397">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="f8a13-398">Mit dem vorherigen Code lautet der URL-Pfad, der an `OnPostJoinListAsync` übermittelt, `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="f8a13-398">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="f8a13-399">Der URL-Pfad, der an `OnPostJoinListUCAsync` übermittelt, lautet `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="f8a13-399">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="f8a13-400">Benutzerdefinierte Routen</span><span class="sxs-lookup"><span data-stu-id="f8a13-400">Custom routes</span></span>

<span data-ttu-id="f8a13-401">Verwenden Sie die `@page`-Anweisung für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="f8a13-401">Use the `@page` directive to:</span></span>

* <span data-ttu-id="f8a13-402">Das Angeben einer benutzerdefinierten Route zu einer Seite.</span><span class="sxs-lookup"><span data-stu-id="f8a13-402">Specify a custom route to a page.</span></span> <span data-ttu-id="f8a13-403">Die Route zur Seite „Info“ kann mit `@page "/Some/Other/Path"` beispielsweise auf `/Some/Other/Path` festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-403">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="f8a13-404">Das Anfügen von Segmenten an die Standardroute einer Seite.</span><span class="sxs-lookup"><span data-stu-id="f8a13-404">Append segments to a page's default route.</span></span> <span data-ttu-id="f8a13-405">Mit `@page "item"` kann beispielsweise ein item-Segment an die Standardroute der Seite angefügt werden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-405">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="f8a13-406">Das Anfügen von Parametern an die Standardroute einer Seite.</span><span class="sxs-lookup"><span data-stu-id="f8a13-406">Append parameters to a page's default route.</span></span> <span data-ttu-id="f8a13-407">Mit `@page "{id}"` kann beispielsweise ein ID-Parameter (`id`) für eine Seite angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-407">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="f8a13-408">Es wird ein relativer Pfad zum Stamm unterstützt, der durch eine Tilde (`~`) festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="f8a13-408">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="f8a13-409">`@page "~/Some/Other/Path"` entspricht beispielsweise `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="f8a13-409">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="f8a13-410">Wenn Sie nicht möchten, dass die Abfragezeichenfolge `?handler=JoinList` in der URL enthalten ist, ändern Sie die Route so, dass der Handlername im Pfadteil der URL eingefügt wird.</span><span class="sxs-lookup"><span data-stu-id="f8a13-410">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="f8a13-411">Sie können die Route anpassen, indem Sie nach der `@page`-Anweisung eine Routenvorlage in doppelten Anführungszeichen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="f8a13-411">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="f8a13-412">Mit dem vorherigen Code lautet der URL-Pfad, der an `OnPostJoinListAsync` übermittelt, `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="f8a13-412">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="f8a13-413">Der URL-Pfad, der an `OnPostJoinListUCAsync` übermittelt, lautet `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="f8a13-413">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="f8a13-414">Das `?` nach `handler` bedeutet, dass der Routenparameter optional ist.</span><span class="sxs-lookup"><span data-stu-id="f8a13-414">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="f8a13-415">Erweiterte Konfigurationen und Einstellungen</span><span class="sxs-lookup"><span data-stu-id="f8a13-415">Advanced configuration and settings</span></span>

<span data-ttu-id="f8a13-416">Die Konfigurationen und Einstellungen in den folgenden Abschnitten sind für die meisten Apps nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="f8a13-416">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="f8a13-417">Verwenden Sie die Erweiterungsmethode <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>, um die erweiterten Optionen zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="f8a13-417">To configure advanced options, use the extension method <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="f8a13-418">Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>, um das Stammverzeichnis für Seiten festzulegen oder Anwendungsmodellkonventionen für Seiten hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="f8a13-418">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="f8a13-419">Weitere Informationen zu Konventionen finden Sie unter [Razor Pages-Autorisierungskonventionen](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="f8a13-419">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="f8a13-420">Informationen zum Vorkompilieren von Ansichten finden Sie unter [Razor-Ansichtenkompilierung](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="f8a13-420">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="f8a13-421">Festlegen des Inhaltsstammverzeichnisses für Razor Pages</span><span class="sxs-lookup"><span data-stu-id="f8a13-421">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="f8a13-422">Standardmäßig lautet das Stammverzeichnis für Razor Pages */Pages*.</span><span class="sxs-lookup"><span data-stu-id="f8a13-422">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="f8a13-423">Fügen Sie <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> hinzu, um anzugeben, dass sich Ihre Razor Pages im [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) der App befinden:</span><span class="sxs-lookup"><span data-stu-id="f8a13-423">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="f8a13-424">Festlegen eines benutzerdefinierten Stammverzeichnisses für Razor Pages</span><span class="sxs-lookup"><span data-stu-id="f8a13-424">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="f8a13-425">Fügen Sie <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> hinzu, um anzugeben, dass sich Ihre Razor-Seiten in einem benutzerdefinierten Stammverzeichnis der App befinden. Geben Sie dabei einen relativen Pfad an:</span><span class="sxs-lookup"><span data-stu-id="f8a13-425">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="f8a13-426">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f8a13-426">Additional resources</span></span>

* <span data-ttu-id="f8a13-427">Lesen Sie auch den Artikel [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start), der auf dieser Einführung aufbaut.</span><span class="sxs-lookup"><span data-stu-id="f8a13-427">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction</span></span>
* [<span data-ttu-id="f8a13-428">Herunterladen und Anzeigen des Beispielcodes</span><span class="sxs-lookup"><span data-stu-id="f8a13-428">Download or view sample code</span></span>](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/integrate-components>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f8a13-429">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="f8a13-429">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="f8a13-430">Razor Pages ist ein neuer Bestandteil von ASP.NET Core MVC, mit dem codierungsseitige Szenarios einfacher und produktiver werden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-430">Razor Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="f8a13-431">Ein Tutorial, in dem der Model-View-Controller-Ansatz verwendet wird, finden Sie unter [Erste Schritte mit ASP.NET Core MVC und Visual Studio](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="f8a13-431">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="f8a13-432">Dieses Dokument bietet eine Einführung in Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="f8a13-432">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="f8a13-433">Es handelt sich nicht um ein Schritt-für-Schritt-Tutorial.</span><span class="sxs-lookup"><span data-stu-id="f8a13-433">It's not a step by step tutorial.</span></span> <span data-ttu-id="f8a13-434">Wenn es Ihnen Probleme bereitet, die Ausführungen in einigen Abschnitten nachzuvollziehen, lesen Sie [Erste Schritte mit Razor-Seiten in ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="f8a13-434">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="f8a13-435">Eine Übersicht über ASP.NET Core finden Sie unter [Einführung in ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="f8a13-435">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f8a13-436">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="f8a13-436">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f8a13-437">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8a13-437">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f8a13-438">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f8a13-438">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f8a13-439">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f8a13-439">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="f8a13-440">Erstellen eines Razor Pages-Projekts</span><span class="sxs-lookup"><span data-stu-id="f8a13-440">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f8a13-441">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f8a13-441">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f8a13-442">Ausführliche Informationen zum Erstellen eines Razor Pages-Projekts finden Sie unter [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="f8a13-442">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f8a13-443">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f8a13-443">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f8a13-444">Führen Sie `dotnet new webapp` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="f8a13-444">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="f8a13-445">Öffnen Sie die generierte Datei *.csproj* aus Visual Studio für Mac.</span><span class="sxs-lookup"><span data-stu-id="f8a13-445">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f8a13-446">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f8a13-446">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f8a13-447">Führen Sie `dotnet new webapp` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="f8a13-447">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="f8a13-448">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="f8a13-448">Razor Pages</span></span>

<span data-ttu-id="f8a13-449">Razor Pages ist in *Startup.cs* aktiviert:</span><span class="sxs-lookup"><span data-stu-id="f8a13-449">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="f8a13-450">Sehen Sie sich diese einfache Seite an: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="f8a13-450">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="f8a13-451">Der vorherige Code ähnelt sehr einer [Razor-Ansichtsdatei](xref:tutorials/first-mvc-app/adding-view), die in einer ASP.NET Core-App mit Controllern und Ansichten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f8a13-451">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="f8a13-452">Der Unterschied besteht in der `@page`-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="f8a13-452">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="f8a13-453">`@page` macht die Datei zu einer MVC-Aktion, d.h. dass Anfragen direkt ohne einen Controller verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-453">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="f8a13-454">`@page` muss die erste Razor-Anweisung auf einer Seite sein.</span><span class="sxs-lookup"><span data-stu-id="f8a13-454">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="f8a13-455">`@page` wirkt sich auf das Verhalten aller anderen Razor-Konstrukte aus.</span><span class="sxs-lookup"><span data-stu-id="f8a13-455">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="f8a13-456">Eine ähnliche Seite, die die `PageModel`-Klasse verwendet, wird in den folgenden zwei Dateien angezeigt.</span><span class="sxs-lookup"><span data-stu-id="f8a13-456">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="f8a13-457">Die Datei *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f8a13-457">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="f8a13-458">Das Seitenmodell *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="f8a13-458">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="f8a13-459">Die `PageModel`-Klassendatei hat standardmäßig den gleichen Namen wie die Datei mit Razor Pages, nur dass außerdem *cs* angefügt wird.</span><span class="sxs-lookup"><span data-stu-id="f8a13-459">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="f8a13-460">Die vorherige Datei mit Razor Pages lautet beispielsweise *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f8a13-460">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="f8a13-461">Die Datei mit der `PageModel`-Klasse heißt *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="f8a13-461">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="f8a13-462">Die Zuordnungen von URL-Pfaden zu Seiten werden durch den Speicherort der Seite im Dateisystem bestimmt.</span><span class="sxs-lookup"><span data-stu-id="f8a13-462">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="f8a13-463">Die folgende Tabelle zeigt einen Pfad zu Razor Pages und die entsprechende URL:</span><span class="sxs-lookup"><span data-stu-id="f8a13-463">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="f8a13-464">Dateiname und Pfad</span><span class="sxs-lookup"><span data-stu-id="f8a13-464">File name and path</span></span>               | <span data-ttu-id="f8a13-465">Entsprechende URL</span><span class="sxs-lookup"><span data-stu-id="f8a13-465">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="f8a13-466">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f8a13-466">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="f8a13-467">`/` oder `/Index`</span><span class="sxs-lookup"><span data-stu-id="f8a13-467">`/` or `/Index`</span></span> |
| <span data-ttu-id="f8a13-468">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f8a13-468">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="f8a13-469">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f8a13-469">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="f8a13-470">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f8a13-470">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="f8a13-471">`/Store` oder `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="f8a13-471">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="f8a13-472">Notizen:</span><span class="sxs-lookup"><span data-stu-id="f8a13-472">Notes:</span></span>

* <span data-ttu-id="f8a13-473">Die Runtime sucht standardmäßig im Ordner *Pages* (Seiten) nach Dateien mit Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="f8a13-473">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="f8a13-474">Wenn eine Seite nicht in einer URL enthalten ist, ist `Index` die Standardseite.</span><span class="sxs-lookup"><span data-stu-id="f8a13-474">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="f8a13-475">Schreiben eines einfachen Formulars</span><span class="sxs-lookup"><span data-stu-id="f8a13-475">Write a basic form</span></span>

<span data-ttu-id="f8a13-476">Razor Pages ist darauf ausgelegt, allgemeine Muster, die mit Webbrowsern verwendet werden können, beim Erstellen einer App leichter implementieren zu können.</span><span class="sxs-lookup"><span data-stu-id="f8a13-476">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="f8a13-477">Die [Modellbindung](xref:mvc/models/model-binding), [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) und alle HTML-Hilfsprogramme *funktionieren nur* mit den Eigenschaften, die in einer Klasse der Razor Pages definiert wurden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-477">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="f8a13-478">Nehmen wir z.B. eine Seite, die ein allgemeines Kontaktformular für das `Contact`-Modell implementiert:</span><span class="sxs-lookup"><span data-stu-id="f8a13-478">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="f8a13-479">Für die Beispiele in diesem Dokument wird `DbContext` in der Datei [Startup.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) initialisiert.</span><span class="sxs-lookup"><span data-stu-id="f8a13-479">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="f8a13-480">Das Datenmodell:</span><span class="sxs-lookup"><span data-stu-id="f8a13-480">The data model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="f8a13-481">Der db-Kontext:</span><span class="sxs-lookup"><span data-stu-id="f8a13-481">The db context:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="f8a13-482">Die Umgebungsdatei *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f8a13-482">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="f8a13-483">Das Seitenmodell *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="f8a13-483">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="f8a13-484">Die `PageModel` -Klasse heißt standardmäßig `<PageName>Model` und befindet sich im selben Namespace wie die Seite.</span><span class="sxs-lookup"><span data-stu-id="f8a13-484">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="f8a13-485">Mit der Klasse `PageModel` kann die Logik einer Seite von deren Darstellung getrennt werden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-485">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="f8a13-486">Sie definiert Seitenhandler für Anforderungen, die an die Seite geschickt wurden, und für zum Rendern der Seite verwendete Daten.</span><span class="sxs-lookup"><span data-stu-id="f8a13-486">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="f8a13-487">Diese Trennung ermöglicht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="f8a13-487">This separation allows:</span></span>

* <span data-ttu-id="f8a13-488">Verwalten von Seitenabhängigkeiten mithilfe von [Dependency Injection](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="f8a13-488">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="f8a13-489">[Komponententests](xref:test/razor-pages-tests) für Seiten</span><span class="sxs-lookup"><span data-stu-id="f8a13-489">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="f8a13-490">Die Seite verfügt über eine `OnPostAsync`-*Handlermethode*, die bei `POST`-Anforderungen ausgeführt wird (wenn ein Benutzer das Formular sendet).</span><span class="sxs-lookup"><span data-stu-id="f8a13-490">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="f8a13-491">Sie können Handlermethoden für alle HTTP-Verben hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="f8a13-491">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="f8a13-492">Die am häufigsten verwendeten Handler sind:</span><span class="sxs-lookup"><span data-stu-id="f8a13-492">The most common handlers are:</span></span>

* <span data-ttu-id="f8a13-493">`OnGet`, um den für eine Seite erforderlichen Status zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="f8a13-493">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="f8a13-494">[OnGet](#OnGet)-Beispiel</span><span class="sxs-lookup"><span data-stu-id="f8a13-494">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="f8a13-495">`OnPost`, um Formularübermittlungen zu behandeln</span><span class="sxs-lookup"><span data-stu-id="f8a13-495">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="f8a13-496">Das Namenssuffix `Async` ist optional. Es wird jedoch standardmäßig häufig für asynchrone Funktionen verwendet.</span><span class="sxs-lookup"><span data-stu-id="f8a13-496">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="f8a13-497">Der vorhergehende Code ist typisch für Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="f8a13-497">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="f8a13-498">Wenn Sie mit ASP.NET-Apps vertraut sind, die Controller und Ansichten verwenden, werden Ihnen folgende Fakten bekannt vorkommen:</span><span class="sxs-lookup"><span data-stu-id="f8a13-498">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="f8a13-499">Der `OnPostAsync`-Code im vorangehenden Beispiel ähnelt dem typischen Controllercode.</span><span class="sxs-lookup"><span data-stu-id="f8a13-499">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="f8a13-500">Die meisten primitiven MVC-Typen wie solche für [Modellbindungen](xref:mvc/models/model-binding), [Validierungen](xref:mvc/models/validation), [Validierungen](xref:mvc/models/validation) und Aktionsergebnisse werden gemeinsam verwendet.</span><span class="sxs-lookup"><span data-stu-id="f8a13-500">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="f8a13-501">Die vorherige `OnPostAsync`-Methode:</span><span class="sxs-lookup"><span data-stu-id="f8a13-501">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="f8a13-502">Der grundlegende Ablauf von `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="f8a13-502">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="f8a13-503">Prüfen auf Validierungsfehler</span><span class="sxs-lookup"><span data-stu-id="f8a13-503">Check for validation errors.</span></span>

* <span data-ttu-id="f8a13-504">Wenn keine Fehler vorliegen, werden die Daten gespeichert und weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="f8a13-504">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="f8a13-505">Wenn es Fehler gibt, zeigen Sie die Seite erneut mit den Validierungsmeldungen an.</span><span class="sxs-lookup"><span data-stu-id="f8a13-505">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="f8a13-506">Die clientseitige Validierung ist identisch mit herkömmlichen ASP.NET Core MVC-Anwendungen,</span><span class="sxs-lookup"><span data-stu-id="f8a13-506">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="f8a13-507">denn Validierungsfehler werden oftmals auf dem Client erkannt und nie an den Server übermittelt.</span><span class="sxs-lookup"><span data-stu-id="f8a13-507">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="f8a13-508">Wenn die Daten erfolgreich eingegeben wurden, ruft die `OnPostAsync`-Handlermethode die `RedirectToPage`-Hilfsmethode auf, um eine Instanz von `RedirectToPageResult` zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="f8a13-508">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="f8a13-509">`RedirectToPage` ist ein neues Aktionsergebnis und ähnelt `RedirectToAction` oder `RedirectToRoute`, ist aber für Seiten angepasst.</span><span class="sxs-lookup"><span data-stu-id="f8a13-509">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="f8a13-510">Im vorhergehenden Beispiel leitet es an die Stammindexseite (`/Index`) weiter.</span><span class="sxs-lookup"><span data-stu-id="f8a13-510">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="f8a13-511">Informationen zu `RedirectToPage` finden Sie im Abschnitt [URL-Generierung für Seiten](#url_gen).</span><span class="sxs-lookup"><span data-stu-id="f8a13-511">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="f8a13-512">Wenn das übermittelte Formular Validierungsfehler enthält (die an den Server übergeben wurden), ruft die `OnPostAsync`-Handlermethode die `Page`-Hilfsmethode auf.</span><span class="sxs-lookup"><span data-stu-id="f8a13-512">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="f8a13-513">`Page` gibt eine Instanz von `PageResult` zurück.</span><span class="sxs-lookup"><span data-stu-id="f8a13-513">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="f8a13-514">Der Vorgang, bei dem `Page` zurückgegeben wird, ähnelt dem Vorgang, bei dem Aktionen im Controller `View` zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="f8a13-514">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="f8a13-515">`PageResult` ist der Standardrückgabetyp für eine Handlermethode.</span><span class="sxs-lookup"><span data-stu-id="f8a13-515">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="f8a13-516">Eine Handlermethode, die `void` zurückgibt, rendert die Seite.</span><span class="sxs-lookup"><span data-stu-id="f8a13-516">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="f8a13-517">Die Eigenschaft `Customer` verwendet das `[BindProperty]`-Attribut, um die Modellbindung zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="f8a13-517">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="f8a13-518">Razor Pages binden Eigenschaften standardmäßig nur an Nicht-`GET`-Verben.</span><span class="sxs-lookup"><span data-stu-id="f8a13-518">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="f8a13-519">Durch die Bindung an Eigenschaften können Sie den Umfang von Codes reduzieren, den Sie schreiben müssen.</span><span class="sxs-lookup"><span data-stu-id="f8a13-519">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="f8a13-520">Die Bindung reduziert den Code mithilfe der gleichen Eigenschaft, um Formularfelder (`<input asp-for="Customer.Name">`) zu rendern und die Eingabe zu akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="f8a13-520">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="f8a13-521">Die Startseite (*Index.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="f8a13-521">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="f8a13-522">Die zugeordnete `PageModel`-Klasse (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="f8a13-522">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="f8a13-523">Die Datei *Index.cshtml* enthält das folgende Markup, um einen Bearbeitungslink für jeden Kontakt zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="f8a13-523">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="f8a13-524">Das [Anchor-Taghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` verwendet das `asp-route-{value}`-Attribut, um einen Link zur Bearbeitungsseite zu generieren.</span><span class="sxs-lookup"><span data-stu-id="f8a13-524">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="f8a13-525">Der Link enthält die Routendaten mit der Kontakt-ID.</span><span class="sxs-lookup"><span data-stu-id="f8a13-525">The link contains route data with the contact ID.</span></span> <span data-ttu-id="f8a13-526">Beispielsweise `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="f8a13-526">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="f8a13-527">[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) ermöglichen serverseitigem Code das Mitwirken am Erstellen und Rendern von HTML-Elementen in Razor-Dateien.</span><span class="sxs-lookup"><span data-stu-id="f8a13-527">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="f8a13-528">Taghilfsprogramme werden durch `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` aktiviert.</span><span class="sxs-lookup"><span data-stu-id="f8a13-528">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="f8a13-529">Die Datei *Pages/Edit.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f8a13-529">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="f8a13-530">Die erste Zeile enthält die `@page "{id:int}"`-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="f8a13-530">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="f8a13-531">Die Routingbeschränkung `"{id:int}"` weist die Seite an, die Anforderungen für die Seite zu akzeptieren, die `int`-Routingdaten enthalten.</span><span class="sxs-lookup"><span data-stu-id="f8a13-531">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="f8a13-532">Wenn eine Anforderung an die Seite bestimmte Routingdaten nicht enthält, die in einen `int` konvertiert werden können, gibt die Runtime einen Fehler vom Typ „HTTP 404: Nicht gefunden“ zurück.</span><span class="sxs-lookup"><span data-stu-id="f8a13-532">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="f8a13-533">Um die ID optional zu machen, fügen Sie `?` an die Routeneinschränkung an:</span><span class="sxs-lookup"><span data-stu-id="f8a13-533">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="f8a13-534">Die Datei *Pages/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="f8a13-534">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="f8a13-535">Die Datei *index.cshtml* enthält auch Markup zum Erstellen der Schaltfläche „Löschen“ für jeden benutzerdefinierten Kontakt:</span><span class="sxs-lookup"><span data-stu-id="f8a13-535">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="f8a13-536">Wenn die „Löschen“-Schaltfläche in HTML gerendert wird, enthält ihr `formaction`-Element Parameter für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="f8a13-536">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="f8a13-537">Die benutzerdefinierte Kontakt-ID, die vom `asp-route-id`-Attribut angegeben wird</span><span class="sxs-lookup"><span data-stu-id="f8a13-537">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="f8a13-538">Der `handler`, der vom `asp-page-handler`-Attribut angegeben wird</span><span class="sxs-lookup"><span data-stu-id="f8a13-538">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="f8a13-539">Hier sehen Sie ein Beispiel für eine gerenderte „Löschen“-Schaltfläche mit einer benutzerdefinierten Kontakt-ID von `1`:</span><span class="sxs-lookup"><span data-stu-id="f8a13-539">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="f8a13-540">Wenn die Schaltfläche ausgewählt wird, wird eine `POST`-Anforderung an den Server gesendet.</span><span class="sxs-lookup"><span data-stu-id="f8a13-540">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="f8a13-541">Durch Konvention wird der Name der Handlermethode auf Grundlage des Werts des `handler`-Parameters gemäß dem Schema `OnPost[handler]Async` ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="f8a13-541">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="f8a13-542">Da der `handler` in diesem Beispiel `delete` ist, wird die Handlermethode `OnPostDeleteAsync` verwendet, um die `POST`-Anforderung zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="f8a13-542">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="f8a13-543">Wenn `asp-page-handler` auf einen anderen Wert (z. B. `remove`) festgelegt wird, wird eine Handlermethode namens `OnPostRemoveAsync` ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="f8a13-543">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="f8a13-544">Im folgenden Code wird der `OnPostDeleteAsync`-Handler definiert:</span><span class="sxs-lookup"><span data-stu-id="f8a13-544">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="f8a13-545">Die `OnPostDeleteAsync`-Methode:</span><span class="sxs-lookup"><span data-stu-id="f8a13-545">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="f8a13-546">Akzeptiert die `id` der Abfragezeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="f8a13-546">Accepts the `id` from the query string.</span></span> <span data-ttu-id="f8a13-547">Wenn die Anweisung der Seite *Index.cshtml* die Routingeinschränkung `"{id:int?}"` enthielte, würde `id` aus den Routendaten abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-547">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="f8a13-548">Die Routendaten für `id` werden beispielsweise wie folgt im URI angegeben: `https://localhost:5001/Customers/2`.</span><span class="sxs-lookup"><span data-stu-id="f8a13-548">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="f8a13-549">Fragt mit `FindAsync` die Datenbank nach dem Kundenkontakt ab.</span><span class="sxs-lookup"><span data-stu-id="f8a13-549">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="f8a13-550">Wenn der Kundenkontakt gefunden wird, wird er aus der Liste der Kundenkontakte entfernt.</span><span class="sxs-lookup"><span data-stu-id="f8a13-550">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="f8a13-551">Die Datenbank wurde aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="f8a13-551">The database is updated.</span></span>
* <span data-ttu-id="f8a13-552">Ruft `RedirectToPage` auf, um die Stammindexseite (`/Index`) umzuleiten.</span><span class="sxs-lookup"><span data-stu-id="f8a13-552">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="f8a13-553">Markieren von Eigenschaften als „Required“ (Erforderlich)</span><span class="sxs-lookup"><span data-stu-id="f8a13-553">Mark page properties as required</span></span>

<span data-ttu-id="f8a13-554">Eigenschaften in einem `PageModel` können mit dem [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute)-Attribut markiert werden:</span><span class="sxs-lookup"><span data-stu-id="f8a13-554">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="f8a13-555">Weitere Informationen finden Sie unter [Modellvalidierung](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="f8a13-555">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="f8a13-556">Verarbeiten von HEAD-Anforderungen mit einem OnGet-Handlerfallback</span><span class="sxs-lookup"><span data-stu-id="f8a13-556">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="f8a13-557">`HEAD`-Anforderungen ermöglichen Ihnen das Abrufen des Headers für eine bestimmte Ressource.</span><span class="sxs-lookup"><span data-stu-id="f8a13-557">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="f8a13-558">Im Gegensatz zu `GET`-Anforderungen geben `HEAD`-Anforderungen keinen Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="f8a13-558">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="f8a13-559">Normalerweise wird ein `OnHead`-Handler erstellt und für `HEAD`-Anforderungen aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="f8a13-559">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="f8a13-560">In ASP.NET Core 2.1 oder höher greift Razor Pages auf den `OnGet`-Handler zurück, wenn kein `OnHead`-Handler definiert ist.</span><span class="sxs-lookup"><span data-stu-id="f8a13-560">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="f8a13-561">Dieses Verhalten wird durch den Aufruf von [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices` ermöglicht:</span><span class="sxs-lookup"><span data-stu-id="f8a13-561">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="f8a13-562">Die Standardvorlagen generieren den `SetCompatibilityVersion`-Aufruf in ASP.NET Core 2.1 und 2.2.</span><span class="sxs-lookup"><span data-stu-id="f8a13-562">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="f8a13-563">Tatsächlich setzt `SetCompatibilityVersion` die Razor Pages-Option `AllowMappingHeadRequestsToGetHandler` auf `true`.</span><span class="sxs-lookup"><span data-stu-id="f8a13-563">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="f8a13-564">Sie müssen nicht alle Verhalten in `SetCompatibilityVersion` aktivieren, sondern können sich nur *bestimmte* Verhalten aussuchen.</span><span class="sxs-lookup"><span data-stu-id="f8a13-564">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="f8a13-565">Der folgende Code gibt an, dass `HEAD`-Anforderungen dem `OnGet`-Handler zugeordnet werden dürfen:</span><span class="sxs-lookup"><span data-stu-id="f8a13-565">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="f8a13-566">XSRF/CSRF und Razor Pages</span><span class="sxs-lookup"><span data-stu-id="f8a13-566">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="f8a13-567">Sie müssen keinen Code für die [Antifälschungsvalidierung](xref:security/anti-request-forgery) schreiben.</span><span class="sxs-lookup"><span data-stu-id="f8a13-567">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="f8a13-568">Die Generierung und Validierung von Antifälschungstoken ist automatisch in Razor Pages enthalten.</span><span class="sxs-lookup"><span data-stu-id="f8a13-568">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="f8a13-569">Verwenden von Layouts, Teilansichten, Vorlagen und Taghilfsprogrammen mit Razor Pages</span><span class="sxs-lookup"><span data-stu-id="f8a13-569">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="f8a13-570">Razor Pages beinhaltet alle Funktionen der Razor-Anzeige-Engine.</span><span class="sxs-lookup"><span data-stu-id="f8a13-570">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="f8a13-571">Layouts, Teilansichten, Vorlagen, Taghilfsprogramme, *_ViewStart.cshtml*, *_ViewImports.cshtml* funktionieren auf die gleiche Weise wie für herkömmliche Razor-Ansichten.</span><span class="sxs-lookup"><span data-stu-id="f8a13-571">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="f8a13-572">Strukturieren Sie diese Seite mit einigen dieser praktischen Funktionen.</span><span class="sxs-lookup"><span data-stu-id="f8a13-572">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="f8a13-573">Fügen Sie der *Pages/Shared/_Layout.cshtml* eine [Layoutseite](xref:mvc/views/layout) hinzu:</span><span class="sxs-lookup"><span data-stu-id="f8a13-573">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="f8a13-574">Das [Layout](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="f8a13-574">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="f8a13-575">Steuert das Layout der einzelnen Seiten, es sei denn, das Layout wird für eine Seite deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="f8a13-575">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="f8a13-576">Importiert HTML-Strukturen, z.B. JavaScript und Stylesheets.</span><span class="sxs-lookup"><span data-stu-id="f8a13-576">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="f8a13-577">Weitere Informationen finden Sie unter [Layoutseite](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="f8a13-577">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="f8a13-578">Die Eigenschaft [Layout](xref:mvc/views/layout#specifying-a-layout) wird in *Pages/_ViewStart.cshtml* festgelegt:</span><span class="sxs-lookup"><span data-stu-id="f8a13-578">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="f8a13-579">Das Layout befindet sich im Ordner *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="f8a13-579">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="f8a13-580">Seiten suchen hierarchisch nach anderen Ansichten (Layouts, Vorlagen oder Teilansichten) und beginnen im gleichen Ordner wie die aktuelle Seite.</span><span class="sxs-lookup"><span data-stu-id="f8a13-580">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="f8a13-581">Ein Layout im Ordner *Pages/Shared* kann von jeder Razor-Seite aus unter dem Ordner *Pages* verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-581">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="f8a13-582">Die Layoutdatei sollte im Ordner *Pages/Shared* gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-582">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="f8a13-583">Wir empfehlen Ihnen, die Layoutdatei **nicht** im Ordner *Views/Shared* (Ansichten/Freigegeben) zu platzieren.</span><span class="sxs-lookup"><span data-stu-id="f8a13-583">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="f8a13-584">*Views/Shared* ist ein MVC-Ansichtsmuster.</span><span class="sxs-lookup"><span data-stu-id="f8a13-584">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="f8a13-585">Razor Pages basieren auf der Ordnerhierarchie, nicht auf Pfadkonventionen.</span><span class="sxs-lookup"><span data-stu-id="f8a13-585">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="f8a13-586">Die Ansichtensuche in einer Razor Page enthält den Ordner *Pages*.</span><span class="sxs-lookup"><span data-stu-id="f8a13-586">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="f8a13-587">Die Layouts, Vorlagen und Teilansichten, die Sie mit MVC-Controllern und herkömmlichen Razor-Ansichten verwenden, *funktionieren einfach so*.</span><span class="sxs-lookup"><span data-stu-id="f8a13-587">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="f8a13-588">Fügen Sie eine Datei *Pages/_ViewImports.cshtml* hinzu:</span><span class="sxs-lookup"><span data-stu-id="f8a13-588">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="f8a13-589">`@namespace` wird weiter unten im Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="f8a13-589">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="f8a13-590">Die `@addTagHelper`-Anweisung bringt die [integrierten Taghilfsprogramme](xref:mvc/views/tag-helpers/builtin-th/Index) zu allen Seiten in der Ordner *Pages*.</span><span class="sxs-lookup"><span data-stu-id="f8a13-590">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="f8a13-591">Wenn die `@namespace`-Anweisung explizit auf eine Seite angewendet wird:</span><span class="sxs-lookup"><span data-stu-id="f8a13-591">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="f8a13-592">Die Anweisung legt den Namespace für die Seite fest.</span><span class="sxs-lookup"><span data-stu-id="f8a13-592">The directive sets the namespace for the page.</span></span> <span data-ttu-id="f8a13-593">Die `@model`-Anweisung muss den Namespace nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="f8a13-593">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="f8a13-594">Wenn sich die `@namespace`-Anweisung in *_ViewImports.cshtml* befindet, stellt der angegebene Namespace das Präfix für den generierten Namespace auf der Seite bereit, die die `@namespace`-Anweisung importiert.</span><span class="sxs-lookup"><span data-stu-id="f8a13-594">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="f8a13-595">Der Rest der generierten Namespaces (der Suffixteil) ist der durch Punkte getrennte relative Pfad zwischen dem Ordner mit *_ViewImports.cshtml* und dem Ordner, der die Seite enthält.</span><span class="sxs-lookup"><span data-stu-id="f8a13-595">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="f8a13-596">Die `PageModel`-Klasse in *Pages/Customers/Edit.cshtml.cs* legt den Namespace z.B. explizit fest:</span><span class="sxs-lookup"><span data-stu-id="f8a13-596">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="f8a13-597">Die Datei *Pages/_ViewImports.cshtml* legt den folgenden Namespace fest:</span><span class="sxs-lookup"><span data-stu-id="f8a13-597">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="f8a13-598">Der generierte Namespace für die Razor Page *Pages/Customers/Edit.cshtml* ist identisch mit der `PageModel`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="f8a13-598">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="f8a13-599">`@namespace` *funktioniert auch mit konventionellen Razor-Ansichten.*</span><span class="sxs-lookup"><span data-stu-id="f8a13-599">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="f8a13-600">Die ursprüngliche Umgebungsdatei *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f8a13-600">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="f8a13-601">Die aktualisierte Umgebungsdatei *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f8a13-601">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="f8a13-602">Die [Razor Pages-Startprojekt](#rpvs17) enthält die Seite *Pages/_ValidationScriptsPartial.cshtml*, die die clientseitige Validierung bindet.</span><span class="sxs-lookup"><span data-stu-id="f8a13-602">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="f8a13-603">Weitere Informationen zu Teilansichten finden Sie unter <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="f8a13-603">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="f8a13-604">URL-Generierung für Seiten</span><span class="sxs-lookup"><span data-stu-id="f8a13-604">URL generation for Pages</span></span>

<span data-ttu-id="f8a13-605">Die zuvor gezeigte `Create`-Seite verwendet `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="f8a13-605">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="f8a13-606">Die App hat die folgende Datei/Ordner-Struktur:</span><span class="sxs-lookup"><span data-stu-id="f8a13-606">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="f8a13-607">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="f8a13-607">*/Pages*</span></span>

  * <span data-ttu-id="f8a13-608">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f8a13-608">*Index.cshtml*</span></span>
  * <span data-ttu-id="f8a13-609">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="f8a13-609">*/Customers*</span></span>

    * <span data-ttu-id="f8a13-610">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f8a13-610">*Create.cshtml*</span></span>
    * <span data-ttu-id="f8a13-611">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f8a13-611">*Edit.cshtml*</span></span>
    * <span data-ttu-id="f8a13-612">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f8a13-612">*Index.cshtml*</span></span>

<span data-ttu-id="f8a13-613">Die Seiten *Pages/Customers/Create.cshtml* und *Pages/Customers/Edit.cshtml* leiten bei Erfolg an *Pages/Index.cshtml* weiter.</span><span class="sxs-lookup"><span data-stu-id="f8a13-613">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="f8a13-614">Die Zeichenfolge `/Index` ist Teil des URI, der auf die vorhergehende Seite zugreifen soll.</span><span class="sxs-lookup"><span data-stu-id="f8a13-614">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="f8a13-615">Die Zeichenfolge `/Index` kann für das Generieren von URIs für die Seite *Pages/Index.cshtml* verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-615">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="f8a13-616">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f8a13-616">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="f8a13-617">Der Seitenname ist der Pfad zu der Seite vom Stammordner */Pages* (einschließlich eines vorangestellten `/`, z.B. `/Index`).</span><span class="sxs-lookup"><span data-stu-id="f8a13-617">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="f8a13-618">Die oben stehenden Beispiele für eine URL-Generierung bieten erweiterte Optionen und Funktionen, durch die Sie URLs nicht mehr hartcodieren müssen.</span><span class="sxs-lookup"><span data-stu-id="f8a13-618">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="f8a13-619">Bei der URL-Generierung wird [Routing](xref:mvc/controllers/routing) verwendet. Außerdem können damit Parameter generiert und entsprechend der Definition der Route im Zielpfad codiert werden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-619">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="f8a13-620">Die URL-Generierung für Seiten unterstützt relative Namen.</span><span class="sxs-lookup"><span data-stu-id="f8a13-620">URL generation for pages supports relative names.</span></span> <span data-ttu-id="f8a13-621">In der folgenden Tabelle wird dargestellt, welche Indexseite für verschiedene `RedirectToPage`-Parameter aus *Pages/Customers/Create.cshtml* ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="f8a13-621">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="f8a13-622">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="f8a13-622">RedirectToPage(x)</span></span>| <span data-ttu-id="f8a13-623">Seite</span><span class="sxs-lookup"><span data-stu-id="f8a13-623">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="f8a13-624">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="f8a13-624">RedirectToPage("/Index")</span></span> | <span data-ttu-id="f8a13-625">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="f8a13-625">*Pages/Index*</span></span> |
| <span data-ttu-id="f8a13-626">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="f8a13-626">RedirectToPage("./Index");</span></span> | <span data-ttu-id="f8a13-627">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="f8a13-627">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="f8a13-628">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="f8a13-628">RedirectToPage("../Index")</span></span> | <span data-ttu-id="f8a13-629">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="f8a13-629">*Pages/Index*</span></span> |
| <span data-ttu-id="f8a13-630">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="f8a13-630">RedirectToPage("Index")</span></span>  | <span data-ttu-id="f8a13-631">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="f8a13-631">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="f8a13-632">`RedirectToPage("Index")`, `RedirectToPage("./Index")` und `RedirectToPage("../Index")` sind *relative Namen*.</span><span class="sxs-lookup"><span data-stu-id="f8a13-632">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="f8a13-633">Der `RedirectToPage`-Parameter wird mit dem Pfad der aktuellen Seite *kombiniert*, um den Namen der Zielseite zu berechnen.</span><span class="sxs-lookup"><span data-stu-id="f8a13-633">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="f8a13-634">Das Verknüpfen relativer Namen eignet sich beim Erstellen von Websites mit einer komplexen Struktur.</span><span class="sxs-lookup"><span data-stu-id="f8a13-634">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="f8a13-635">Wenn Sie relative Namen verwenden, um Seiten in einem Ordner zu verknüpfen, können Sie diesen Ordner umbenennen.</span><span class="sxs-lookup"><span data-stu-id="f8a13-635">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="f8a13-636">Alle Links funktionieren weiterhin, da sie nicht den Namen des Ordners enthalten.</span><span class="sxs-lookup"><span data-stu-id="f8a13-636">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="f8a13-637">Um auf eine Seite in einem anderen [Bereich](xref:mvc/controllers/areas) umzuleiten, geben Sie den Bereich an:</span><span class="sxs-lookup"><span data-stu-id="f8a13-637">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="f8a13-638">Weitere Informationen finden Sie unter <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="f8a13-638">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="f8a13-639">Attribut „ViewData“</span><span class="sxs-lookup"><span data-stu-id="f8a13-639">ViewData attribute</span></span>

<span data-ttu-id="f8a13-640">Daten können mit [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute) an eine Seite übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-640">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="f8a13-641">Die Werte der Eigenschaften auf Controllern oder Razor Pages-Modellen, die mit dem `[ViewData]`-Attribut versehen sind, werden in [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) gespeichert und daraus geladen.</span><span class="sxs-lookup"><span data-stu-id="f8a13-641">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="f8a13-642">Im folgenden Beispiel enthält das `AboutModel` eine `Title`-Eigenschaft, die mit `[ViewData]` markiert ist.</span><span class="sxs-lookup"><span data-stu-id="f8a13-642">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="f8a13-643">Die Eigenschaft `Title` wird auf den Titel der Infoseite festgelegt:</span><span class="sxs-lookup"><span data-stu-id="f8a13-643">The `Title` property is set to the title of the About page:</span></span>

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

<span data-ttu-id="f8a13-644">Greifen Sie auf der Infoseite auf die Eigenschaft `Title` als Modelleigenschaft zu:</span><span class="sxs-lookup"><span data-stu-id="f8a13-644">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="f8a13-645">Im Layout wird der Titel aus dem ViewData-Wörterbuch gelesen:</span><span class="sxs-lookup"><span data-stu-id="f8a13-645">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="f8a13-646">TempData</span><span class="sxs-lookup"><span data-stu-id="f8a13-646">TempData</span></span>

<span data-ttu-id="f8a13-647">ASP.NET Core macht die Eigenschaft [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) auf einem [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller) verfügbar.</span><span class="sxs-lookup"><span data-stu-id="f8a13-647">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="f8a13-648">Diese Eigenschaft speichert Daten, bis sie gelesen wurden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-648">This property stores data until it's read.</span></span> <span data-ttu-id="f8a13-649">Die Methoden `Keep` und `Peek` können verwendet werden, um die Daten zu überprüfen, ohne sie zu löschen.</span><span class="sxs-lookup"><span data-stu-id="f8a13-649">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="f8a13-650">`TempData` eignet sich für die Weiterleitung, wenn Daten für mehr als eine Anforderung benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-650">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="f8a13-651">Im folgenden Code wird der Wert von `Message` mit `TempData` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="f8a13-651">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="f8a13-652">Das folgende Markup in der Datei *Pages/Customers/Index.cshtml* zeigt den Wert von `Message` mit `TempData` an.</span><span class="sxs-lookup"><span data-stu-id="f8a13-652">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="f8a13-653">Das Seitenmodell *Pages/Customers/Index.cshtml.cs* wendet das `[TempData]`-Attribut auf die Eigenschaft `Message` an.</span><span class="sxs-lookup"><span data-stu-id="f8a13-653">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="f8a13-654">Weitere Informationen finden Sie unter [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="f8a13-654">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="f8a13-655">Mehrere Handler pro Seite</span><span class="sxs-lookup"><span data-stu-id="f8a13-655">Multiple handlers per page</span></span>

<span data-ttu-id="f8a13-656">Die folgende Seite generiert mit dem `asp-page-handler`-Taghilfsprogramm Markup für zwei Handler:</span><span class="sxs-lookup"><span data-stu-id="f8a13-656">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="f8a13-657">Das Formular im vorherigen Beispiel hat zwei Sendeschaltflächen, und jede verwendet `FormActionTagHelper`, um an eine andere URL zu übermitteln.</span><span class="sxs-lookup"><span data-stu-id="f8a13-657">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="f8a13-658">Das `asp-page-handler`-Attribut ist eine Ergänzung für `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="f8a13-658">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="f8a13-659">`asp-page-handler` generiert URLs, die als Übermittlungsziel jeweils die durch eine Seite festgelegte Handlermethode verwenden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-659">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="f8a13-660">`asp-page` wird nicht angegeben, weil das Beispiel mit der aktuellen Seite verknüpft.</span><span class="sxs-lookup"><span data-stu-id="f8a13-660">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="f8a13-661">Das Seitenmodell:</span><span class="sxs-lookup"><span data-stu-id="f8a13-661">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="f8a13-662">Der vorherige Code verwendet *benannte Handlermethoden*.</span><span class="sxs-lookup"><span data-stu-id="f8a13-662">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="f8a13-663">Benannte Handlermethoden werden aus dem Text im Namen nach `On<HTTP Verb>` und vor `Async` (falls vorhanden) erstellt.</span><span class="sxs-lookup"><span data-stu-id="f8a13-663">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="f8a13-664">Im vorherigen Beispiel sind OnPost**JoinList**Async und OnPost**JoinListUC**Async die Seitenmethoden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-664">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="f8a13-665">Wenn Sie *OnPost* und *Async* entfernen, lauten die Handlernamen `JoinList` und `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="f8a13-665">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="f8a13-666">Mit dem vorherigen Code lautet der URL-Pfad, der an `OnPostJoinListAsync` übermittelt, `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="f8a13-666">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="f8a13-667">Der URL-Pfad, der an `OnPostJoinListUCAsync` übermittelt, lautet `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="f8a13-667">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="f8a13-668">Benutzerdefinierte Routen</span><span class="sxs-lookup"><span data-stu-id="f8a13-668">Custom routes</span></span>

<span data-ttu-id="f8a13-669">Verwenden Sie die `@page`-Anweisung für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="f8a13-669">Use the `@page` directive to:</span></span>

* <span data-ttu-id="f8a13-670">Das Angeben einer benutzerdefinierten Route zu einer Seite.</span><span class="sxs-lookup"><span data-stu-id="f8a13-670">Specify a custom route to a page.</span></span> <span data-ttu-id="f8a13-671">Die Route zur Seite „Info“ kann mit `@page "/Some/Other/Path"` beispielsweise auf `/Some/Other/Path` festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-671">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="f8a13-672">Das Anfügen von Segmenten an die Standardroute einer Seite.</span><span class="sxs-lookup"><span data-stu-id="f8a13-672">Append segments to a page's default route.</span></span> <span data-ttu-id="f8a13-673">Mit `@page "item"` kann beispielsweise ein item-Segment an die Standardroute der Seite angefügt werden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-673">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="f8a13-674">Das Anfügen von Parametern an die Standardroute einer Seite.</span><span class="sxs-lookup"><span data-stu-id="f8a13-674">Append parameters to a page's default route.</span></span> <span data-ttu-id="f8a13-675">Mit `@page "{id}"` kann beispielsweise ein ID-Parameter (`id`) für eine Seite angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="f8a13-675">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="f8a13-676">Es wird ein relativer Pfad zum Stamm unterstützt, der durch eine Tilde (`~`) festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="f8a13-676">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="f8a13-677">`@page "~/Some/Other/Path"` entspricht beispielsweise `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="f8a13-677">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="f8a13-678">Wenn Sie nicht möchten, dass die Abfragezeichenfolge `?handler=JoinList` in der URL enthalten ist, ändern Sie die Route so, dass der Handlername im Pfadteil der URL eingefügt wird.</span><span class="sxs-lookup"><span data-stu-id="f8a13-678">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="f8a13-679">Sie können die Route anpassen, indem Sie nach der `@page`-Anweisung eine Routenvorlage in doppelten Anführungszeichen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="f8a13-679">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="f8a13-680">Mit dem vorherigen Code lautet der URL-Pfad, der an `OnPostJoinListAsync` übermittelt, `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="f8a13-680">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="f8a13-681">Der URL-Pfad, der an `OnPostJoinListUCAsync` übermittelt, lautet `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="f8a13-681">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="f8a13-682">Das `?` nach `handler` bedeutet, dass der Routenparameter optional ist.</span><span class="sxs-lookup"><span data-stu-id="f8a13-682">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="f8a13-683">Konfiguration und Einstellungen</span><span class="sxs-lookup"><span data-stu-id="f8a13-683">Configuration and settings</span></span>

<span data-ttu-id="f8a13-684">Um die erweiterten Optionen zu konfigurieren, verwenden Sie die Erweiterungsmethode `AddRazorPagesOptions` auf dem MVC-Generator:</span><span class="sxs-lookup"><span data-stu-id="f8a13-684">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="f8a13-685">Derzeit können Sie `RazorPagesOptions` verwenden, um das Stammverzeichnis für Seiten festzulegen oder Anwendungsmodellkonventionen für Seiten hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="f8a13-685">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="f8a13-686">Auf diese Weise wird in Zukunft eine höhere Erweiterbarkeit erreicht.</span><span class="sxs-lookup"><span data-stu-id="f8a13-686">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="f8a13-687">Informationen zum Vorkompilieren von Ansichten finden Sie unter [Razor view compilation and precompilation in ASP.NET Core (Razor-Ansichtenkompilierung und Vorkompilierung in ASP.NET)](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="f8a13-687">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="f8a13-688">[Laden Sie Beispielcode herunter, oder zeigen Sie ihn an](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="f8a13-688">[Download or view sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="f8a13-689">Lesen Sie auch den Artikel [Erste Schritte mit Razor-Seiten](xref:tutorials/razor-pages/razor-pages-start), der auf dieser Einführung aufbaut.</span><span class="sxs-lookup"><span data-stu-id="f8a13-689">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="f8a13-690">Festlegen des Inhaltsstammverzeichnisses für Razor Pages</span><span class="sxs-lookup"><span data-stu-id="f8a13-690">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="f8a13-691">Standardmäßig lautet das Stammverzeichnis für Razor Pages */Pages*.</span><span class="sxs-lookup"><span data-stu-id="f8a13-691">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="f8a13-692">Fügen Sie [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) zu [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) hinzu, um anzugeben, dass sich Ihre Razor Pages im [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) der App befinden:</span><span class="sxs-lookup"><span data-stu-id="f8a13-692">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="f8a13-693">Festlegen eines benutzerdefinierten Stammverzeichnisses für Razor Pages</span><span class="sxs-lookup"><span data-stu-id="f8a13-693">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="f8a13-694">Fügen Sie [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) zu [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) hinzu, um anzugeben, dass Ihre Razor Pages sich in einem benutzerdefinierten Stammverzeichnis in der App befinden (geben Sie einen relativen Pfad an):</span><span class="sxs-lookup"><span data-stu-id="f8a13-694">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="f8a13-695">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f8a13-695">Additional resources</span></span>

* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
