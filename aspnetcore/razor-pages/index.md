---
title: Einführung in Razor Pages in ASP.NET Core
author: Rick-Anderson
description: Erfahren Sie, wie Razor Pages in ASP.NET Core codierungsseitige Szenarios einfacher und produktiver gestalten als MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/index
ms.openlocfilehash: 6939d285838a6dd971f530c1d65d73273b5b14e7
ms.sourcegitcommit: 69e1a79a572b0af17d08e81af12c594b7316f2e1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/15/2020
ms.locfileid: "83424568"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="66382-103">Einführung in Razor Pages in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="66382-103">Introduction to Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="66382-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="66382-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="66382-105">Razor Pages kann im Vergleich zu Controllern und Ansichten das Programmieren seitenbasierter Anwendungen vereinfachen und die Produktivität erhöhen.</span><span class="sxs-lookup"><span data-stu-id="66382-105">Razor Pages can make coding page-focused scenarios easier and more productive than using controllers and views.</span></span>

<span data-ttu-id="66382-106">Ein Tutorial, in dem der Model-View-Controller-Ansatz verwendet wird, finden Sie unter [Erste Schritte mit ASP.NET Core MVC und Visual Studio](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="66382-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="66382-107">Dieses Dokument bietet eine Einführung in Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="66382-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="66382-108">Es handelt sich nicht um ein Schritt-für-Schritt-Tutorial.</span><span class="sxs-lookup"><span data-stu-id="66382-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="66382-109">Wenn es Ihnen Probleme bereitet, die Ausführungen in einigen Abschnitten nachzuvollziehen, lesen Sie [Erste Schritte mit Razor-Seiten in ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="66382-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="66382-110">Eine Übersicht über ASP.NET Core finden Sie unter [Einführung in ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="66382-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="66382-111">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="66382-111">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66382-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66382-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="66382-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="66382-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="66382-114">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="66382-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="66382-115">Erstellen eines Razor Pages-Projekts</span><span class="sxs-lookup"><span data-stu-id="66382-115">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66382-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66382-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="66382-117">Ausführliche Informationen zum Erstellen eines Razor Pages-Projekts finden Sie unter [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="66382-117">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="66382-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="66382-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="66382-119">Führen Sie `dotnet new webapp` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="66382-119">Run `dotnet new webapp` from the command line.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="66382-120">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="66382-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="66382-121">Ausführliche Informationen zum Erstellen eines Razor Pages-Projekts finden Sie unter [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="66382-121">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="66382-122">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="66382-122">Razor Pages</span></span>

<span data-ttu-id="66382-123">Razor Pages ist in *Startup.cs* aktiviert:</span><span class="sxs-lookup"><span data-stu-id="66382-123">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

<span data-ttu-id="66382-124">Sehen Sie sich diese einfache Seite an: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="66382-124">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

<span data-ttu-id="66382-125">Der vorherige Code ähnelt sehr einer [Razor-Ansichtsdatei](xref:tutorials/first-mvc-app/adding-view), die in einer ASP.NET Core-App mit Controllern und Ansichten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="66382-125">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="66382-126">Der Unterschied besteht in der [`@page`](xref:mvc/views/razor#page)-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="66382-126">What makes it different is the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="66382-127">`@page` macht die Datei zu einer MVC-Aktion, d.h. dass Anfragen direkt ohne einen Controller verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="66382-127">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="66382-128">`@page` muss die erste Razor-Anweisung auf einer Seite sein.</span><span class="sxs-lookup"><span data-stu-id="66382-128">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="66382-129">`@page` wirkt sich auf das Verhalten aller anderen [Razor](xref:mvc/views/razor)-Konstrukte aus.</span><span class="sxs-lookup"><span data-stu-id="66382-129">`@page` affects the behavior of other [Razor](xref:mvc/views/razor) constructs.</span></span> <span data-ttu-id="66382-130">Razor Pages-Dateinamen haben das Suffix *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="66382-130">Razor Pages file names have a *.cshtml* suffix.</span></span>

<span data-ttu-id="66382-131">Eine ähnliche Seite, die die `PageModel`-Klasse verwendet, wird in den folgenden zwei Dateien angezeigt.</span><span class="sxs-lookup"><span data-stu-id="66382-131">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="66382-132">Die Datei *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="66382-132">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="66382-133">Das Seitenmodell *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="66382-133">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="66382-134">Die `PageModel`-Klassendatei hat standardmäßig den gleichen Namen wie die Datei mit Razor Pages, nur dass außerdem *cs* angefügt wird.</span><span class="sxs-lookup"><span data-stu-id="66382-134">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="66382-135">Die vorherige Datei mit Razor Pages lautet beispielsweise *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="66382-135">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="66382-136">Die Datei mit der `PageModel`-Klasse heißt *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="66382-136">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="66382-137">Die Zuordnungen von URL-Pfaden zu Seiten werden durch den Speicherort der Seite im Dateisystem bestimmt.</span><span class="sxs-lookup"><span data-stu-id="66382-137">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="66382-138">Die folgende Tabelle zeigt einen Pfad zu Razor Pages und die entsprechende URL:</span><span class="sxs-lookup"><span data-stu-id="66382-138">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="66382-139">Dateiname und Pfad</span><span class="sxs-lookup"><span data-stu-id="66382-139">File name and path</span></span>               | <span data-ttu-id="66382-140">Entsprechende URL</span><span class="sxs-lookup"><span data-stu-id="66382-140">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="66382-141">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="66382-141">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="66382-142">`/` oder `/Index`</span><span class="sxs-lookup"><span data-stu-id="66382-142">`/` or `/Index`</span></span> |
| <span data-ttu-id="66382-143">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="66382-143">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="66382-144">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="66382-144">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="66382-145">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="66382-145">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="66382-146">`/Store` oder `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="66382-146">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="66382-147">Notizen:</span><span class="sxs-lookup"><span data-stu-id="66382-147">Notes:</span></span>

* <span data-ttu-id="66382-148">Die Runtime sucht standardmäßig im Ordner *Pages* (Seiten) nach Dateien mit Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="66382-148">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="66382-149">Wenn eine Seite nicht in einer URL enthalten ist, ist `Index` die Standardseite.</span><span class="sxs-lookup"><span data-stu-id="66382-149">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="66382-150">Schreiben eines einfachen Formulars</span><span class="sxs-lookup"><span data-stu-id="66382-150">Write a basic form</span></span>

<span data-ttu-id="66382-151">Razor Pages ist darauf ausgelegt, allgemeine Muster, die mit Webbrowsern verwendet werden können, beim Erstellen einer App leichter implementieren zu können.</span><span class="sxs-lookup"><span data-stu-id="66382-151">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="66382-152">Die [Modellbindung](xref:mvc/models/model-binding), [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) und alle HTML-Hilfsprogramme *funktionieren nur* mit den Eigenschaften, die in einer Klasse der Razor Pages definiert wurden.</span><span class="sxs-lookup"><span data-stu-id="66382-152">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="66382-153">Nehmen wir z.B. eine Seite, die ein allgemeines Kontaktformular für das `Contact`-Modell implementiert:</span><span class="sxs-lookup"><span data-stu-id="66382-153">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="66382-154">Für die Beispiele in diesem Dokument wird `DbContext` in der Datei [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) initialisiert.</span><span class="sxs-lookup"><span data-stu-id="66382-154">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) file.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

<span data-ttu-id="66382-155">Das Datenmodell:</span><span class="sxs-lookup"><span data-stu-id="66382-155">The data model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="66382-156">Der db-Kontext:</span><span class="sxs-lookup"><span data-stu-id="66382-156">The db context:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

<span data-ttu-id="66382-157">Die Umgebungsdatei *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="66382-157">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="66382-158">Das Seitenmodell *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="66382-158">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="66382-159">Die `PageModel` -Klasse heißt standardmäßig `<PageName>Model` und befindet sich im selben Namespace wie die Seite.</span><span class="sxs-lookup"><span data-stu-id="66382-159">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="66382-160">Mit der Klasse `PageModel` kann die Logik einer Seite von deren Darstellung getrennt werden.</span><span class="sxs-lookup"><span data-stu-id="66382-160">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="66382-161">Sie definiert Seitenhandler für Anforderungen, die an die Seite geschickt wurden, und für zum Rendern der Seite verwendete Daten.</span><span class="sxs-lookup"><span data-stu-id="66382-161">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="66382-162">Diese Trennung ermöglicht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="66382-162">This separation allows:</span></span>

* <span data-ttu-id="66382-163">Verwalten von Seitenabhängigkeiten mithilfe von [Dependency Injection](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="66382-163">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* [<span data-ttu-id="66382-164">Komponententests</span><span class="sxs-lookup"><span data-stu-id="66382-164">Unit testing</span></span>](xref:test/razor-pages-tests)

<span data-ttu-id="66382-165">Die Seite verfügt über eine `OnPostAsync`-*Handlermethode*, die bei `POST`-Anforderungen ausgeführt wird (wenn ein Benutzer das Formular sendet).</span><span class="sxs-lookup"><span data-stu-id="66382-165">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="66382-166">Für alle HTTP-Verben können Handlermethoden hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="66382-166">Handler methods for any HTTP verb can be added.</span></span> <span data-ttu-id="66382-167">Die am häufigsten verwendeten Handler sind:</span><span class="sxs-lookup"><span data-stu-id="66382-167">The most common handlers are:</span></span>

* <span data-ttu-id="66382-168">`OnGet`, um den für eine Seite erforderlichen Status zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="66382-168">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="66382-169">Im vorangehenden Code wird die Razor-Seite *CreateModel.cshtml* durch die `OnGet`-Methode dargestellt.</span><span class="sxs-lookup"><span data-stu-id="66382-169">In the preceding code, the `OnGet` method displays the *CreateModel.cshtml* Razor Page.</span></span>
* <span data-ttu-id="66382-170">`OnPost`, um Formularübermittlungen zu behandeln</span><span class="sxs-lookup"><span data-stu-id="66382-170">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="66382-171">Das Namenssuffix `Async` ist optional. Es wird jedoch standardmäßig häufig für asynchrone Funktionen verwendet.</span><span class="sxs-lookup"><span data-stu-id="66382-171">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="66382-172">Der vorhergehende Code ist typisch für Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="66382-172">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="66382-173">Wenn Sie mit ASP.NET-Apps vertraut sind, die Controller und Ansichten verwenden, werden Ihnen folgende Fakten bekannt vorkommen:</span><span class="sxs-lookup"><span data-stu-id="66382-173">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="66382-174">Der `OnPostAsync`-Code im vorangehenden Beispiel ähnelt dem typischen Controllercode.</span><span class="sxs-lookup"><span data-stu-id="66382-174">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="66382-175">Die meisten primitiven MVC-Typen wie solche für [Modellbindungen](xref:mvc/models/model-binding), [Validierungen](xref:mvc/models/validation) und Aktionsergebnisse werden in Controllern und Razor Pages auf dieselbe Weise eingesetzt.</span><span class="sxs-lookup"><span data-stu-id="66382-175">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results work the same with Controllers and Razor Pages.</span></span> 

<span data-ttu-id="66382-176">Die vorherige `OnPostAsync`-Methode:</span><span class="sxs-lookup"><span data-stu-id="66382-176">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="66382-177">Der grundlegende Ablauf von `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="66382-177">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="66382-178">Prüfen auf Validierungsfehler</span><span class="sxs-lookup"><span data-stu-id="66382-178">Check for validation errors.</span></span>

* <span data-ttu-id="66382-179">Wenn keine Fehler vorliegen, werden die Daten gespeichert und weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="66382-179">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="66382-180">Wenn es Fehler gibt, zeigen Sie die Seite erneut mit den Validierungsmeldungen an.</span><span class="sxs-lookup"><span data-stu-id="66382-180">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="66382-181">denn Validierungsfehler werden oftmals auf dem Client erkannt und nie an den Server übermittelt.</span><span class="sxs-lookup"><span data-stu-id="66382-181">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="66382-182">Die Umgebungsdatei *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="66382-182">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

<span data-ttu-id="66382-183">Der von *Pages/Create.cshtml* gerenderte HTML-Code sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="66382-183">The rendered HTML from *Pages/Create.cshtml*:</span></span>

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

<span data-ttu-id="66382-184">Im vorherigen Code gilt für die Formularübermittlung mittels POST Folgendes:</span><span class="sxs-lookup"><span data-stu-id="66382-184">In the previous code, posting the form:</span></span>

* <span data-ttu-id="66382-185">Bei gültigen Daten:</span><span class="sxs-lookup"><span data-stu-id="66382-185">With valid data:</span></span>

  * <span data-ttu-id="66382-186">Die `OnPostAsync`-Handlermethode ruft die <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*>-Hilfsmethode auf.</span><span class="sxs-lookup"><span data-stu-id="66382-186">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> helper method.</span></span> <span data-ttu-id="66382-187">`RedirectToPage` gibt eine Instanz von <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult> zurück.</span><span class="sxs-lookup"><span data-stu-id="66382-187">`RedirectToPage` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult>.</span></span> <span data-ttu-id="66382-188">`RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="66382-188">`RedirectToPage`:</span></span>

    * <span data-ttu-id="66382-189">ist ein Aktionsergebnis.</span><span class="sxs-lookup"><span data-stu-id="66382-189">Is an action result.</span></span>
    * <span data-ttu-id="66382-190">ähnelt `RedirectToAction` oder `RedirectToRoute` (wird in Controllern und Ansichten verwendet).</span><span class="sxs-lookup"><span data-stu-id="66382-190">Is similar to `RedirectToAction` or `RedirectToRoute` (used in controllers and views).</span></span>
    * <span data-ttu-id="66382-191">ist an Seiten angepasst.</span><span class="sxs-lookup"><span data-stu-id="66382-191">Is customized for pages.</span></span> <span data-ttu-id="66382-192">Im vorhergehenden Beispiel leitet es an die Stammindexseite (`/Index`) weiter.</span><span class="sxs-lookup"><span data-stu-id="66382-192">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="66382-193">Informationen zu `RedirectToPage` finden Sie im Abschnitt [URL-Generierung für Seiten](#url_gen).</span><span class="sxs-lookup"><span data-stu-id="66382-193">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

* <span data-ttu-id="66382-194">Bei Validierungsfehlern, die an den Server übermittelt werden:</span><span class="sxs-lookup"><span data-stu-id="66382-194">With validation errors that are passed to the server:</span></span>

  * <span data-ttu-id="66382-195">Die `OnPostAsync`-Handlermethode ruft die <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*>-Hilfsmethode auf.</span><span class="sxs-lookup"><span data-stu-id="66382-195">The `OnPostAsync` handler method calls the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*> helper method.</span></span> <span data-ttu-id="66382-196">`Page` gibt eine Instanz von <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> zurück.</span><span class="sxs-lookup"><span data-stu-id="66382-196">`Page` returns an instance of <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult>.</span></span> <span data-ttu-id="66382-197">Der Vorgang, bei dem `Page` zurückgegeben wird, ähnelt dem Vorgang, bei dem Aktionen im Controller `View` zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="66382-197">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="66382-198">`PageResult` ist der Standardrückgabetyp für eine Handlermethode.</span><span class="sxs-lookup"><span data-stu-id="66382-198">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="66382-199">Eine Handlermethode, die `void` zurückgibt, rendert die Seite.</span><span class="sxs-lookup"><span data-stu-id="66382-199">A handler method that returns `void` renders the page.</span></span>
  * <span data-ttu-id="66382-200">Wenn im vorangehenden Beispiel das Formular mithilfe von POST übermittelt und dabei kein Wert angegeben wird, gibt [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) „false“ zurück.</span><span class="sxs-lookup"><span data-stu-id="66382-200">In the preceding example, posting the form with no value results in [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) returning false.</span></span> <span data-ttu-id="66382-201">In diesem Beispiel werden keine Validierungsfehler auf dem Client angezeigt.</span><span class="sxs-lookup"><span data-stu-id="66382-201">In this sample, no validation errors are displayed on the client.</span></span> <span data-ttu-id="66382-202">Die Verarbeitung von Validierungsfehlern wird weiter unten in diesem Artikel behandelt.</span><span class="sxs-lookup"><span data-stu-id="66382-202">Validation error handing is covered later in this document.</span></span>

  [!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* <span data-ttu-id="66382-203">Bei Validierungsfehlern, die durch eine clientseitige Validierung erkannt werden:</span><span class="sxs-lookup"><span data-stu-id="66382-203">With validation errors detected by client side validation:</span></span>

  * <span data-ttu-id="66382-204">Die Daten werden **nicht** per POST an den Server gesendet.</span><span class="sxs-lookup"><span data-stu-id="66382-204">Data is **not** posted to the server.</span></span>
  * <span data-ttu-id="66382-205">Die clientseitige Validierung wird weiter unten in diesem Artikel erläutert.</span><span class="sxs-lookup"><span data-stu-id="66382-205">Client-side validation is explained later in this document.</span></span>

<span data-ttu-id="66382-206">Die Eigenschaft `Customer` verwendet das [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute)-Attribut, um die Modellbindung zu aktivieren:</span><span class="sxs-lookup"><span data-stu-id="66382-206">The `Customer` property uses [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt in to model binding:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

<span data-ttu-id="66382-207">`[BindProperty]` sollte **nicht** in Modellen mit Eigenschaften verwendet werden, die vom Client nicht geändert werden dürfen.</span><span class="sxs-lookup"><span data-stu-id="66382-207">`[BindProperty]` should **not** be used on models containing properties that should not be changed by the client.</span></span> <span data-ttu-id="66382-208">Weitere Informationen finden Sie unter [Overposting](xref:data/ef-rp/crud#overposting).</span><span class="sxs-lookup"><span data-stu-id="66382-208">For more information, see [Overposting](xref:data/ef-rp/crud#overposting).</span></span>

<span data-ttu-id="66382-209">Razor Pages binden Eigenschaften standardmäßig nur an Nicht-`GET`-Verben.</span><span class="sxs-lookup"><span data-stu-id="66382-209">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="66382-210">Durch die Bindung an Eigenschaften entfällt das Schreiben von Code, mit dem HTTP-Daten in den Modelltyp konvertiert werden.</span><span class="sxs-lookup"><span data-stu-id="66382-210">Binding to properties removes the need to writing code to convert HTTP data to the model type.</span></span> <span data-ttu-id="66382-211">Die Bindung reduziert den Code mithilfe der gleichen Eigenschaft, um Formularfelder (`<input asp-for="Customer.Name">`) zu rendern und die Eingabe zu akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="66382-211">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="66382-212">Sehen Sie sich die Ansichtsdatei *Pages/Create.cshtml* an:</span><span class="sxs-lookup"><span data-stu-id="66382-212">Reviewing the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* <span data-ttu-id="66382-213">Im vorangehenden Code bindet das [Eingabetag-Hilfsprogramm](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` das HTML-Element `<input>` an den Modellausdruck `Customer.Name`.</span><span class="sxs-lookup"><span data-stu-id="66382-213">In the preceding code, the [input tag helper](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` binds the HTML `<input>` element to the `Customer.Name` model expression.</span></span>
* <span data-ttu-id="66382-214">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) stellt Taghilfsprogramme zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="66382-214">[`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) makes Tag Helpers available.</span></span>

### <a name="the-home-page"></a><span data-ttu-id="66382-215">Die Homepage</span><span class="sxs-lookup"><span data-stu-id="66382-215">The home page</span></span>

<span data-ttu-id="66382-216">*Index.cshtml* ist die Homepage:</span><span class="sxs-lookup"><span data-stu-id="66382-216">*Index.cshtml* is the home page:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

<span data-ttu-id="66382-217">Die zugeordnete `PageModel`-Klasse (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="66382-217">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="66382-218">Die Datei *Index.cshtml* enthält das folgende Markup:</span><span class="sxs-lookup"><span data-stu-id="66382-218">The *Index.cshtml* file contains the following markup:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

<span data-ttu-id="66382-219">Das [Anchor-Taghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `<a /a>` verwendet das `asp-route-{value}`-Attribut, um einen Link zur Bearbeitungsseite zu generieren.</span><span class="sxs-lookup"><span data-stu-id="66382-219">The `<a /a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="66382-220">Der Link enthält die Routendaten mit der Kontakt-ID.</span><span class="sxs-lookup"><span data-stu-id="66382-220">The link contains route data with the contact ID.</span></span> <span data-ttu-id="66382-221">Beispielsweise `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="66382-221">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="66382-222">[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) ermöglichen serverseitigem Code das Mitwirken am Erstellen und Rendern von HTML-Elementen in Razor-Dateien.</span><span class="sxs-lookup"><span data-stu-id="66382-222">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>

<span data-ttu-id="66382-223">Die Datei *Index.cshtml* enthält das Markup zum Erstellen der Schaltfläche „delete“ (Löschen) für jeden Kundenkontakt:</span><span class="sxs-lookup"><span data-stu-id="66382-223">The *Index.cshtml* file contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

<span data-ttu-id="66382-224">Der gerenderte HTML-Code sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="66382-224">The rendered HTML:</span></span>

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="66382-225">Wenn die „delete“-Schaltfläche in HTML gerendert wird, enthält das zugehörige [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction)-Element Parameter für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="66382-225">When the delete button is rendered in HTML, its [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction) includes parameters for:</span></span>

* <span data-ttu-id="66382-226">die Kundenkontakt-ID, die durch das `asp-route-id`-Attribut angegeben wird</span><span class="sxs-lookup"><span data-stu-id="66382-226">The customer contact ID, specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="66382-227">den `handler`, der durch das `asp-page-handler`-Attribut angegeben wird</span><span class="sxs-lookup"><span data-stu-id="66382-227">The `handler`, specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="66382-228">Wenn die Schaltfläche ausgewählt wird, wird eine `POST`-Anforderung an den Server gesendet.</span><span class="sxs-lookup"><span data-stu-id="66382-228">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="66382-229">Durch Konvention wird der Name der Handlermethode auf Grundlage des Werts des `handler`-Parameters gemäß dem Schema `OnPost[handler]Async` ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="66382-229">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="66382-230">Da der `handler` in diesem Beispiel `delete` ist, wird die Handlermethode `OnPostDeleteAsync` verwendet, um die `POST`-Anforderung zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="66382-230">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="66382-231">Wenn `asp-page-handler` auf einen anderen Wert (z. B. `remove`) festgelegt wird, wird eine Handlermethode namens `OnPostRemoveAsync` ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="66382-231">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="66382-232">Die `OnPostDeleteAsync`-Methode:</span><span class="sxs-lookup"><span data-stu-id="66382-232">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="66382-233">ruft die `id` der Abfragezeichenfolge ab.</span><span class="sxs-lookup"><span data-stu-id="66382-233">Gets the `id` from the query string.</span></span>
* <span data-ttu-id="66382-234">Fragt mit `FindAsync` die Datenbank nach dem Kundenkontakt ab.</span><span class="sxs-lookup"><span data-stu-id="66382-234">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="66382-235">Wenn der Kundenkontakt gefunden wird, wird er entfernt, und die Datenbank wird aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="66382-235">If the customer contact is found, it's removed and the database is updated.</span></span>
* <span data-ttu-id="66382-236">Ruft <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> auf, um die Stammindexseite (`/Index`) umzuleiten.</span><span class="sxs-lookup"><span data-stu-id="66382-236">Calls <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> to redirect to the root Index page (`/Index`).</span></span>

### <a name="the-editcshtml-file"></a><span data-ttu-id="66382-237">Die Datei „Edit.cshtml“</span><span class="sxs-lookup"><span data-stu-id="66382-237">The Edit.cshtml file</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

<span data-ttu-id="66382-238">Die erste Zeile enthält die `@page "{id:int}"`-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="66382-238">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="66382-239">Die Routingbeschränkung `"{id:int}"` weist die Seite an, die Anforderungen für die Seite zu akzeptieren, die `int`-Routingdaten enthalten.</span><span class="sxs-lookup"><span data-stu-id="66382-239">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="66382-240">Wenn eine Anforderung an die Seite bestimmte Routingdaten nicht enthält, die in einen `int` konvertiert werden können, gibt die Runtime einen Fehler vom Typ „HTTP 404: Nicht gefunden“ zurück.</span><span class="sxs-lookup"><span data-stu-id="66382-240">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="66382-241">Um die ID optional zu machen, fügen Sie `?` an die Routeneinschränkung an:</span><span class="sxs-lookup"><span data-stu-id="66382-241">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="66382-242">Die Datei *Edit.cshtml.cs* sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="66382-242">The *Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a><span data-ttu-id="66382-243">Validierung</span><span class="sxs-lookup"><span data-stu-id="66382-243">Validation</span></span>

<span data-ttu-id="66382-244">Für Validierungsregeln gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="66382-244">Validation rules:</span></span>

* <span data-ttu-id="66382-245">Sie werden deklarativ in der Modellklasse angegeben.</span><span class="sxs-lookup"><span data-stu-id="66382-245">Are declaratively specified in the model class.</span></span>
* <span data-ttu-id="66382-246">Sie werden überall in der App erzwungen.</span><span class="sxs-lookup"><span data-stu-id="66382-246">Are enforced everywhere in the app.</span></span>

<span data-ttu-id="66382-247">Der Namespace <xref:System.ComponentModel.DataAnnotations> stellt eine Gruppe integrierter Validierungsattribute bereit, die deklarativ auf eine Klasse oder Eigenschaft angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="66382-247">The <xref:System.ComponentModel.DataAnnotations> namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="66382-248">„DataAnnotations“ enthält auch Formatierungsattribute wie [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute), die bei der Formatierung helfen und keinerlei Validierung bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="66382-248">DataAnnotations also contains formatting attributes like [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="66382-249">Sehen Sie sich das `Customer`-Modell an:</span><span class="sxs-lookup"><span data-stu-id="66382-249">Consider the `Customer` model:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

<span data-ttu-id="66382-250">Für die folgende Ansichtsdatei *Create.cshtml* gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="66382-250">Using the following *Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

<span data-ttu-id="66382-251">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="66382-251">The preceding code:</span></span>

* <span data-ttu-id="66382-252">umfasst jQuery-Skripts einschließlich solcher zur Validierung.</span><span class="sxs-lookup"><span data-stu-id="66382-252">Includes jQuery and jQuery validation scripts.</span></span>
* <span data-ttu-id="66382-253">Verwendet die [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) `<div />` und `<span />`, um Folgendes zu ermöglichen:</span><span class="sxs-lookup"><span data-stu-id="66382-253">Uses the `<div />` and `<span />` [Tag Helpers](xref:mvc/views/tag-helpers/intro) to enable:</span></span>

  * <span data-ttu-id="66382-254">clientseitiger Validierung.</span><span class="sxs-lookup"><span data-stu-id="66382-254">Client-side validation.</span></span>
  * <span data-ttu-id="66382-255">Rendering von Validierungsfehlern.</span><span class="sxs-lookup"><span data-stu-id="66382-255">Validation error rendering.</span></span>

* <span data-ttu-id="66382-256">wird der folgende HTML-Code generiert:</span><span class="sxs-lookup"><span data-stu-id="66382-256">Generates the following HTML:</span></span>

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

<span data-ttu-id="66382-257">Wenn Sie das Formular „Create“ (Erstellen) ohne einen Wert für den Namen mit POST übermitteln, wird die Fehlermeldung „The Name field is required.“ (Für das Namensfeld muss ein Wert angegeben werden.)</span><span class="sxs-lookup"><span data-stu-id="66382-257">Posting the Create form without a name value displays the error message "The Name field is required."</span></span> <span data-ttu-id="66382-258">auf dem Formular angezeigt.</span><span class="sxs-lookup"><span data-stu-id="66382-258">on the form.</span></span> <span data-ttu-id="66382-259">Wenn JavaScript auf dem Client aktiviert ist, zeigt der Browser den Fehler an, ohne dass Daten per POST an den Server gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="66382-259">If JavaScript is enabled on the client, the browser displays the error without posting to the server.</span></span>

<span data-ttu-id="66382-260">Das `[StringLength(10)]`-Attribut generiert `data-val-length-max="10"` für den gerenderten HTML-Code.</span><span class="sxs-lookup"><span data-stu-id="66382-260">The `[StringLength(10)]` attribute generates `data-val-length-max="10"` on the rendered HTML.</span></span> <span data-ttu-id="66382-261">`data-val-length-max` verhindert, dass im Browser ein Wert eingegeben wird, der die angegebene Maximallänge überschreitet.</span><span class="sxs-lookup"><span data-stu-id="66382-261">`data-val-length-max` prevents browsers from entering more than the maximum length specified.</span></span> <span data-ttu-id="66382-262">Wenn ein Tool wie [Fiddler](https://www.telerik.com/fiddler) zum Bearbeiten und erneuten Senden einer POST-Anforderung verwendet wird</span><span class="sxs-lookup"><span data-stu-id="66382-262">If a tool such as [Fiddler](https://www.telerik.com/fiddler) is used to edit and replay the post:</span></span>

* <span data-ttu-id="66382-263">und die Länge des Namens 10 Zeichen überschreitet,</span><span class="sxs-lookup"><span data-stu-id="66382-263">With the name longer than 10.</span></span>
* <span data-ttu-id="66382-264">geschieht Folgendes: Die Fehlermeldung „The field Name must be a string with a maximum length of 10.“ (Das Namensfeld muss eine Zeichenfolge mit maximal 10 Zeichen enthalten.)</span><span class="sxs-lookup"><span data-stu-id="66382-264">The error message "The field Name must be a string with a maximum length of 10."</span></span> <span data-ttu-id="66382-265">wird zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="66382-265">is returned.</span></span>

<span data-ttu-id="66382-266">Sehen Sie sich das folgende `Movie`-Modell an:</span><span class="sxs-lookup"><span data-stu-id="66382-266">Consider the following `Movie` model:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="66382-267">Die Validierungsattribute geben das Verhalten an, das für die Modelleigenschaften erzwungen werden soll:</span><span class="sxs-lookup"><span data-stu-id="66382-267">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="66382-268">Die Attribute `Required` und `MinimumLength` geben an, dass eine Eigenschaft einen Wert haben muss. Ein Benutzer kann allerdings ein Leerzeichen eingeben, um diese Anforderung zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="66382-268">The `Required` and `MinimumLength` attributes indicate that a property must have a value, but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="66382-269">Das Attribut `RegularExpression` wird verwendet, um einzuschränken, welche Zeichen eingegeben werden dürfen.</span><span class="sxs-lookup"><span data-stu-id="66382-269">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="66382-270">Für „Genre“ im Code oben gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="66382-270">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="66382-271">Es dürfen nur Buchstaben enthalten sein.</span><span class="sxs-lookup"><span data-stu-id="66382-271">Must only use letters.</span></span>
  * <span data-ttu-id="66382-272">Der erste Buchstabe muss ein Großbuchstabe sein.</span><span class="sxs-lookup"><span data-stu-id="66382-272">The first letter is required to be uppercase.</span></span> <span data-ttu-id="66382-273">Leerzeichen, Zahlen und Sonderzeichen sind nicht zulässig.</span><span class="sxs-lookup"><span data-stu-id="66382-273">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="66382-274">Für `RegularExpression`-„Rating“ (Bewertung) gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="66382-274">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="66382-275">Das erste Zeichen muss ein Großbuchstabe sein.</span><span class="sxs-lookup"><span data-stu-id="66382-275">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="66382-276">Sonderzeichen und Zahlen sind als darauffolgende Zeichen zulässig.</span><span class="sxs-lookup"><span data-stu-id="66382-276">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="66382-277">„PG-13“ ist als Bewertung („Rating“) gültig, nicht jedoch als „Genre“.</span><span class="sxs-lookup"><span data-stu-id="66382-277">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="66382-278">Das Attribut `Range` schränkt einen Wert auf einen bestimmten Bereich ein.</span><span class="sxs-lookup"><span data-stu-id="66382-278">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="66382-279">Mit dem Attribut `StringLength` kann die maximale Länge einer Zeichenfolgeneigenschaft und optional die minimale Länge festlegt werden.</span><span class="sxs-lookup"><span data-stu-id="66382-279">The `StringLength` attribute sets the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="66382-280">Werttypen (wie `decimal`, `int`, `float`, `DateTime`) sind grundsätzlich erforderlich und benötigen nicht das Attribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="66382-280">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="66382-281">Auf der Seite „Create“ (Erstellen) für das `Movie`-Modell werden ungültige Werte und die daraus resultierenden Fehler angezeigt:</span><span class="sxs-lookup"><span data-stu-id="66382-281">The Create page for the `Movie` model shows displays errors with invalid values:</span></span>

![Ansichtsformular „Movie“ mit mehreren clientseitigen jQuery-Validierungsfehlern](~/tutorials/razor-pages/validation/_static/val.png)

<span data-ttu-id="66382-283">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="66382-283">For more information, see:</span></span>

* [<span data-ttu-id="66382-284">Hinzufügen von Validierungscode zur Movie-App</span><span class="sxs-lookup"><span data-stu-id="66382-284">Add validation to the Movie app</span></span>](xref:tutorials/razor-pages/validation)
* <span data-ttu-id="66382-285">[Modellvalidierung in ASP.NET Core](xref:mvc/models/validation)</span><span class="sxs-lookup"><span data-stu-id="66382-285">[Model validation in ASP.NET Core](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="66382-286">Verarbeiten von HEAD-Anforderungen mit einem OnGet-Handlerfallback</span><span class="sxs-lookup"><span data-stu-id="66382-286">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="66382-287">`HEAD`-Anforderungen ermöglichen das Abrufen der Header für eine bestimmte Ressource.</span><span class="sxs-lookup"><span data-stu-id="66382-287">`HEAD` requests allow retrieving the headers for a specific resource.</span></span> <span data-ttu-id="66382-288">Im Gegensatz zu `GET`-Anforderungen geben `HEAD`-Anforderungen keinen Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="66382-288">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="66382-289">Normalerweise wird ein `OnHead`-Handler erstellt und für `HEAD`-Anforderungen aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="66382-289">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="66382-290">Razor Pages ruft den `OnGet`-Handler auf, wenn kein `OnHead`-Handler definiert ist.</span><span class="sxs-lookup"><span data-stu-id="66382-290">Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span>

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="66382-291">XSRF/CSRF und Razor Pages</span><span class="sxs-lookup"><span data-stu-id="66382-291">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="66382-292">Razor Pages wird durch [Validierungsmaßnahmen vor XSRF/CSRF-Angriffen geschützt](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="66382-292">Razor Pages are protected by [Antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="66382-293">Das [Formulartag-Hilfsprogramm](xref:mvc/views/working-with-forms#the-form-tag-helper) injiziert Anti-XSRF/CSRF-Token in HTML-Formularelemente.</span><span class="sxs-lookup"><span data-stu-id="66382-293">The [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="66382-294">Verwenden von Layouts, Teilansichten, Vorlagen und Taghilfsprogrammen mit Razor Pages</span><span class="sxs-lookup"><span data-stu-id="66382-294">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="66382-295">Razor Pages beinhaltet alle Funktionen der Razor-Anzeige-Engine.</span><span class="sxs-lookup"><span data-stu-id="66382-295">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="66382-296">Layouts, Teilansichten, Vorlagen, Taghilfsprogramme, *_ViewStart.cshtml* und *_ViewImports.cshtml* funktionieren auf die gleiche Weise wie für herkömmliche Razor-Ansichten.</span><span class="sxs-lookup"><span data-stu-id="66382-296">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, and *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="66382-297">Strukturieren Sie diese Seite mit einigen dieser praktischen Funktionen.</span><span class="sxs-lookup"><span data-stu-id="66382-297">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="66382-298">Fügen Sie der *Pages/Shared/_Layout.cshtml* eine [Layoutseite](xref:mvc/views/layout) hinzu:</span><span class="sxs-lookup"><span data-stu-id="66382-298">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

<span data-ttu-id="66382-299">Das [Layout](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="66382-299">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="66382-300">Steuert das Layout der einzelnen Seiten, es sei denn, das Layout wird für eine Seite deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="66382-300">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="66382-301">Importiert HTML-Strukturen, z.B. JavaScript und Stylesheets.</span><span class="sxs-lookup"><span data-stu-id="66382-301">Imports HTML structures such as JavaScript and stylesheets.</span></span>
* <span data-ttu-id="66382-302">Der Inhalt der Razor-Seite wird gerendert, wenn `@RenderBody()` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="66382-302">The contents of the Razor page are rendered where `@RenderBody()` is called.</span></span>

<span data-ttu-id="66382-303">Weitere Informationen finden Sie unter [Layoutseite](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="66382-303">For more information, see [layout page](xref:mvc/views/layout).</span></span>

<span data-ttu-id="66382-304">Die Eigenschaft [Layout](xref:mvc/views/layout#specifying-a-layout) wird in *Pages/_ViewStart.cshtml* festgelegt:</span><span class="sxs-lookup"><span data-stu-id="66382-304">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="66382-305">Das Layout befindet sich im Ordner *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="66382-305">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="66382-306">Seiten suchen hierarchisch nach anderen Ansichten (Layouts, Vorlagen oder Teilansichten) und beginnen im gleichen Ordner wie die aktuelle Seite.</span><span class="sxs-lookup"><span data-stu-id="66382-306">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="66382-307">Ein Layout im Ordner *Pages/Shared* kann von jeder Razor-Seite aus unter dem Ordner *Pages* verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="66382-307">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="66382-308">Die Layoutdatei sollte im Ordner *Pages/Shared* gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="66382-308">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="66382-309">Wir empfehlen Ihnen, die Layoutdatei **nicht** im Ordner *Views/Shared* (Ansichten/Freigegeben) zu platzieren.</span><span class="sxs-lookup"><span data-stu-id="66382-309">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="66382-310">*Views/Shared* ist ein MVC-Ansichtsmuster.</span><span class="sxs-lookup"><span data-stu-id="66382-310">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="66382-311">Razor Pages basieren auf der Ordnerhierarchie, nicht auf Pfadkonventionen.</span><span class="sxs-lookup"><span data-stu-id="66382-311">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="66382-312">Die Ansichtensuche in einer Razor Page enthält den Ordner *Pages*.</span><span class="sxs-lookup"><span data-stu-id="66382-312">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="66382-313">Die Layouts, Vorlagen und Teilansichten, die mit MVC-Controllern und herkömmlichen Razor-Ansichten verwendet werden, *funktionieren problemlos*.</span><span class="sxs-lookup"><span data-stu-id="66382-313">The layouts, templates, and partials used with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="66382-314">Fügen Sie eine Datei *Pages/_ViewImports.cshtml* hinzu:</span><span class="sxs-lookup"><span data-stu-id="66382-314">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="66382-315">`@namespace` wird weiter unten im Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="66382-315">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="66382-316">Die `@addTagHelper`-Anweisung bringt die [integrierten Taghilfsprogramme](xref:mvc/views/tag-helpers/builtin-th/Index) zu allen Seiten in der Ordner *Pages*.</span><span class="sxs-lookup"><span data-stu-id="66382-316">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="66382-317">Die `@namespace`-Anweisung wird wie folgt für eine Seite festgelegt:</span><span class="sxs-lookup"><span data-stu-id="66382-317">The `@namespace` directive set on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="66382-318">Die `@namespace`-Anweisung legt den Namespace für die Seite fest.</span><span class="sxs-lookup"><span data-stu-id="66382-318">The `@namespace` directive sets the namespace for the page.</span></span> <span data-ttu-id="66382-319">Die `@model`-Anweisung muss den Namespace nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="66382-319">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="66382-320">Wenn sich die `@namespace`-Anweisung in *_ViewImports.cshtml* befindet, stellt der angegebene Namespace das Präfix für den generierten Namespace auf der Seite bereit, die die `@namespace`-Anweisung importiert.</span><span class="sxs-lookup"><span data-stu-id="66382-320">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="66382-321">Der Rest der generierten Namespaces (der Suffixteil) ist der durch Punkte getrennte relative Pfad zwischen dem Ordner mit *_ViewImports.cshtml* und dem Ordner, der die Seite enthält.</span><span class="sxs-lookup"><span data-stu-id="66382-321">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="66382-322">Die `PageModel`-Klasse in *Pages/Customers/Edit.cshtml.cs* legt den Namespace z.B. explizit fest:</span><span class="sxs-lookup"><span data-stu-id="66382-322">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="66382-323">Die Datei *Pages/_ViewImports.cshtml* legt den folgenden Namespace fest:</span><span class="sxs-lookup"><span data-stu-id="66382-323">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="66382-324">Der generierte Namespace für die Razor Page *Pages/Customers/Edit.cshtml* ist identisch mit der `PageModel`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="66382-324">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="66382-325">`@namespace` *funktioniert auch mit konventionellen Razor-Ansichten.*</span><span class="sxs-lookup"><span data-stu-id="66382-325">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="66382-326">Sehen Sie sich die Ansichtsdatei *Pages/Create.cshtml* an:</span><span class="sxs-lookup"><span data-stu-id="66382-326">Consider the *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

<span data-ttu-id="66382-327">Die aktualisierte Ansichtsdatei *Pages/Create.cshtml* mit *_ViewImports.cshtml* und der vorherigen Layoutdatei sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="66382-327">The updated *Pages/Create.cshtml* view file with *_ViewImports.cshtml* and the preceding layout file:</span></span>

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

<span data-ttu-id="66382-328">Im vorangehenden Code werden von *_ViewImports.cshtml* der Namespace und die Taghilfsprogramme importiert.</span><span class="sxs-lookup"><span data-stu-id="66382-328">In the preceding code, the *_ViewImports.cshtml* imported the namespace and Tag Helpers.</span></span> <span data-ttu-id="66382-329">Die JavaScript-Dateien werden von der Layoutdatei importiert.</span><span class="sxs-lookup"><span data-stu-id="66382-329">The layout file imported the JavaScript files.</span></span>

<span data-ttu-id="66382-330">Die [Razor Pages-Startprojekt](#rpvs17) enthält die Seite *Pages/_ValidationScriptsPartial.cshtml*, die die clientseitige Validierung bindet.</span><span class="sxs-lookup"><span data-stu-id="66382-330">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="66382-331">Weitere Informationen zu Teilansichten finden Sie unter <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="66382-331">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="66382-332">URL-Generierung für Seiten</span><span class="sxs-lookup"><span data-stu-id="66382-332">URL generation for Pages</span></span>

<span data-ttu-id="66382-333">Die zuvor gezeigte `Create`-Seite verwendet `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="66382-333">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

<span data-ttu-id="66382-334">Die App hat die folgende Datei/Ordner-Struktur:</span><span class="sxs-lookup"><span data-stu-id="66382-334">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="66382-335">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="66382-335">*/Pages*</span></span>

  * <span data-ttu-id="66382-336">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="66382-336">*Index.cshtml*</span></span>
  * <span data-ttu-id="66382-337">*Privacy.cshtml*</span><span class="sxs-lookup"><span data-stu-id="66382-337">*Privacy.cshtml*</span></span>
  * <span data-ttu-id="66382-338">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="66382-338">*/Customers*</span></span>

    * <span data-ttu-id="66382-339">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="66382-339">*Create.cshtml*</span></span>
    * <span data-ttu-id="66382-340">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="66382-340">*Edit.cshtml*</span></span>
    * <span data-ttu-id="66382-341">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="66382-341">*Index.cshtml*</span></span>

<span data-ttu-id="66382-342">Die Seiten *Pages/Customers/Create.cshtml* und *Pages/Customers/Edit.cshtml* führen bei Erfolg eine Umleitung zu *Pages/Customers/Index.cshtml* durch.</span><span class="sxs-lookup"><span data-stu-id="66382-342">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Customers/Index.cshtml* after success.</span></span> <span data-ttu-id="66382-343">Die Zeichenfolge `./Index` stellt einen relativen Seitennamen dar, der für den Zugriff auf die vorherige Seite verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="66382-343">The string `./Index` is a relative page name used to access the preceding page.</span></span> <span data-ttu-id="66382-344">Sie wird für das Generieren von URIs für die Seite *Pages/Customers/Index.cshtml* verwendet.</span><span class="sxs-lookup"><span data-stu-id="66382-344">It is used to generate URLs to the *Pages/Customers/Index.cshtml* page.</span></span> <span data-ttu-id="66382-345">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66382-345">For example:</span></span>

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

<span data-ttu-id="66382-346">Der absolute Seitenname `/Index` wird zum Generieren der URLs für die Seite *Pages/Index.cshtml* verwendet.</span><span class="sxs-lookup"><span data-stu-id="66382-346">The absolute page name `/Index` is used to generate URLs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="66382-347">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66382-347">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="66382-348">Der Seitenname ist der Pfad zu der Seite vom Stammordner */Pages* (einschließlich eines vorangestellten `/`, z.B. `/Index`).</span><span class="sxs-lookup"><span data-stu-id="66382-348">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="66382-349">Die oben stehenden Beispiele für eine URL-Generierung bieten erweiterte Optionen und Funktionen, durch die Sie URLs nicht mehr hartcodieren müssen.</span><span class="sxs-lookup"><span data-stu-id="66382-349">The preceding URL generation samples offer enhanced options and functional capabilities over hard-coding a URL.</span></span> <span data-ttu-id="66382-350">Bei der URL-Generierung wird [Routing](xref:mvc/controllers/routing) verwendet. Außerdem können damit Parameter generiert und entsprechend der Definition der Route im Zielpfad codiert werden.</span><span class="sxs-lookup"><span data-stu-id="66382-350">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="66382-351">Die URL-Generierung für Seiten unterstützt relative Namen.</span><span class="sxs-lookup"><span data-stu-id="66382-351">URL generation for pages supports relative names.</span></span> <span data-ttu-id="66382-352">In der folgenden Tabelle wird dargestellt, welche Indexseite durch verschiedene `RedirectToPage`-Parameter in *Pages/Customers/Create.cshtml* ausgewählt wird.</span><span class="sxs-lookup"><span data-stu-id="66382-352">The following table shows which Index page is selected using different `RedirectToPage` parameters in *Pages/Customers/Create.cshtml*.</span></span>

| <span data-ttu-id="66382-353">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="66382-353">RedirectToPage(x)</span></span>| <span data-ttu-id="66382-354">Seite</span><span class="sxs-lookup"><span data-stu-id="66382-354">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="66382-355">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="66382-355">RedirectToPage("/Index")</span></span> | <span data-ttu-id="66382-356">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="66382-356">*Pages/Index*</span></span> |
| <span data-ttu-id="66382-357">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="66382-357">RedirectToPage("./Index");</span></span> | <span data-ttu-id="66382-358">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="66382-358">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="66382-359">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="66382-359">RedirectToPage("../Index")</span></span> | <span data-ttu-id="66382-360">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="66382-360">*Pages/Index*</span></span> |
| <span data-ttu-id="66382-361">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="66382-361">RedirectToPage("Index")</span></span>  | <span data-ttu-id="66382-362">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="66382-362">*Pages/Customers/Index*</span></span> |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

<span data-ttu-id="66382-363">`RedirectToPage("Index")`, `RedirectToPage("./Index")` und `RedirectToPage("../Index")` sind *relative Namen*.</span><span class="sxs-lookup"><span data-stu-id="66382-363">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")` are *relative names*.</span></span> <span data-ttu-id="66382-364">Der `RedirectToPage`-Parameter wird mit dem Pfad der aktuellen Seite *kombiniert*, um den Namen der Zielseite zu berechnen.</span><span class="sxs-lookup"><span data-stu-id="66382-364">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>

<span data-ttu-id="66382-365">Das Verknüpfen relativer Namen eignet sich beim Erstellen von Websites mit einer komplexen Struktur.</span><span class="sxs-lookup"><span data-stu-id="66382-365">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="66382-366">Wenn durch relative Namen Seiten in einem Ordner verknüpft werden, hat das folgende Vorteile:</span><span class="sxs-lookup"><span data-stu-id="66382-366">When relative names are used to link between pages in a folder:</span></span>

* <span data-ttu-id="66382-367">Relative Links funktionieren weiterhin, wenn ein Ordner umbenannt wird.</span><span class="sxs-lookup"><span data-stu-id="66382-367">Renaming a folder doesn't break the relative links.</span></span>
* <span data-ttu-id="66382-368">Links funktionieren weiterhin, da sie keinen Ordnernamen enthalten.</span><span class="sxs-lookup"><span data-stu-id="66382-368">Links are not broken because they don't include the folder name.</span></span>

<span data-ttu-id="66382-369">Um auf eine Seite in einem anderen [Bereich](xref:mvc/controllers/areas) umzuleiten, geben Sie den Bereich an:</span><span class="sxs-lookup"><span data-stu-id="66382-369">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="66382-370">Weitere Informationen finden Sie unter <xref:mvc/controllers/areas> und <xref:razor-pages/razor-pages-conventions>.</span><span class="sxs-lookup"><span data-stu-id="66382-370">For more information, see <xref:mvc/controllers/areas> and <xref:razor-pages/razor-pages-conventions>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="66382-371">Attribut „ViewData“</span><span class="sxs-lookup"><span data-stu-id="66382-371">ViewData attribute</span></span>

<span data-ttu-id="66382-372">Daten können mit <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute> an eine Seite übermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="66382-372">Data can be passed to a page with <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute>.</span></span> <span data-ttu-id="66382-373">Für Eigenschaften mit dem `[ViewData]`-Attribut werden die Werte in <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> gespeichert und daraus geladen.</span><span class="sxs-lookup"><span data-stu-id="66382-373">Properties with the `[ViewData]` attribute have their values stored and loaded from the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary>.</span></span>

<span data-ttu-id="66382-374">Im folgenden Beispiel wendet `AboutModel` das `[ViewData]`-Attribut auf die `Title`-Eigenschaft an:</span><span class="sxs-lookup"><span data-stu-id="66382-374">In the following example, the `AboutModel` applies the `[ViewData]` attribute to the `Title` property:</span></span>

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

<span data-ttu-id="66382-375">Greifen Sie auf der Infoseite auf die Eigenschaft `Title` als Modelleigenschaft zu:</span><span class="sxs-lookup"><span data-stu-id="66382-375">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="66382-376">Im Layout wird der Titel aus dem ViewData-Wörterbuch gelesen:</span><span class="sxs-lookup"><span data-stu-id="66382-376">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="66382-377">TempData</span><span class="sxs-lookup"><span data-stu-id="66382-377">TempData</span></span>

<span data-ttu-id="66382-378">ASP.NET Core macht <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> verfügbar.</span><span class="sxs-lookup"><span data-stu-id="66382-378">ASP.NET Core exposes the <xref:Microsoft.AspNetCore.Mvc.Controller.TempData>.</span></span> <span data-ttu-id="66382-379">Diese Eigenschaft speichert Daten, bis sie gelesen wurden.</span><span class="sxs-lookup"><span data-stu-id="66382-379">This property stores data until it's read.</span></span> <span data-ttu-id="66382-380">Die Methoden <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> und <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> können verwendet werden, um die Daten zu überprüfen, ohne sie zu löschen.</span><span class="sxs-lookup"><span data-stu-id="66382-380">The <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> and <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="66382-381">`TempData` eignet sich für die Umleitung, wenn Daten für mehr als eine Anforderung benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="66382-381">`TempData` is useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="66382-382">Im folgenden Code wird der Wert von `Message` mit `TempData` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="66382-382">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="66382-383">Das folgende Markup in der Datei *Pages/Customers/Index.cshtml* zeigt den Wert von `Message` mit `TempData` an.</span><span class="sxs-lookup"><span data-stu-id="66382-383">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="66382-384">Das Seitenmodell *Pages/Customers/Index.cshtml.cs* wendet das `[TempData]`-Attribut auf die Eigenschaft `Message` an.</span><span class="sxs-lookup"><span data-stu-id="66382-384">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="66382-385">Weitere Informationen finden Sie unter [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="66382-385">For more information, see [TempData](xref:fundamentals/app-state#tempdata).</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="66382-386">Mehrere Handler pro Seite</span><span class="sxs-lookup"><span data-stu-id="66382-386">Multiple handlers per page</span></span>

<span data-ttu-id="66382-387">Die folgende Seite generiert mit dem `asp-page-handler`-Taghilfsprogramm Markup für zwei Handler:</span><span class="sxs-lookup"><span data-stu-id="66382-387">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<span data-ttu-id="66382-388">Das Formular im vorherigen Beispiel hat zwei Sendeschaltflächen, und jede verwendet `FormActionTagHelper`, um an eine andere URL zu übermitteln.</span><span class="sxs-lookup"><span data-stu-id="66382-388">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="66382-389">Das `asp-page-handler`-Attribut ist eine Ergänzung für `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="66382-389">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="66382-390">`asp-page-handler` generiert URLs, die als Übermittlungsziel jeweils die durch eine Seite festgelegte Handlermethode verwenden.</span><span class="sxs-lookup"><span data-stu-id="66382-390">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="66382-391">`asp-page` wird nicht angegeben, weil das Beispiel mit der aktuellen Seite verknüpft.</span><span class="sxs-lookup"><span data-stu-id="66382-391">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="66382-392">Das Seitenmodell:</span><span class="sxs-lookup"><span data-stu-id="66382-392">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="66382-393">Der vorherige Code verwendet *benannte Handlermethoden*.</span><span class="sxs-lookup"><span data-stu-id="66382-393">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="66382-394">Benannte Handlermethoden werden aus dem Text im Namen nach `On<HTTP Verb>` und vor `Async` (falls vorhanden) erstellt.</span><span class="sxs-lookup"><span data-stu-id="66382-394">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="66382-395">Im vorherigen Beispiel sind OnPost**JoinList**Async und OnPost**JoinListUC**Async die Seitenmethoden.</span><span class="sxs-lookup"><span data-stu-id="66382-395">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="66382-396">Wenn Sie *OnPost* und *Async* entfernen, lauten die Handlernamen `JoinList` und `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="66382-396">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="66382-397">Mit dem vorherigen Code lautet der URL-Pfad, der an `OnPostJoinListAsync` übermittelt, `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="66382-397">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="66382-398">Der URL-Pfad, der an `OnPostJoinListUCAsync` übermittelt, lautet `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="66382-398">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="66382-399">Benutzerdefinierte Routen</span><span class="sxs-lookup"><span data-stu-id="66382-399">Custom routes</span></span>

<span data-ttu-id="66382-400">Verwenden Sie die `@page`-Anweisung für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="66382-400">Use the `@page` directive to:</span></span>

* <span data-ttu-id="66382-401">Das Angeben einer benutzerdefinierten Route zu einer Seite.</span><span class="sxs-lookup"><span data-stu-id="66382-401">Specify a custom route to a page.</span></span> <span data-ttu-id="66382-402">Die Route zur Seite „Info“ kann mit `@page "/Some/Other/Path"` beispielsweise auf `/Some/Other/Path` festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="66382-402">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="66382-403">Das Anfügen von Segmenten an die Standardroute einer Seite.</span><span class="sxs-lookup"><span data-stu-id="66382-403">Append segments to a page's default route.</span></span> <span data-ttu-id="66382-404">Mit `@page "item"` kann beispielsweise ein item-Segment an die Standardroute der Seite angefügt werden.</span><span class="sxs-lookup"><span data-stu-id="66382-404">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="66382-405">Das Anfügen von Parametern an die Standardroute einer Seite.</span><span class="sxs-lookup"><span data-stu-id="66382-405">Append parameters to a page's default route.</span></span> <span data-ttu-id="66382-406">Mit `@page "{id}"` kann beispielsweise ein ID-Parameter (`id`) für eine Seite angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="66382-406">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="66382-407">Es wird ein relativer Pfad zum Stamm unterstützt, der durch eine Tilde (`~`) festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="66382-407">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="66382-408">`@page "~/Some/Other/Path"` entspricht beispielsweise `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="66382-408">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="66382-409">Wenn Sie nicht möchten, dass die Abfragezeichenfolge `?handler=JoinList` in der URL enthalten ist, ändern Sie die Route so, dass der Handlername im Pfadteil der URL eingefügt wird.</span><span class="sxs-lookup"><span data-stu-id="66382-409">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="66382-410">Sie können die Route anpassen, indem Sie nach der `@page`-Anweisung eine Routenvorlage in doppelten Anführungszeichen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="66382-410">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="66382-411">Mit dem vorherigen Code lautet der URL-Pfad, der an `OnPostJoinListAsync` übermittelt, `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="66382-411">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="66382-412">Der URL-Pfad, der an `OnPostJoinListUCAsync` übermittelt, lautet `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="66382-412">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="66382-413">Das `?` nach `handler` bedeutet, dass der Routenparameter optional ist.</span><span class="sxs-lookup"><span data-stu-id="66382-413">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="advanced-configuration-and-settings"></a><span data-ttu-id="66382-414">Erweiterte Konfigurationen und Einstellungen</span><span class="sxs-lookup"><span data-stu-id="66382-414">Advanced configuration and settings</span></span>

<span data-ttu-id="66382-415">Die Konfigurationen und Einstellungen in den folgenden Abschnitten sind für die meisten Apps nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="66382-415">The configuration and settings in following sections is not required by most apps.</span></span>

<span data-ttu-id="66382-416">Verwenden Sie die Erweiterungsmethode <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>, um die erweiterten Optionen zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="66382-416">To configure advanced options, use the extension method <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

<span data-ttu-id="66382-417">Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>, um das Stammverzeichnis für Seiten festzulegen oder Anwendungsmodellkonventionen für Seiten hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="66382-417">Use the <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="66382-418">Weitere Informationen zu Konventionen finden Sie unter [Razor Pages-Autorisierungskonventionen](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="66382-418">For more information on conventions, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization).</span></span>

<span data-ttu-id="66382-419">Informationen zum Vorkompilieren von Ansichten finden Sie unter [Razor-Ansichtenkompilierung](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="66382-419">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation).</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="66382-420">Festlegen des Inhaltsstammverzeichnisses für Razor Pages</span><span class="sxs-lookup"><span data-stu-id="66382-420">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="66382-421">Standardmäßig lautet das Stammverzeichnis für Razor Pages */Pages*.</span><span class="sxs-lookup"><span data-stu-id="66382-421">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="66382-422">Fügen Sie <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> hinzu, um anzugeben, dass sich Ihre Razor Pages im [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) der App befinden:</span><span class="sxs-lookup"><span data-stu-id="66382-422">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) of the app:</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="66382-423">Festlegen eines benutzerdefinierten Stammverzeichnisses für Razor Pages</span><span class="sxs-lookup"><span data-stu-id="66382-423">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="66382-424">Fügen Sie <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> hinzu, um anzugeben, dass sich Ihre Razor-Seiten in einem benutzerdefinierten Stammverzeichnis der App befinden. Geben Sie dabei einen relativen Pfad an:</span><span class="sxs-lookup"><span data-stu-id="66382-424">Add <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> to specify that Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

[!code-cs[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="66382-425">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="66382-425">Additional resources</span></span>

* <span data-ttu-id="66382-426">Lesen Sie auch den Artikel [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start), der auf dieser Einführung aufbaut.</span><span class="sxs-lookup"><span data-stu-id="66382-426">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction</span></span>
* [<span data-ttu-id="66382-427">Herunterladen und Anzeigen des Beispielcodes</span><span class="sxs-lookup"><span data-stu-id="66382-427">Download or view sample code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
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

<span data-ttu-id="66382-428">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="66382-428">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="66382-429">Razor Pages ist ein neuer Bestandteil von ASP.NET Core MVC, mit dem codierungsseitige Szenarios einfacher und produktiver werden.</span><span class="sxs-lookup"><span data-stu-id="66382-429">Razor Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="66382-430">Ein Tutorial, in dem der Model-View-Controller-Ansatz verwendet wird, finden Sie unter [Erste Schritte mit ASP.NET Core MVC und Visual Studio](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="66382-430">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="66382-431">Dieses Dokument bietet eine Einführung in Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="66382-431">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="66382-432">Es handelt sich nicht um ein Schritt-für-Schritt-Tutorial.</span><span class="sxs-lookup"><span data-stu-id="66382-432">It's not a step by step tutorial.</span></span> <span data-ttu-id="66382-433">Wenn es Ihnen Probleme bereitet, die Ausführungen in einigen Abschnitten nachzuvollziehen, lesen Sie [Erste Schritte mit Razor-Seiten in ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="66382-433">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="66382-434">Eine Übersicht über ASP.NET Core finden Sie unter [Einführung in ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="66382-434">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="66382-435">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="66382-435">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66382-436">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66382-436">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="66382-437">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="66382-437">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="66382-438">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="66382-438">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="66382-439">Erstellen eines Razor Pages-Projekts</span><span class="sxs-lookup"><span data-stu-id="66382-439">Create a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="66382-440">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66382-440">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="66382-441">Ausführliche Informationen zum Erstellen eines Razor Pages-Projekts finden Sie unter [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="66382-441">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="66382-442">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="66382-442">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="66382-443">Führen Sie `dotnet new webapp` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="66382-443">Run `dotnet new webapp` from the command line.</span></span>

<span data-ttu-id="66382-444">Öffnen Sie die generierte Datei *.csproj* aus Visual Studio für Mac.</span><span class="sxs-lookup"><span data-stu-id="66382-444">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="66382-445">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="66382-445">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="66382-446">Führen Sie `dotnet new webapp` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="66382-446">Run `dotnet new webapp` from the command line.</span></span>

---

## <a name="razor-pages"></a><span data-ttu-id="66382-447">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="66382-447">Razor Pages</span></span>

<span data-ttu-id="66382-448">Razor Pages ist in *Startup.cs* aktiviert:</span><span class="sxs-lookup"><span data-stu-id="66382-448">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="66382-449">Sehen Sie sich diese einfache Seite an: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="66382-449">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="66382-450">Der vorherige Code ähnelt sehr einer [Razor-Ansichtsdatei](xref:tutorials/first-mvc-app/adding-view), die in einer ASP.NET Core-App mit Controllern und Ansichten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="66382-450">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="66382-451">Der Unterschied besteht in der `@page`-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="66382-451">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="66382-452">`@page` macht die Datei zu einer MVC-Aktion, d.h. dass Anfragen direkt ohne einen Controller verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="66382-452">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="66382-453">`@page` muss die erste Razor-Anweisung auf einer Seite sein.</span><span class="sxs-lookup"><span data-stu-id="66382-453">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="66382-454">`@page` wirkt sich auf das Verhalten aller anderen Razor-Konstrukte aus.</span><span class="sxs-lookup"><span data-stu-id="66382-454">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="66382-455">Eine ähnliche Seite, die die `PageModel`-Klasse verwendet, wird in den folgenden zwei Dateien angezeigt.</span><span class="sxs-lookup"><span data-stu-id="66382-455">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="66382-456">Die Datei *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="66382-456">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="66382-457">Das Seitenmodell *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="66382-457">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="66382-458">Die `PageModel`-Klassendatei hat standardmäßig den gleichen Namen wie die Datei mit Razor Pages, nur dass außerdem *cs* angefügt wird.</span><span class="sxs-lookup"><span data-stu-id="66382-458">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="66382-459">Die vorherige Datei mit Razor Pages lautet beispielsweise *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="66382-459">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="66382-460">Die Datei mit der `PageModel`-Klasse heißt *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="66382-460">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="66382-461">Die Zuordnungen von URL-Pfaden zu Seiten werden durch den Speicherort der Seite im Dateisystem bestimmt.</span><span class="sxs-lookup"><span data-stu-id="66382-461">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="66382-462">Die folgende Tabelle zeigt einen Pfad zu Razor Pages und die entsprechende URL:</span><span class="sxs-lookup"><span data-stu-id="66382-462">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="66382-463">Dateiname und Pfad</span><span class="sxs-lookup"><span data-stu-id="66382-463">File name and path</span></span>               | <span data-ttu-id="66382-464">Entsprechende URL</span><span class="sxs-lookup"><span data-stu-id="66382-464">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="66382-465">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="66382-465">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="66382-466">`/` oder `/Index`</span><span class="sxs-lookup"><span data-stu-id="66382-466">`/` or `/Index`</span></span> |
| <span data-ttu-id="66382-467">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="66382-467">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="66382-468">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="66382-468">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="66382-469">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="66382-469">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="66382-470">`/Store` oder `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="66382-470">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="66382-471">Notizen:</span><span class="sxs-lookup"><span data-stu-id="66382-471">Notes:</span></span>

* <span data-ttu-id="66382-472">Die Runtime sucht standardmäßig im Ordner *Pages* (Seiten) nach Dateien mit Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="66382-472">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="66382-473">Wenn eine Seite nicht in einer URL enthalten ist, ist `Index` die Standardseite.</span><span class="sxs-lookup"><span data-stu-id="66382-473">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="66382-474">Schreiben eines einfachen Formulars</span><span class="sxs-lookup"><span data-stu-id="66382-474">Write a basic form</span></span>

<span data-ttu-id="66382-475">Razor Pages ist darauf ausgelegt, allgemeine Muster, die mit Webbrowsern verwendet werden können, beim Erstellen einer App leichter implementieren zu können.</span><span class="sxs-lookup"><span data-stu-id="66382-475">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="66382-476">Die [Modellbindung](xref:mvc/models/model-binding), [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) und alle HTML-Hilfsprogramme *funktionieren nur* mit den Eigenschaften, die in einer Klasse der Razor Pages definiert wurden.</span><span class="sxs-lookup"><span data-stu-id="66382-476">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="66382-477">Nehmen wir z.B. eine Seite, die ein allgemeines Kontaktformular für das `Contact`-Modell implementiert:</span><span class="sxs-lookup"><span data-stu-id="66382-477">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="66382-478">Für die Beispiele in diesem Dokument wird `DbContext` in der Datei [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) initialisiert.</span><span class="sxs-lookup"><span data-stu-id="66382-478">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="66382-479">Das Datenmodell:</span><span class="sxs-lookup"><span data-stu-id="66382-479">The data model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="66382-480">Der db-Kontext:</span><span class="sxs-lookup"><span data-stu-id="66382-480">The db context:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="66382-481">Die Umgebungsdatei *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="66382-481">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="66382-482">Das Seitenmodell *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="66382-482">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="66382-483">Die `PageModel` -Klasse heißt standardmäßig `<PageName>Model` und befindet sich im selben Namespace wie die Seite.</span><span class="sxs-lookup"><span data-stu-id="66382-483">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="66382-484">Mit der Klasse `PageModel` kann die Logik einer Seite von deren Darstellung getrennt werden.</span><span class="sxs-lookup"><span data-stu-id="66382-484">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="66382-485">Sie definiert Seitenhandler für Anforderungen, die an die Seite geschickt wurden, und für zum Rendern der Seite verwendete Daten.</span><span class="sxs-lookup"><span data-stu-id="66382-485">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="66382-486">Diese Trennung ermöglicht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="66382-486">This separation allows:</span></span>

* <span data-ttu-id="66382-487">Verwalten von Seitenabhängigkeiten mithilfe von [Dependency Injection](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="66382-487">Managing of page dependencies through [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="66382-488">[Komponententests](xref:test/razor-pages-tests) für Seiten</span><span class="sxs-lookup"><span data-stu-id="66382-488">[Unit testing](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="66382-489">Die Seite verfügt über eine `OnPostAsync`-*Handlermethode*, die bei `POST`-Anforderungen ausgeführt wird (wenn ein Benutzer das Formular sendet).</span><span class="sxs-lookup"><span data-stu-id="66382-489">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="66382-490">Sie können Handlermethoden für alle HTTP-Verben hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="66382-490">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="66382-491">Die am häufigsten verwendeten Handler sind:</span><span class="sxs-lookup"><span data-stu-id="66382-491">The most common handlers are:</span></span>

* <span data-ttu-id="66382-492">`OnGet`, um den für eine Seite erforderlichen Status zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="66382-492">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="66382-493">[OnGet](#OnGet)-Beispiel</span><span class="sxs-lookup"><span data-stu-id="66382-493">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="66382-494">`OnPost`, um Formularübermittlungen zu behandeln</span><span class="sxs-lookup"><span data-stu-id="66382-494">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="66382-495">Das Namenssuffix `Async` ist optional. Es wird jedoch standardmäßig häufig für asynchrone Funktionen verwendet.</span><span class="sxs-lookup"><span data-stu-id="66382-495">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="66382-496">Der vorhergehende Code ist typisch für Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="66382-496">The preceding code is typical for Razor Pages.</span></span>

<span data-ttu-id="66382-497">Wenn Sie mit ASP.NET-Apps vertraut sind, die Controller und Ansichten verwenden, werden Ihnen folgende Fakten bekannt vorkommen:</span><span class="sxs-lookup"><span data-stu-id="66382-497">If you're familiar with ASP.NET apps using controllers and views:</span></span>

* <span data-ttu-id="66382-498">Der `OnPostAsync`-Code im vorangehenden Beispiel ähnelt dem typischen Controllercode.</span><span class="sxs-lookup"><span data-stu-id="66382-498">The `OnPostAsync` code in the preceding example looks similar to typical controller code.</span></span>
* <span data-ttu-id="66382-499">Die meisten primitiven MVC-Typen wie solche für [Modellbindungen](xref:mvc/models/model-binding), [Validierungen](xref:mvc/models/validation), [Validierungen](xref:mvc/models/validation) und Aktionsergebnisse werden gemeinsam verwendet.</span><span class="sxs-lookup"><span data-stu-id="66382-499">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), [Validation](xref:mvc/models/validation),  and action results are shared.</span></span>

<span data-ttu-id="66382-500">Die vorherige `OnPostAsync`-Methode:</span><span class="sxs-lookup"><span data-stu-id="66382-500">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="66382-501">Der grundlegende Ablauf von `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="66382-501">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="66382-502">Prüfen auf Validierungsfehler</span><span class="sxs-lookup"><span data-stu-id="66382-502">Check for validation errors.</span></span>

* <span data-ttu-id="66382-503">Wenn keine Fehler vorliegen, werden die Daten gespeichert und weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="66382-503">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="66382-504">Wenn es Fehler gibt, zeigen Sie die Seite erneut mit den Validierungsmeldungen an.</span><span class="sxs-lookup"><span data-stu-id="66382-504">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="66382-505">Die clientseitige Validierung ist identisch mit herkömmlichen ASP.NET Core MVC-Anwendungen,</span><span class="sxs-lookup"><span data-stu-id="66382-505">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="66382-506">denn Validierungsfehler werden oftmals auf dem Client erkannt und nie an den Server übermittelt.</span><span class="sxs-lookup"><span data-stu-id="66382-506">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="66382-507">Wenn die Daten erfolgreich eingegeben wurden, ruft die `OnPostAsync`-Handlermethode die `RedirectToPage`-Hilfsmethode auf, um eine Instanz von `RedirectToPageResult` zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="66382-507">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="66382-508">`RedirectToPage` ist ein neues Aktionsergebnis und ähnelt `RedirectToAction` oder `RedirectToRoute`, ist aber für Seiten angepasst.</span><span class="sxs-lookup"><span data-stu-id="66382-508">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="66382-509">Im vorhergehenden Beispiel leitet es an die Stammindexseite (`/Index`) weiter.</span><span class="sxs-lookup"><span data-stu-id="66382-509">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="66382-510">Informationen zu `RedirectToPage` finden Sie im Abschnitt [URL-Generierung für Seiten](#url_gen).</span><span class="sxs-lookup"><span data-stu-id="66382-510">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="66382-511">Wenn das übermittelte Formular Validierungsfehler enthält (die an den Server übergeben wurden), ruft die `OnPostAsync`-Handlermethode die `Page`-Hilfsmethode auf.</span><span class="sxs-lookup"><span data-stu-id="66382-511">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="66382-512">`Page` gibt eine Instanz von `PageResult` zurück.</span><span class="sxs-lookup"><span data-stu-id="66382-512">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="66382-513">Der Vorgang, bei dem `Page` zurückgegeben wird, ähnelt dem Vorgang, bei dem Aktionen im Controller `View` zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="66382-513">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="66382-514">`PageResult` ist der Standardrückgabetyp für eine Handlermethode.</span><span class="sxs-lookup"><span data-stu-id="66382-514">`PageResult` is the default return type for a handler method.</span></span> <span data-ttu-id="66382-515">Eine Handlermethode, die `void` zurückgibt, rendert die Seite.</span><span class="sxs-lookup"><span data-stu-id="66382-515">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="66382-516">Die Eigenschaft `Customer` verwendet das `[BindProperty]`-Attribut, um die Modellbindung zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="66382-516">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="66382-517">Razor Pages binden Eigenschaften standardmäßig nur an Nicht-`GET`-Verben.</span><span class="sxs-lookup"><span data-stu-id="66382-517">Razor Pages, by default, bind properties only with non-`GET` verbs.</span></span> <span data-ttu-id="66382-518">Durch die Bindung an Eigenschaften können Sie den Umfang von Codes reduzieren, den Sie schreiben müssen.</span><span class="sxs-lookup"><span data-stu-id="66382-518">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="66382-519">Die Bindung reduziert den Code mithilfe der gleichen Eigenschaft, um Formularfelder (`<input asp-for="Customer.Name">`) zu rendern und die Eingabe zu akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="66382-519">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="66382-520">Die Startseite (*Index.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="66382-520">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="66382-521">Die zugeordnete `PageModel`-Klasse (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="66382-521">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="66382-522">Die Datei *Index.cshtml* enthält das folgende Markup, um einen Bearbeitungslink für jeden Kontakt zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="66382-522">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="66382-523">Das [Anchor-Taghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` verwendet das `asp-route-{value}`-Attribut, um einen Link zur Bearbeitungsseite zu generieren.</span><span class="sxs-lookup"><span data-stu-id="66382-523">The `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="66382-524">Der Link enthält die Routendaten mit der Kontakt-ID.</span><span class="sxs-lookup"><span data-stu-id="66382-524">The link contains route data with the contact ID.</span></span> <span data-ttu-id="66382-525">Beispielsweise `https://localhost:5001/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="66382-525">For example, `https://localhost:5001/Edit/1`.</span></span> <span data-ttu-id="66382-526">[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) ermöglichen serverseitigem Code das Mitwirken am Erstellen und Rendern von HTML-Elementen in Razor-Dateien.</span><span class="sxs-lookup"><span data-stu-id="66382-526">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="66382-527">Taghilfsprogramme werden durch `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` aktiviert.</span><span class="sxs-lookup"><span data-stu-id="66382-527">Tag Helpers are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="66382-528">Die Datei *Pages/Edit.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="66382-528">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="66382-529">Die erste Zeile enthält die `@page "{id:int}"`-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="66382-529">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="66382-530">Die Routingbeschränkung `"{id:int}"` weist die Seite an, die Anforderungen für die Seite zu akzeptieren, die `int`-Routingdaten enthalten.</span><span class="sxs-lookup"><span data-stu-id="66382-530">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="66382-531">Wenn eine Anforderung an die Seite bestimmte Routingdaten nicht enthält, die in einen `int` konvertiert werden können, gibt die Runtime einen Fehler vom Typ „HTTP 404: Nicht gefunden“ zurück.</span><span class="sxs-lookup"><span data-stu-id="66382-531">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="66382-532">Um die ID optional zu machen, fügen Sie `?` an die Routeneinschränkung an:</span><span class="sxs-lookup"><span data-stu-id="66382-532">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="66382-533">Die Datei *Pages/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="66382-533">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="66382-534">Die Datei *index.cshtml* enthält auch Markup zum Erstellen der Schaltfläche „Löschen“ für jeden benutzerdefinierten Kontakt:</span><span class="sxs-lookup"><span data-stu-id="66382-534">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="66382-535">Wenn die „Löschen“-Schaltfläche in HTML gerendert wird, enthält ihr `formaction`-Element Parameter für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="66382-535">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="66382-536">Die benutzerdefinierte Kontakt-ID, die vom `asp-route-id`-Attribut angegeben wird</span><span class="sxs-lookup"><span data-stu-id="66382-536">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="66382-537">Der `handler`, der vom `asp-page-handler`-Attribut angegeben wird</span><span class="sxs-lookup"><span data-stu-id="66382-537">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="66382-538">Hier sehen Sie ein Beispiel für eine gerenderte „Löschen“-Schaltfläche mit einer benutzerdefinierten Kontakt-ID von `1`:</span><span class="sxs-lookup"><span data-stu-id="66382-538">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="66382-539">Wenn die Schaltfläche ausgewählt wird, wird eine `POST`-Anforderung an den Server gesendet.</span><span class="sxs-lookup"><span data-stu-id="66382-539">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="66382-540">Durch Konvention wird der Name der Handlermethode auf Grundlage des Werts des `handler`-Parameters gemäß dem Schema `OnPost[handler]Async` ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="66382-540">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="66382-541">Da der `handler` in diesem Beispiel `delete` ist, wird die Handlermethode `OnPostDeleteAsync` verwendet, um die `POST`-Anforderung zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="66382-541">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="66382-542">Wenn `asp-page-handler` auf einen anderen Wert (z. B. `remove`) festgelegt wird, wird eine Handlermethode namens `OnPostRemoveAsync` ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="66382-542">If the `asp-page-handler` is set to a different value, such as `remove`, a handler method with the name `OnPostRemoveAsync` is selected.</span></span> <span data-ttu-id="66382-543">Im folgenden Code wird der `OnPostDeleteAsync`-Handler definiert:</span><span class="sxs-lookup"><span data-stu-id="66382-543">The following code shows the `OnPostDeleteAsync` handler:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="66382-544">Die `OnPostDeleteAsync`-Methode:</span><span class="sxs-lookup"><span data-stu-id="66382-544">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="66382-545">Akzeptiert die `id` der Abfragezeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="66382-545">Accepts the `id` from the query string.</span></span> <span data-ttu-id="66382-546">Wenn die Anweisung der Seite *Index.cshtml* die Routingeinschränkung `"{id:int?}"` enthielte, würde `id` aus den Routendaten abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="66382-546">If the *Index.cshtml* page directive contained routing constraint `"{id:int?}"`, `id` would come from route data.</span></span> <span data-ttu-id="66382-547">Die Routendaten für `id` werden beispielsweise wie folgt im URI angegeben: `https://localhost:5001/Customers/2`.</span><span class="sxs-lookup"><span data-stu-id="66382-547">The route data for `id` is specified in the URI such as `https://localhost:5001/Customers/2`.</span></span>
* <span data-ttu-id="66382-548">Fragt mit `FindAsync` die Datenbank nach dem Kundenkontakt ab.</span><span class="sxs-lookup"><span data-stu-id="66382-548">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="66382-549">Wenn der Kundenkontakt gefunden wird, wird er aus der Liste der Kundenkontakte entfernt.</span><span class="sxs-lookup"><span data-stu-id="66382-549">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="66382-550">Die Datenbank wurde aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="66382-550">The database is updated.</span></span>
* <span data-ttu-id="66382-551">Ruft `RedirectToPage` auf, um die Stammindexseite (`/Index`) umzuleiten.</span><span class="sxs-lookup"><span data-stu-id="66382-551">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="66382-552">Markieren von Eigenschaften als „Required“ (Erforderlich)</span><span class="sxs-lookup"><span data-stu-id="66382-552">Mark page properties as required</span></span>

<span data-ttu-id="66382-553">Eigenschaften in einem `PageModel` können mit dem [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute)-Attribut markiert werden:</span><span class="sxs-lookup"><span data-stu-id="66382-553">Properties on a `PageModel` can be marked with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="66382-554">Weitere Informationen finden Sie unter [Modellvalidierung](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="66382-554">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a><span data-ttu-id="66382-555">Verarbeiten von HEAD-Anforderungen mit einem OnGet-Handlerfallback</span><span class="sxs-lookup"><span data-stu-id="66382-555">Handle HEAD requests with an OnGet handler fallback</span></span>

<span data-ttu-id="66382-556">`HEAD`-Anforderungen ermöglichen Ihnen das Abrufen des Headers für eine bestimmte Ressource.</span><span class="sxs-lookup"><span data-stu-id="66382-556">`HEAD` requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="66382-557">Im Gegensatz zu `GET`-Anforderungen geben `HEAD`-Anforderungen keinen Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="66382-557">Unlike `GET` requests, `HEAD` requests don't return a response body.</span></span>

<span data-ttu-id="66382-558">Normalerweise wird ein `OnHead`-Handler erstellt und für `HEAD`-Anforderungen aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="66382-558">Ordinarily, an `OnHead` handler is created and called for `HEAD` requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="66382-559">In ASP.NET Core 2.1 oder höher greift Razor Pages auf den `OnGet`-Handler zurück, wenn kein `OnHead`-Handler definiert ist.</span><span class="sxs-lookup"><span data-stu-id="66382-559">In ASP.NET Core 2.1 or later, Razor Pages falls back to calling the `OnGet` handler if no `OnHead` handler is defined.</span></span> <span data-ttu-id="66382-560">Dieses Verhalten wird durch den Aufruf von [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices` ermöglicht:</span><span class="sxs-lookup"><span data-stu-id="66382-560">This behavior is enabled by the call to [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="66382-561">Die Standardvorlagen generieren den `SetCompatibilityVersion`-Aufruf in ASP.NET Core 2.1 und 2.2.</span><span class="sxs-lookup"><span data-stu-id="66382-561">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span> <span data-ttu-id="66382-562">Tatsächlich setzt `SetCompatibilityVersion` die Razor Pages-Option `AllowMappingHeadRequestsToGetHandler` auf `true`.</span><span class="sxs-lookup"><span data-stu-id="66382-562">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="66382-563">Sie müssen nicht alle Verhalten in `SetCompatibilityVersion` aktivieren, sondern können sich nur *bestimmte* Verhalten aussuchen.</span><span class="sxs-lookup"><span data-stu-id="66382-563">Rather than opting in to all behaviors with `SetCompatibilityVersion`, you can explicitly opt in to *specific* behaviors.</span></span> <span data-ttu-id="66382-564">Der folgende Code gibt an, dass `HEAD`-Anforderungen dem `OnGet`-Handler zugeordnet werden dürfen:</span><span class="sxs-lookup"><span data-stu-id="66382-564">The following code opts in to allowing `HEAD` requests to be mapped to the `OnGet` handler:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="66382-565">XSRF/CSRF und Razor Pages</span><span class="sxs-lookup"><span data-stu-id="66382-565">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="66382-566">Sie müssen keinen Code für die [Antifälschungsvalidierung](xref:security/anti-request-forgery) schreiben.</span><span class="sxs-lookup"><span data-stu-id="66382-566">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="66382-567">Die Generierung und Validierung von Antifälschungstoken ist automatisch in Razor Pages enthalten.</span><span class="sxs-lookup"><span data-stu-id="66382-567">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="66382-568">Verwenden von Layouts, Teilansichten, Vorlagen und Taghilfsprogrammen mit Razor Pages</span><span class="sxs-lookup"><span data-stu-id="66382-568">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="66382-569">Razor Pages beinhaltet alle Funktionen der Razor-Anzeige-Engine.</span><span class="sxs-lookup"><span data-stu-id="66382-569">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="66382-570">Layouts, Teilansichten, Vorlagen, Taghilfsprogramme, *_ViewStart.cshtml*, *_ViewImports.cshtml* funktionieren auf die gleiche Weise wie für herkömmliche Razor-Ansichten.</span><span class="sxs-lookup"><span data-stu-id="66382-570">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="66382-571">Strukturieren Sie diese Seite mit einigen dieser praktischen Funktionen.</span><span class="sxs-lookup"><span data-stu-id="66382-571">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

<span data-ttu-id="66382-572">Fügen Sie der *Pages/Shared/_Layout.cshtml* eine [Layoutseite](xref:mvc/views/layout) hinzu:</span><span class="sxs-lookup"><span data-stu-id="66382-572">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="66382-573">Das [Layout](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="66382-573">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="66382-574">Steuert das Layout der einzelnen Seiten, es sei denn, das Layout wird für eine Seite deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="66382-574">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="66382-575">Importiert HTML-Strukturen, z.B. JavaScript und Stylesheets.</span><span class="sxs-lookup"><span data-stu-id="66382-575">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="66382-576">Weitere Informationen finden Sie unter [Layoutseite](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="66382-576">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="66382-577">Die Eigenschaft [Layout](xref:mvc/views/layout#specifying-a-layout) wird in *Pages/_ViewStart.cshtml* festgelegt:</span><span class="sxs-lookup"><span data-stu-id="66382-577">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

<span data-ttu-id="66382-578">Das Layout befindet sich im Ordner *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="66382-578">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="66382-579">Seiten suchen hierarchisch nach anderen Ansichten (Layouts, Vorlagen oder Teilansichten) und beginnen im gleichen Ordner wie die aktuelle Seite.</span><span class="sxs-lookup"><span data-stu-id="66382-579">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="66382-580">Ein Layout im Ordner *Pages/Shared* kann von jeder Razor-Seite aus unter dem Ordner *Pages* verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="66382-580">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="66382-581">Die Layoutdatei sollte im Ordner *Pages/Shared* gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="66382-581">The layout file should go in the *Pages/Shared* folder.</span></span>

<span data-ttu-id="66382-582">Wir empfehlen Ihnen, die Layoutdatei **nicht** im Ordner *Views/Shared* (Ansichten/Freigegeben) zu platzieren.</span><span class="sxs-lookup"><span data-stu-id="66382-582">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="66382-583">*Views/Shared* ist ein MVC-Ansichtsmuster.</span><span class="sxs-lookup"><span data-stu-id="66382-583">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="66382-584">Razor Pages basieren auf der Ordnerhierarchie, nicht auf Pfadkonventionen.</span><span class="sxs-lookup"><span data-stu-id="66382-584">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="66382-585">Die Ansichtensuche in einer Razor Page enthält den Ordner *Pages*.</span><span class="sxs-lookup"><span data-stu-id="66382-585">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="66382-586">Die Layouts, Vorlagen und Teilansichten, die Sie mit MVC-Controllern und herkömmlichen Razor-Ansichten verwenden, *funktionieren einfach so*.</span><span class="sxs-lookup"><span data-stu-id="66382-586">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="66382-587">Fügen Sie eine Datei *Pages/_ViewImports.cshtml* hinzu:</span><span class="sxs-lookup"><span data-stu-id="66382-587">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="66382-588">`@namespace` wird weiter unten im Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="66382-588">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="66382-589">Die `@addTagHelper`-Anweisung bringt die [integrierten Taghilfsprogramme](xref:mvc/views/tag-helpers/builtin-th/Index) zu allen Seiten in der Ordner *Pages*.</span><span class="sxs-lookup"><span data-stu-id="66382-589">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="66382-590">Wenn die `@namespace`-Anweisung explizit auf eine Seite angewendet wird:</span><span class="sxs-lookup"><span data-stu-id="66382-590">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="66382-591">Die Anweisung legt den Namespace für die Seite fest.</span><span class="sxs-lookup"><span data-stu-id="66382-591">The directive sets the namespace for the page.</span></span> <span data-ttu-id="66382-592">Die `@model`-Anweisung muss den Namespace nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="66382-592">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="66382-593">Wenn sich die `@namespace`-Anweisung in *_ViewImports.cshtml* befindet, stellt der angegebene Namespace das Präfix für den generierten Namespace auf der Seite bereit, die die `@namespace`-Anweisung importiert.</span><span class="sxs-lookup"><span data-stu-id="66382-593">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="66382-594">Der Rest der generierten Namespaces (der Suffixteil) ist der durch Punkte getrennte relative Pfad zwischen dem Ordner mit *_ViewImports.cshtml* und dem Ordner, der die Seite enthält.</span><span class="sxs-lookup"><span data-stu-id="66382-594">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="66382-595">Die `PageModel`-Klasse in *Pages/Customers/Edit.cshtml.cs* legt den Namespace z.B. explizit fest:</span><span class="sxs-lookup"><span data-stu-id="66382-595">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="66382-596">Die Datei *Pages/_ViewImports.cshtml* legt den folgenden Namespace fest:</span><span class="sxs-lookup"><span data-stu-id="66382-596">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="66382-597">Der generierte Namespace für die Razor Page *Pages/Customers/Edit.cshtml* ist identisch mit der `PageModel`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="66382-597">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="66382-598">`@namespace` *funktioniert auch mit konventionellen Razor-Ansichten.*</span><span class="sxs-lookup"><span data-stu-id="66382-598">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="66382-599">Die ursprüngliche Umgebungsdatei *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="66382-599">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="66382-600">Die aktualisierte Umgebungsdatei *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="66382-600">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="66382-601">Die [Razor Pages-Startprojekt](#rpvs17) enthält die Seite *Pages/_ValidationScriptsPartial.cshtml*, die die clientseitige Validierung bindet.</span><span class="sxs-lookup"><span data-stu-id="66382-601">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="66382-602">Weitere Informationen zu Teilansichten finden Sie unter <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="66382-602">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="66382-603">URL-Generierung für Seiten</span><span class="sxs-lookup"><span data-stu-id="66382-603">URL generation for Pages</span></span>

<span data-ttu-id="66382-604">Die zuvor gezeigte `Create`-Seite verwendet `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="66382-604">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="66382-605">Die App hat die folgende Datei/Ordner-Struktur:</span><span class="sxs-lookup"><span data-stu-id="66382-605">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="66382-606">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="66382-606">*/Pages*</span></span>

  * <span data-ttu-id="66382-607">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="66382-607">*Index.cshtml*</span></span>
  * <span data-ttu-id="66382-608">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="66382-608">*/Customers*</span></span>

    * <span data-ttu-id="66382-609">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="66382-609">*Create.cshtml*</span></span>
    * <span data-ttu-id="66382-610">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="66382-610">*Edit.cshtml*</span></span>
    * <span data-ttu-id="66382-611">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="66382-611">*Index.cshtml*</span></span>

<span data-ttu-id="66382-612">Die Seiten *Pages/Customers/Create.cshtml* und *Pages/Customers/Edit.cshtml* leiten bei Erfolg an *Pages/Index.cshtml* weiter.</span><span class="sxs-lookup"><span data-stu-id="66382-612">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="66382-613">Die Zeichenfolge `/Index` ist Teil des URI, der auf die vorhergehende Seite zugreifen soll.</span><span class="sxs-lookup"><span data-stu-id="66382-613">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="66382-614">Die Zeichenfolge `/Index` kann für das Generieren von URIs für die Seite *Pages/Index.cshtml* verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="66382-614">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="66382-615">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66382-615">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="66382-616">Der Seitenname ist der Pfad zu der Seite vom Stammordner */Pages* (einschließlich eines vorangestellten `/`, z.B. `/Index`).</span><span class="sxs-lookup"><span data-stu-id="66382-616">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="66382-617">Die oben stehenden Beispiele für eine URL-Generierung bieten erweiterte Optionen und Funktionen, durch die Sie URLs nicht mehr hartcodieren müssen.</span><span class="sxs-lookup"><span data-stu-id="66382-617">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="66382-618">Bei der URL-Generierung wird [Routing](xref:mvc/controllers/routing) verwendet. Außerdem können damit Parameter generiert und entsprechend der Definition der Route im Zielpfad codiert werden.</span><span class="sxs-lookup"><span data-stu-id="66382-618">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="66382-619">Die URL-Generierung für Seiten unterstützt relative Namen.</span><span class="sxs-lookup"><span data-stu-id="66382-619">URL generation for pages supports relative names.</span></span> <span data-ttu-id="66382-620">In der folgenden Tabelle wird dargestellt, welche Indexseite für verschiedene `RedirectToPage`-Parameter aus *Pages/Customers/Create.cshtml* ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="66382-620">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="66382-621">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="66382-621">RedirectToPage(x)</span></span>| <span data-ttu-id="66382-622">Seite</span><span class="sxs-lookup"><span data-stu-id="66382-622">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="66382-623">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="66382-623">RedirectToPage("/Index")</span></span> | <span data-ttu-id="66382-624">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="66382-624">*Pages/Index*</span></span> |
| <span data-ttu-id="66382-625">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="66382-625">RedirectToPage("./Index");</span></span> | <span data-ttu-id="66382-626">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="66382-626">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="66382-627">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="66382-627">RedirectToPage("../Index")</span></span> | <span data-ttu-id="66382-628">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="66382-628">*Pages/Index*</span></span> |
| <span data-ttu-id="66382-629">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="66382-629">RedirectToPage("Index")</span></span>  | <span data-ttu-id="66382-630">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="66382-630">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="66382-631">`RedirectToPage("Index")`, `RedirectToPage("./Index")` und `RedirectToPage("../Index")` sind *relative Namen*.</span><span class="sxs-lookup"><span data-stu-id="66382-631">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="66382-632">Der `RedirectToPage`-Parameter wird mit dem Pfad der aktuellen Seite *kombiniert*, um den Namen der Zielseite zu berechnen.</span><span class="sxs-lookup"><span data-stu-id="66382-632">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="66382-633">Das Verknüpfen relativer Namen eignet sich beim Erstellen von Websites mit einer komplexen Struktur.</span><span class="sxs-lookup"><span data-stu-id="66382-633">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="66382-634">Wenn Sie relative Namen verwenden, um Seiten in einem Ordner zu verknüpfen, können Sie diesen Ordner umbenennen.</span><span class="sxs-lookup"><span data-stu-id="66382-634">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="66382-635">Alle Links funktionieren weiterhin, da sie nicht den Namen des Ordners enthalten.</span><span class="sxs-lookup"><span data-stu-id="66382-635">All the links still work (because they didn't include the folder name).</span></span>

<span data-ttu-id="66382-636">Um auf eine Seite in einem anderen [Bereich](xref:mvc/controllers/areas) umzuleiten, geben Sie den Bereich an:</span><span class="sxs-lookup"><span data-stu-id="66382-636">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="66382-637">Weitere Informationen finden Sie unter <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="66382-637">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="66382-638">Attribut „ViewData“</span><span class="sxs-lookup"><span data-stu-id="66382-638">ViewData attribute</span></span>

<span data-ttu-id="66382-639">Daten können mit [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute) an eine Seite übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="66382-639">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="66382-640">Die Werte der Eigenschaften auf Controllern oder Razor Pages-Modellen, die mit dem `[ViewData]`-Attribut versehen sind, werden in [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) gespeichert und daraus geladen.</span><span class="sxs-lookup"><span data-stu-id="66382-640">Properties on controllers or Razor Page models with the `[ViewData]` attribute have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="66382-641">Im folgenden Beispiel enthält das `AboutModel` eine `Title`-Eigenschaft, die mit `[ViewData]` markiert ist.</span><span class="sxs-lookup"><span data-stu-id="66382-641">In the following example, the `AboutModel` contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="66382-642">Die Eigenschaft `Title` wird auf den Titel der Infoseite festgelegt:</span><span class="sxs-lookup"><span data-stu-id="66382-642">The `Title` property is set to the title of the About page:</span></span>

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

<span data-ttu-id="66382-643">Greifen Sie auf der Infoseite auf die Eigenschaft `Title` als Modelleigenschaft zu:</span><span class="sxs-lookup"><span data-stu-id="66382-643">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="66382-644">Im Layout wird der Titel aus dem ViewData-Wörterbuch gelesen:</span><span class="sxs-lookup"><span data-stu-id="66382-644">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a><span data-ttu-id="66382-645">TempData</span><span class="sxs-lookup"><span data-stu-id="66382-645">TempData</span></span>

<span data-ttu-id="66382-646">ASP.NET Core macht die Eigenschaft [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) auf einem [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller) verfügbar.</span><span class="sxs-lookup"><span data-stu-id="66382-646">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="66382-647">Diese Eigenschaft speichert Daten, bis sie gelesen wurden.</span><span class="sxs-lookup"><span data-stu-id="66382-647">This property stores data until it's read.</span></span> <span data-ttu-id="66382-648">Die Methoden `Keep` und `Peek` können verwendet werden, um die Daten zu überprüfen, ohne sie zu löschen.</span><span class="sxs-lookup"><span data-stu-id="66382-648">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="66382-649">`TempData` eignet sich für die Weiterleitung, wenn Daten für mehr als eine Anforderung benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="66382-649">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="66382-650">Im folgenden Code wird der Wert von `Message` mit `TempData` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="66382-650">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="66382-651">Das folgende Markup in der Datei *Pages/Customers/Index.cshtml* zeigt den Wert von `Message` mit `TempData` an.</span><span class="sxs-lookup"><span data-stu-id="66382-651">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="66382-652">Das Seitenmodell *Pages/Customers/Index.cshtml.cs* wendet das `[TempData]`-Attribut auf die Eigenschaft `Message` an.</span><span class="sxs-lookup"><span data-stu-id="66382-652">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```csharp
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="66382-653">Weitere Informationen finden Sie unter [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="66382-653">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="66382-654">Mehrere Handler pro Seite</span><span class="sxs-lookup"><span data-stu-id="66382-654">Multiple handlers per page</span></span>

<span data-ttu-id="66382-655">Die folgende Seite generiert mit dem `asp-page-handler`-Taghilfsprogramm Markup für zwei Handler:</span><span class="sxs-lookup"><span data-stu-id="66382-655">The following page generates markup for two handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="66382-656">Das Formular im vorherigen Beispiel hat zwei Sendeschaltflächen, und jede verwendet `FormActionTagHelper`, um an eine andere URL zu übermitteln.</span><span class="sxs-lookup"><span data-stu-id="66382-656">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="66382-657">Das `asp-page-handler`-Attribut ist eine Ergänzung für `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="66382-657">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="66382-658">`asp-page-handler` generiert URLs, die als Übermittlungsziel jeweils die durch eine Seite festgelegte Handlermethode verwenden.</span><span class="sxs-lookup"><span data-stu-id="66382-658">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="66382-659">`asp-page` wird nicht angegeben, weil das Beispiel mit der aktuellen Seite verknüpft.</span><span class="sxs-lookup"><span data-stu-id="66382-659">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="66382-660">Das Seitenmodell:</span><span class="sxs-lookup"><span data-stu-id="66382-660">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="66382-661">Der vorherige Code verwendet *benannte Handlermethoden*.</span><span class="sxs-lookup"><span data-stu-id="66382-661">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="66382-662">Benannte Handlermethoden werden aus dem Text im Namen nach `On<HTTP Verb>` und vor `Async` (falls vorhanden) erstellt.</span><span class="sxs-lookup"><span data-stu-id="66382-662">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="66382-663">Im vorherigen Beispiel sind OnPost**JoinList**Async und OnPost**JoinListUC**Async die Seitenmethoden.</span><span class="sxs-lookup"><span data-stu-id="66382-663">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="66382-664">Wenn Sie *OnPost* und *Async* entfernen, lauten die Handlernamen `JoinList` und `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="66382-664">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="66382-665">Mit dem vorherigen Code lautet der URL-Pfad, der an `OnPostJoinListAsync` übermittelt, `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="66382-665">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="66382-666">Der URL-Pfad, der an `OnPostJoinListUCAsync` übermittelt, lautet `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="66382-666">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="66382-667">Benutzerdefinierte Routen</span><span class="sxs-lookup"><span data-stu-id="66382-667">Custom routes</span></span>

<span data-ttu-id="66382-668">Verwenden Sie die `@page`-Anweisung für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="66382-668">Use the `@page` directive to:</span></span>

* <span data-ttu-id="66382-669">Das Angeben einer benutzerdefinierten Route zu einer Seite.</span><span class="sxs-lookup"><span data-stu-id="66382-669">Specify a custom route to a page.</span></span> <span data-ttu-id="66382-670">Die Route zur Seite „Info“ kann mit `@page "/Some/Other/Path"` beispielsweise auf `/Some/Other/Path` festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="66382-670">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="66382-671">Das Anfügen von Segmenten an die Standardroute einer Seite.</span><span class="sxs-lookup"><span data-stu-id="66382-671">Append segments to a page's default route.</span></span> <span data-ttu-id="66382-672">Mit `@page "item"` kann beispielsweise ein item-Segment an die Standardroute der Seite angefügt werden.</span><span class="sxs-lookup"><span data-stu-id="66382-672">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="66382-673">Das Anfügen von Parametern an die Standardroute einer Seite.</span><span class="sxs-lookup"><span data-stu-id="66382-673">Append parameters to a page's default route.</span></span> <span data-ttu-id="66382-674">Mit `@page "{id}"` kann beispielsweise ein ID-Parameter (`id`) für eine Seite angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="66382-674">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="66382-675">Es wird ein relativer Pfad zum Stamm unterstützt, der durch eine Tilde (`~`) festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="66382-675">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="66382-676">`@page "~/Some/Other/Path"` entspricht beispielsweise `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="66382-676">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="66382-677">Wenn Sie nicht möchten, dass die Abfragezeichenfolge `?handler=JoinList` in der URL enthalten ist, ändern Sie die Route so, dass der Handlername im Pfadteil der URL eingefügt wird.</span><span class="sxs-lookup"><span data-stu-id="66382-677">If you don't like the query string `?handler=JoinList` in the URL, change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="66382-678">Sie können die Route anpassen, indem Sie nach der `@page`-Anweisung eine Routenvorlage in doppelten Anführungszeichen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="66382-678">The route can be customized by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="66382-679">Mit dem vorherigen Code lautet der URL-Pfad, der an `OnPostJoinListAsync` übermittelt, `https://localhost:5001/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="66382-679">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `https://localhost:5001/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="66382-680">Der URL-Pfad, der an `OnPostJoinListUCAsync` übermittelt, lautet `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="66382-680">The URL path that submits to `OnPostJoinListUCAsync` is `https://localhost:5001/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="66382-681">Das `?` nach `handler` bedeutet, dass der Routenparameter optional ist.</span><span class="sxs-lookup"><span data-stu-id="66382-681">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="66382-682">Konfiguration und Einstellungen</span><span class="sxs-lookup"><span data-stu-id="66382-682">Configuration and settings</span></span>

<span data-ttu-id="66382-683">Um die erweiterten Optionen zu konfigurieren, verwenden Sie die Erweiterungsmethode `AddRazorPagesOptions` auf dem MVC-Generator:</span><span class="sxs-lookup"><span data-stu-id="66382-683">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="66382-684">Derzeit können Sie `RazorPagesOptions` verwenden, um das Stammverzeichnis für Seiten festzulegen oder Anwendungsmodellkonventionen für Seiten hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="66382-684">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="66382-685">Auf diese Weise wird in Zukunft eine höhere Erweiterbarkeit erreicht.</span><span class="sxs-lookup"><span data-stu-id="66382-685">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="66382-686">Informationen zum Vorkompilieren von Ansichten finden Sie unter [Razor-Ansichtenkompilierung](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="66382-686">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="66382-687">[Laden Sie Beispielcode herunter, oder zeigen Sie ihn an](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="66382-687">[Download or view sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="66382-688">Lesen Sie auch den Artikel [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start), der auf dieser Einführung aufbaut.</span><span class="sxs-lookup"><span data-stu-id="66382-688">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="66382-689">Festlegen des Inhaltsstammverzeichnisses für Razor Pages</span><span class="sxs-lookup"><span data-stu-id="66382-689">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="66382-690">Standardmäßig lautet das Stammverzeichnis für Razor Pages */Pages*.</span><span class="sxs-lookup"><span data-stu-id="66382-690">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="66382-691">Fügen Sie [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) zu [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) hinzu, um anzugeben, dass sich Ihre Razor Pages im [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) der App befinden:</span><span class="sxs-lookup"><span data-stu-id="66382-691">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the [content root](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="66382-692">Festlegen eines benutzerdefinierten Stammverzeichnisses für Razor Pages</span><span class="sxs-lookup"><span data-stu-id="66382-692">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="66382-693">Fügen Sie [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) zu [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) hinzu, um anzugeben, dass Ihre Razor Pages sich in einem benutzerdefinierten Stammverzeichnis in der App befinden (geben Sie einen relativen Pfad an):</span><span class="sxs-lookup"><span data-stu-id="66382-693">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="66382-694">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="66382-694">Additional resources</span></span>

* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
