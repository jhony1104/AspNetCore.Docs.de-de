---
title: Einführung in Razor Pages in ASP.NET Core
author: Rick-Anderson
description: Erfahren Sie, wie Razor Pages in ASP.NET Core codierungsseitige Szenarios einfacher und produktiver gestalten als MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 04/06/2019
uid: razor-pages/index
ms.openlocfilehash: 419355d670536fef1a38fbcb8ce1fd880c0e9b0d
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555734"
---
# <a name="introduction-to-razor-pages-in-aspnet-core"></a><span data-ttu-id="8937a-103">Einführung in Razor Pages in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8937a-103">Introduction to Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="8937a-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Ryan Nowak](https://github.com/rynowak)</span><span class="sxs-lookup"><span data-stu-id="8937a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Ryan Nowak](https://github.com/rynowak)</span></span>

<span data-ttu-id="8937a-105">Razor Pages ist ein neuer Bestandteil von ASP.NET Core MVC, mit dem codierungsseitige Szenarios einfacher und produktiver werden.</span><span class="sxs-lookup"><span data-stu-id="8937a-105">Razor Pages is a new aspect of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="8937a-106">Ein Tutorial, in dem der Model-View-Controller-Ansatz verwendet wird, finden Sie unter [Erste Schritte mit ASP.NET Core MVC und Visual Studio](xref:tutorials/first-mvc-app/start-mvc).</span><span class="sxs-lookup"><span data-stu-id="8937a-106">If you're looking for a tutorial that uses the Model-View-Controller approach, see [Get started with ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="8937a-107">Dieses Dokument bietet eine Einführung in Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="8937a-107">This document provides an introduction to Razor Pages.</span></span> <span data-ttu-id="8937a-108">Es handelt sich nicht um ein Schritt-für-Schritt-Tutorial.</span><span class="sxs-lookup"><span data-stu-id="8937a-108">It's not a step by step tutorial.</span></span> <span data-ttu-id="8937a-109">Wenn es Ihnen Probleme bereitet, die Ausführungen in einigen Abschnitten nachzuvollziehen, lesen Sie [Erste Schritte mit Razor-Seiten in ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="8937a-109">If you find some of the sections too advanced, see [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span></span> <span data-ttu-id="8937a-110">Eine Übersicht über ASP.NET Core finden Sie unter [Einführung in ASP.NET Core](xref:index).</span><span class="sxs-lookup"><span data-stu-id="8937a-110">For an overview of ASP.NET Core, see the [Introduction to ASP.NET Core](xref:index).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8937a-111">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="8937a-111">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8937a-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8937a-112">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8937a-113">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8937a-113">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8937a-114">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8937a-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-razor-pages-project"></a><span data-ttu-id="8937a-115">Erstellen eines Razor Pages-Projekts</span><span class="sxs-lookup"><span data-stu-id="8937a-115">Create a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8937a-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8937a-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8937a-117">Ausführliche Informationen zum Erstellen eines Razor Pages-Projekts finden Sie unter [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start).</span><span class="sxs-lookup"><span data-stu-id="8937a-117">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) for detailed instructions on how to create a Razor Pages project.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8937a-118">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8937a-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="8937a-119">Führen Sie `dotnet new webapp` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="8937a-119">Run `dotnet new webapp` from the command line.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="8937a-120">Führen Sie `dotnet new razor` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="8937a-120">Run `dotnet new razor` from the command line.</span></span>

::: moniker-end

<span data-ttu-id="8937a-121">Öffnen Sie die generierte Datei *.csproj* aus Visual Studio für Mac.</span><span class="sxs-lookup"><span data-stu-id="8937a-121">Open the generated *.csproj* file from Visual Studio for Mac.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8937a-122">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8937a-122">Visual Studio Code</span></span>](#tab/visual-studio-code)

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="8937a-123">Führen Sie `dotnet new webapp` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="8937a-123">Run `dotnet new webapp` from the command line.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="8937a-124">Führen Sie `dotnet new razor` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="8937a-124">Run `dotnet new razor` from the command line.</span></span>

::: moniker-end

---

## <a name="razor-pages"></a><span data-ttu-id="8937a-125">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="8937a-125">Razor Pages</span></span>

<span data-ttu-id="8937a-126">Razor Pages ist in *Startup.cs* aktiviert:</span><span class="sxs-lookup"><span data-stu-id="8937a-126">Razor Pages is enabled in *Startup.cs*:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

<span data-ttu-id="8937a-127">Sehen Sie sich diese einfache Seite an: <a name="OnGet"></a></span><span class="sxs-lookup"><span data-stu-id="8937a-127">Consider a basic page: <a name="OnGet"></a></span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

<span data-ttu-id="8937a-128">Der vorherige Code ähnelt sehr einer [Razor-Ansichtsdatei](xref:tutorials/first-mvc-app/adding-view), die in einer ASP.NET Core-App mit Controllern und Ansichten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="8937a-128">The preceding code looks a lot like a [Razor view file](xref:tutorials/first-mvc-app/adding-view) used in an ASP.NET Core app with controllers and views.</span></span> <span data-ttu-id="8937a-129">Der Unterschied besteht in der `@page`-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="8937a-129">What makes it different is the `@page` directive.</span></span> <span data-ttu-id="8937a-130">`@page` macht die Datei zu einer MVC-Aktion, d.h. dass Anfragen direkt ohne einen Controller verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="8937a-130">`@page` makes the file into an MVC action - which means that it handles requests directly, without going through a controller.</span></span> <span data-ttu-id="8937a-131">`@page` muss die erste Razor-Anweisung auf einer Seite sein.</span><span class="sxs-lookup"><span data-stu-id="8937a-131">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="8937a-132">`@page` wirkt sich auf das Verhalten aller anderen Razor-Konstrukte aus.</span><span class="sxs-lookup"><span data-stu-id="8937a-132">`@page` affects the behavior of other Razor constructs.</span></span>

<span data-ttu-id="8937a-133">Eine ähnliche Seite, die die `PageModel`-Klasse verwendet, wird in den folgenden zwei Dateien angezeigt.</span><span class="sxs-lookup"><span data-stu-id="8937a-133">A similar page, using a `PageModel` class, is shown in the following two files.</span></span> <span data-ttu-id="8937a-134">Die Datei *Pages/Index2.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="8937a-134">The *Pages/Index2.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

<span data-ttu-id="8937a-135">Das Seitenmodell *Pages/Index2.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="8937a-135">The *Pages/Index2.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

<span data-ttu-id="8937a-136">Die `PageModel`-Klassendatei hat standardmäßig den gleichen Namen wie die Datei mit Razor Pages, nur dass außerdem *cs* angefügt wird.</span><span class="sxs-lookup"><span data-stu-id="8937a-136">By convention, the `PageModel` class file has the same name as the Razor Page file with *.cs* appended.</span></span> <span data-ttu-id="8937a-137">Die vorherige Datei mit Razor Pages lautet beispielsweise *Pages/Index2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="8937a-137">For example, the previous Razor Page is *Pages/Index2.cshtml*.</span></span> <span data-ttu-id="8937a-138">Die Datei mit der `PageModel`-Klasse heißt *Pages/Index2.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="8937a-138">The file containing the `PageModel` class is named *Pages/Index2.cshtml.cs*.</span></span>

<span data-ttu-id="8937a-139">Die Zuordnungen von URL-Pfaden zu Seiten werden durch den Speicherort der Seite im Dateisystem bestimmt.</span><span class="sxs-lookup"><span data-stu-id="8937a-139">The associations of URL paths to pages are determined by the page's location in the file system.</span></span> <span data-ttu-id="8937a-140">Die folgende Tabelle zeigt einen Pfad zu Razor Pages und die entsprechende URL:</span><span class="sxs-lookup"><span data-stu-id="8937a-140">The following table shows a Razor Page path and the matching URL:</span></span>

| <span data-ttu-id="8937a-141">Dateiname und Pfad</span><span class="sxs-lookup"><span data-stu-id="8937a-141">File name and path</span></span>               | <span data-ttu-id="8937a-142">Entsprechende URL</span><span class="sxs-lookup"><span data-stu-id="8937a-142">matching URL</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="8937a-143">*/Pages/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="8937a-143">*/Pages/Index.cshtml*</span></span> | <span data-ttu-id="8937a-144">`/` oder `/Index`</span><span class="sxs-lookup"><span data-stu-id="8937a-144">`/` or `/Index`</span></span> |
| <span data-ttu-id="8937a-145">*/Pages/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="8937a-145">*/Pages/Contact.cshtml*</span></span> | `/Contact` |
| <span data-ttu-id="8937a-146">*/Pages/Store/Contact.cshtml*</span><span class="sxs-lookup"><span data-stu-id="8937a-146">*/Pages/Store/Contact.cshtml*</span></span> | `/Store/Contact` |
| <span data-ttu-id="8937a-147">*/Pages/Store/Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="8937a-147">*/Pages/Store/Index.cshtml*</span></span> | <span data-ttu-id="8937a-148">`/Store` oder `/Store/Index`</span><span class="sxs-lookup"><span data-stu-id="8937a-148">`/Store` or `/Store/Index`</span></span> |

<span data-ttu-id="8937a-149">Notizen:</span><span class="sxs-lookup"><span data-stu-id="8937a-149">Notes:</span></span>

* <span data-ttu-id="8937a-150">Die Runtime sucht standardmäßig im Ordner *Pages* (Seiten) nach Dateien mit Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="8937a-150">The runtime looks for Razor Pages files in the *Pages* folder by default.</span></span>
* <span data-ttu-id="8937a-151">Wenn eine Seite nicht in einer URL enthalten ist, ist `Index` die Standardseite.</span><span class="sxs-lookup"><span data-stu-id="8937a-151">`Index` is the default page when a URL doesn't include a page.</span></span>

## <a name="write-a-basic-form"></a><span data-ttu-id="8937a-152">Schreiben eines einfachen Formulars</span><span class="sxs-lookup"><span data-stu-id="8937a-152">Write a basic form</span></span>

<span data-ttu-id="8937a-153">Razor Pages ist darauf ausgelegt, allgemeine Muster, die mit Webbrowsern verwendet werden können, beim Erstellen einer App leichter implementieren zu können.</span><span class="sxs-lookup"><span data-stu-id="8937a-153">Razor Pages is designed to make common patterns used with web browsers easy to implement when building an app.</span></span> <span data-ttu-id="8937a-154">Die [Modellbindung](xref:mvc/models/model-binding), [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) und alle HTML-Hilfsprogramme *funktionieren nur* mit den Eigenschaften, die in einer Klasse der Razor Pages definiert wurden.</span><span class="sxs-lookup"><span data-stu-id="8937a-154">[Model binding](xref:mvc/models/model-binding), [Tag Helpers](xref:mvc/views/tag-helpers/intro), and HTML helpers all *just work* with the properties defined in a Razor Page class.</span></span> <span data-ttu-id="8937a-155">Nehmen wir z.B. eine Seite, die ein allgemeines Kontaktformular für das `Contact`-Modell implementiert:</span><span class="sxs-lookup"><span data-stu-id="8937a-155">Consider a page that implements a basic "contact us" form for the `Contact` model:</span></span>

<span data-ttu-id="8937a-156">Für die Beispiele in diesem Dokument wird `DbContext` in der Datei [Startup.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) initialisiert.</span><span class="sxs-lookup"><span data-stu-id="8937a-156">For the samples in this document, the `DbContext` is initialized in the [Startup.cs](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) file.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

<span data-ttu-id="8937a-157">Das Datenmodell:</span><span class="sxs-lookup"><span data-stu-id="8937a-157">The data model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/Customer.cs)]

<span data-ttu-id="8937a-158">Der db-Kontext:</span><span class="sxs-lookup"><span data-stu-id="8937a-158">The db context:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

<span data-ttu-id="8937a-159">Die Umgebungsdatei *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="8937a-159">The *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

<span data-ttu-id="8937a-160">Das Seitenmodell *Pages/Create.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="8937a-160">The *Pages/Create.cshtml.cs* page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

<span data-ttu-id="8937a-161">Die `PageModel` -Klasse heißt standardmäßig `<PageName>Model` und befindet sich im selben Namespace wie die Seite.</span><span class="sxs-lookup"><span data-stu-id="8937a-161">By convention, the `PageModel` class is called `<PageName>Model` and is in the same namespace as the page.</span></span>

<span data-ttu-id="8937a-162">Mit der Klasse `PageModel` kann die Logik einer Seite von deren Darstellung getrennt werden.</span><span class="sxs-lookup"><span data-stu-id="8937a-162">The `PageModel` class allows separation of the logic of a page from its presentation.</span></span> <span data-ttu-id="8937a-163">Sie definiert Seitenhandler für Anforderungen, die an die Seite geschickt wurden, und für zum Rendern der Seite verwendete Daten.</span><span class="sxs-lookup"><span data-stu-id="8937a-163">It defines page handlers for requests sent to the page and the data used to render the page.</span></span> <span data-ttu-id="8937a-164">Durch diese Trennung können Sie Seitenabhängigkeiten durch [Abhängigkeiteneinschleusung](xref:fundamentals/dependency-injection) verwalten und [Komponententests](xref:test/razor-pages-tests) für die Seiten durchführen.</span><span class="sxs-lookup"><span data-stu-id="8937a-164">This separation allows you to manage page dependencies through [dependency injection](xref:fundamentals/dependency-injection) and to [unit test](xref:test/razor-pages-tests) the pages.</span></span>

<span data-ttu-id="8937a-165">Die Seite hat eine `OnPostAsync`-*Handlermethode*, die auf `POST`-Anforderungen ausgeführt wird (wenn ein Benutzer das Formular sendet).</span><span class="sxs-lookup"><span data-stu-id="8937a-165">The page has an `OnPostAsync` *handler method*, which runs on `POST` requests (when a user posts the form).</span></span> <span data-ttu-id="8937a-166">Sie können Handlermethoden für alle HTTP-Verben hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="8937a-166">You can add handler methods for any HTTP verb.</span></span> <span data-ttu-id="8937a-167">Die am häufigsten verwendeten Handler sind:</span><span class="sxs-lookup"><span data-stu-id="8937a-167">The most common handlers are:</span></span>

* <span data-ttu-id="8937a-168">`OnGet`, um den für eine Seite erforderlichen Status zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="8937a-168">`OnGet` to initialize state needed for the page.</span></span> <span data-ttu-id="8937a-169">[OnGet](#OnGet)-Beispiel</span><span class="sxs-lookup"><span data-stu-id="8937a-169">[OnGet](#OnGet) sample.</span></span>
* <span data-ttu-id="8937a-170">`OnPost`, um Formularübermittlungen zu behandeln</span><span class="sxs-lookup"><span data-stu-id="8937a-170">`OnPost` to handle form submissions.</span></span>

<span data-ttu-id="8937a-171">Das Namenssuffix `Async` ist optional. Es wird jedoch standardmäßig häufig für asynchrone Funktionen verwendet.</span><span class="sxs-lookup"><span data-stu-id="8937a-171">The `Async` naming suffix is optional but is often used by convention for asynchronous functions.</span></span> <span data-ttu-id="8937a-172">Der `OnPostAsync`-Code im vorhergehenden Beispiel ähnelt dem, was Sie normalerweise in einem Controller schreiben würden.</span><span class="sxs-lookup"><span data-stu-id="8937a-172">The `OnPostAsync` code in the preceding example looks similar to what you would normally write in a controller.</span></span> <span data-ttu-id="8937a-173">Der vorhergehende Code ist typisch für Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="8937a-173">The preceding code is typical for Razor Pages.</span></span> <span data-ttu-id="8937a-174">Die meisten primitiven MVC-Typen wie die [Modellbindung](xref:mvc/models/model-binding), die [Validierung](xref:mvc/models/validation) und Aktionsergebnisse werden freigegeben.</span><span class="sxs-lookup"><span data-stu-id="8937a-174">Most of the MVC primitives like [model binding](xref:mvc/models/model-binding), [validation](xref:mvc/models/validation), and action results are shared.</span></span>  <!-- Review: Ryan, can we get a list of what is shared and what isn't? -->

<span data-ttu-id="8937a-175">Die vorherige `OnPostAsync`-Methode:</span><span class="sxs-lookup"><span data-stu-id="8937a-175">The previous `OnPostAsync` method:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="8937a-176">Der grundlegende Ablauf von `OnPostAsync`:</span><span class="sxs-lookup"><span data-stu-id="8937a-176">The basic flow of `OnPostAsync`:</span></span>

<span data-ttu-id="8937a-177">Prüfen auf Validierungsfehler</span><span class="sxs-lookup"><span data-stu-id="8937a-177">Check for validation errors.</span></span>

* <span data-ttu-id="8937a-178">Wenn keine Fehler vorliegen, werden die Daten gespeichert und weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="8937a-178">If there are no errors, save the data and redirect.</span></span>
* <span data-ttu-id="8937a-179">Wenn es Fehler gibt, zeigen Sie die Seite erneut mit den Validierungsmeldungen an.</span><span class="sxs-lookup"><span data-stu-id="8937a-179">If there are errors, show the page again with validation messages.</span></span> <span data-ttu-id="8937a-180">Die clientseitige Validierung ist identisch mit herkömmlichen ASP.NET Core MVC-Anwendungen,</span><span class="sxs-lookup"><span data-stu-id="8937a-180">Client-side validation is identical to traditional ASP.NET Core MVC applications.</span></span> <span data-ttu-id="8937a-181">denn Validierungsfehler werden oftmals auf dem Client erkannt und nie an den Server übermittelt.</span><span class="sxs-lookup"><span data-stu-id="8937a-181">In many cases, validation errors would be detected on the client, and never submitted to the server.</span></span>

<span data-ttu-id="8937a-182">Wenn die Daten erfolgreich eingegeben wurden, ruft die `OnPostAsync`-Handlermethode die `RedirectToPage`-Hilfsmethode auf, um eine Instanz von `RedirectToPageResult` zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="8937a-182">When the data is entered successfully, the `OnPostAsync` handler method calls the `RedirectToPage` helper method to return an instance of `RedirectToPageResult`.</span></span> <span data-ttu-id="8937a-183">`RedirectToPage` ist ein neues Aktionsergebnis und ähnelt `RedirectToAction` oder `RedirectToRoute`, ist aber für Seiten angepasst.</span><span class="sxs-lookup"><span data-stu-id="8937a-183">`RedirectToPage` is a new action result, similar to `RedirectToAction` or `RedirectToRoute`, but customized for pages.</span></span> <span data-ttu-id="8937a-184">Im vorhergehenden Beispiel leitet es an die Stammindexseite (`/Index`) weiter.</span><span class="sxs-lookup"><span data-stu-id="8937a-184">In the preceding sample, it redirects to the root Index page (`/Index`).</span></span> <span data-ttu-id="8937a-185">Informationen zu `RedirectToPage` finden Sie im Abschnitt [URL-Generierung für Seiten](#url_gen).</span><span class="sxs-lookup"><span data-stu-id="8937a-185">`RedirectToPage` is detailed in the [URL generation for Pages](#url_gen) section.</span></span>

<span data-ttu-id="8937a-186">Wenn das übermittelte Formular Validierungsfehler enthält (die an den Server übergeben wurden), ruft die `OnPostAsync`-Handlermethode die `Page`-Hilfsmethode auf.</span><span class="sxs-lookup"><span data-stu-id="8937a-186">When the submitted form has validation errors (that are passed to the server), the`OnPostAsync` handler method calls the `Page` helper method.</span></span> <span data-ttu-id="8937a-187">`Page` gibt eine Instanz von `PageResult` zurück.</span><span class="sxs-lookup"><span data-stu-id="8937a-187">`Page` returns an instance of `PageResult`.</span></span> <span data-ttu-id="8937a-188">Der Vorgang, bei dem `Page` zurückgegeben wird, ähnelt dem Vorgang, bei dem Aktionen im Controller `View` zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="8937a-188">Returning `Page` is similar to how actions in controllers return `View`.</span></span> <span data-ttu-id="8937a-189">`PageResult` ist der standardmäßige</span><span class="sxs-lookup"><span data-stu-id="8937a-189">`PageResult` is the default</span></span> <!-- Review  --> <span data-ttu-id="8937a-190">Rückgabetyp für eine Handlermethode.</span><span class="sxs-lookup"><span data-stu-id="8937a-190">return type for a handler method.</span></span> <span data-ttu-id="8937a-191">Eine Handlermethode, die `void` zurückgibt, rendert die Seite.</span><span class="sxs-lookup"><span data-stu-id="8937a-191">A handler method that returns `void` renders the page.</span></span>

<span data-ttu-id="8937a-192">Die Eigenschaft `Customer` verwendet das `[BindProperty]`-Attribut, um die Modellbindung zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="8937a-192">The `Customer` property uses `[BindProperty]` attribute to opt in to model binding.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

<span data-ttu-id="8937a-193">Razor Pages binden Eigenschaften standardmäßig nur an Nicht-GET-Verben.</span><span class="sxs-lookup"><span data-stu-id="8937a-193">Razor Pages, by default, bind properties only with non-GET verbs.</span></span> <span data-ttu-id="8937a-194">Durch die Bindung an Eigenschaften können Sie den Umfang von Codes reduzieren, den Sie schreiben müssen.</span><span class="sxs-lookup"><span data-stu-id="8937a-194">Binding to properties can reduce the amount of code you have to write.</span></span> <span data-ttu-id="8937a-195">Die Bindung reduziert den Code mithilfe der gleichen Eigenschaft, um Formularfelder (`<input asp-for="Customer.Name">`) zu rendern und die Eingabe zu akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="8937a-195">Binding reduces code by using the same property to render form fields (`<input asp-for="Customer.Name">`) and accept the input.</span></span>

[!INCLUDE[](~/includes/bind-get.md)]

<span data-ttu-id="8937a-196">Die Startseite (*Index.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="8937a-196">The home page (*Index.cshtml*):</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

<span data-ttu-id="8937a-197">Die zugeordnete `PageModel`-Klasse (*Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="8937a-197">The associated `PageModel` class (*Index.cshtml.cs*):</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

<span data-ttu-id="8937a-198">Die Datei *Index.cshtml* enthält das folgende Markup, um einen Bearbeitungslink für jeden Kontakt zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="8937a-198">The *Index.cshtml* file contains the following markup to create an edit link for each contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

<span data-ttu-id="8937a-199">Das [Anchor-Taghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) verwendet das Attribut `asp-route-{value}`, um einen Link zur Bearbeitungsseite zu generieren.</span><span class="sxs-lookup"><span data-stu-id="8937a-199">The [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) used the `asp-route-{value}` attribute to generate a link to the Edit page.</span></span> <span data-ttu-id="8937a-200">Der Link enthält die Routendaten mit der Kontakt-ID.</span><span class="sxs-lookup"><span data-stu-id="8937a-200">The link contains route data with the contact ID.</span></span> <span data-ttu-id="8937a-201">Beispielsweise `http://localhost:5000/Edit/1`.</span><span class="sxs-lookup"><span data-stu-id="8937a-201">For example, `http://localhost:5000/Edit/1`.</span></span> <span data-ttu-id="8937a-202">Verwenden Sie das `asp-area`-Attribut zur Angabe eines Bereichs.</span><span class="sxs-lookup"><span data-stu-id="8937a-202">Use the `asp-area` attribute to specify an area.</span></span> <span data-ttu-id="8937a-203">Weitere Informationen finden Sie unter <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="8937a-203">For more information, see <xref:mvc/controllers/areas>.</span></span>

<span data-ttu-id="8937a-204">Die Datei *Pages/Edit.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="8937a-204">The *Pages/Edit.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

<span data-ttu-id="8937a-205">Die erste Zeile enthält die `@page "{id:int}"`-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="8937a-205">The first line contains the `@page "{id:int}"` directive.</span></span> <span data-ttu-id="8937a-206">Die Routingbeschränkung `"{id:int}"` weist die Seite an, die Anforderungen für die Seite zu akzeptieren, die `int`-Routingdaten enthalten.</span><span class="sxs-lookup"><span data-stu-id="8937a-206">The routing constraint`"{id:int}"` tells the page to accept requests to the page that contain `int` route data.</span></span> <span data-ttu-id="8937a-207">Wenn eine Anforderung an die Seite bestimmte Routingdaten nicht enthält, die in einen `int` konvertiert werden können, gibt die Runtime einen Fehler vom Typ „HTTP 404: Nicht gefunden“ zurück.</span><span class="sxs-lookup"><span data-stu-id="8937a-207">If a request to the page doesn't contain route data that can be converted to an `int`, the runtime returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="8937a-208">Um die ID optional zu machen, fügen Sie `?` an die Routeneinschränkung an:</span><span class="sxs-lookup"><span data-stu-id="8937a-208">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="8937a-209">Die Datei *Pages/Edit.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="8937a-209">The *Pages/Edit.cshtml.cs* file:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

<span data-ttu-id="8937a-210">Die Datei *index.cshtml* enthält auch Markup zum Erstellen der Schaltfläche „Löschen“ für jeden benutzerdefinierten Kontakt:</span><span class="sxs-lookup"><span data-stu-id="8937a-210">The *Index.cshtml* file also contains markup to create a delete button for each customer contact:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

<span data-ttu-id="8937a-211">Wenn die „Löschen“-Schaltfläche in HTML gerendert wird, enthält ihr `formaction`-Element Parameter für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="8937a-211">When the delete button is rendered in HTML, its `formaction` includes parameters for:</span></span>

* <span data-ttu-id="8937a-212">Die benutzerdefinierte Kontakt-ID, die vom `asp-route-id`-Attribut angegeben wird</span><span class="sxs-lookup"><span data-stu-id="8937a-212">The customer contact ID specified by the `asp-route-id` attribute.</span></span>
* <span data-ttu-id="8937a-213">Der `handler`, der vom `asp-page-handler`-Attribut angegeben wird</span><span class="sxs-lookup"><span data-stu-id="8937a-213">The `handler` specified by the `asp-page-handler` attribute.</span></span>

<span data-ttu-id="8937a-214">Hier sehen Sie ein Beispiel für eine gerenderte „Löschen“-Schaltfläche mit einer benutzerdefinierten Kontakt-ID von `1`:</span><span class="sxs-lookup"><span data-stu-id="8937a-214">Here is an example of a rendered delete button with a customer contact ID of `1`:</span></span>

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

<span data-ttu-id="8937a-215">Wenn die Schaltfläche ausgewählt wird, wird eine `POST`-Anforderung an den Server gesendet.</span><span class="sxs-lookup"><span data-stu-id="8937a-215">When the button is selected, a form `POST` request is sent to the server.</span></span> <span data-ttu-id="8937a-216">Durch Konvention wird der Name der Handlermethode auf Grundlage des Werts des `handler`-Parameters gemäß dem Schema `OnPost[handler]Async` ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="8937a-216">By convention, the name of the handler method is selected based on the value of the `handler` parameter according to the scheme `OnPost[handler]Async`.</span></span>

<span data-ttu-id="8937a-217">Da der `handler` in diesem Beispiel `delete` ist, wird die Handlermethode `OnPostDeleteAsync` verwendet, um die `POST`-Anforderung zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="8937a-217">Because the `handler` is `delete` in this example, the `OnPostDeleteAsync` handler method is used to process the `POST` request.</span></span> <span data-ttu-id="8937a-218">Wenn der `asp-page-handler` auf einen anderen Wert festgelegt wird, wie z.B. `remove`, wird eine Seitenhandlermethode mit dem Namen `OnPostRemoveAsync` ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="8937a-218">If the `asp-page-handler` is set to a different value, such as `remove`, a page handler method with the name `OnPostRemoveAsync` is selected.</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

<span data-ttu-id="8937a-219">Die `OnPostDeleteAsync`-Methode:</span><span class="sxs-lookup"><span data-stu-id="8937a-219">The `OnPostDeleteAsync` method:</span></span>

* <span data-ttu-id="8937a-220">Akzeptiert die `id` der Abfragezeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="8937a-220">Accepts the `id` from the query string.</span></span>
* <span data-ttu-id="8937a-221">Fragt mit `FindAsync` die Datenbank nach dem Kundenkontakt ab.</span><span class="sxs-lookup"><span data-stu-id="8937a-221">Queries the database for the customer contact with `FindAsync`.</span></span>
* <span data-ttu-id="8937a-222">Wenn der Kundenkontakt gefunden wird, wird er aus der Liste der Kundenkontakte entfernt.</span><span class="sxs-lookup"><span data-stu-id="8937a-222">If the customer contact is found, they're removed from the list of customer contacts.</span></span> <span data-ttu-id="8937a-223">Die Datenbank wurde aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="8937a-223">The database is updated.</span></span>
* <span data-ttu-id="8937a-224">Ruft `RedirectToPage` auf, um die Stammindexseite (`/Index`) umzuleiten.</span><span class="sxs-lookup"><span data-stu-id="8937a-224">Calls `RedirectToPage` to redirect to the root Index page (`/Index`).</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="mark-page-properties-as-required"></a><span data-ttu-id="8937a-225">Markieren von Eigenschaften als „Required“ (Erforderlich)</span><span class="sxs-lookup"><span data-stu-id="8937a-225">Mark page properties as required</span></span>

<span data-ttu-id="8937a-226">Eigenschaften in einem `PageModel` können als [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) markiert werden:</span><span class="sxs-lookup"><span data-stu-id="8937a-226">Properties on a `PageModel` can be decorated with the [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute) attribute:</span></span>

[!code-cs[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

<span data-ttu-id="8937a-227">Weitere Informationen finden Sie unter [Modellvalidierung](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="8937a-227">For more information, see [Model validation](xref:mvc/models/validation).</span></span>

## <a name="manage-head-requests-with-the-onget-handler"></a><span data-ttu-id="8937a-228">Verwalten von HEAD-Anforderungen mit dem OnGet-Handler</span><span class="sxs-lookup"><span data-stu-id="8937a-228">Manage HEAD requests with the OnGet handler</span></span>

<span data-ttu-id="8937a-229">HEAD-Anforderungen gestatten Ihnen das Abrufen der Kopfzeilen für eine bestimmte Ressource.</span><span class="sxs-lookup"><span data-stu-id="8937a-229">HEAD requests allow you to retrieve the headers for a specific resource.</span></span> <span data-ttu-id="8937a-230">Im Gegensatz zu GET-Anforderungen geben HEAD-Anforderungen keinen Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="8937a-230">Unlike GET requests, HEAD requests don't return a response body.</span></span>

<span data-ttu-id="8937a-231">Normalerweise wird ein HEAD-Handler erstellt und für HEAD-Anforderungen aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="8937a-231">Ordinarily, a HEAD handler is created and called for HEAD requests:</span></span> 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

<span data-ttu-id="8937a-232">Wenn kein HEAD-Handler (`OnHead`) definiert ist, greift Razor Pages in ASP.NET Core 2.1 oder höher auf das Aufrufen des GET-Seitenhandlers (`OnGet`) zurück.</span><span class="sxs-lookup"><span data-stu-id="8937a-232">If no HEAD handler (`OnHead`) is defined, Razor Pages falls back to calling the GET page handler (`OnGet`) in ASP.NET Core 2.1 or later.</span></span> <span data-ttu-id="8937a-233">In ASP.NET Core 2.1 und 2.2 tritt dieses Verhalten mit der [SetCompatibilityVersion-Methode](xref:mvc/compatibility-version) in `Startup.Configure` auf:</span><span class="sxs-lookup"><span data-stu-id="8937a-233">In ASP.NET Core 2.1 and 2.2, this behavior occurs with the [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.Configure`:</span></span>

```csharp
services.AddMvc()
    .SetCompatibilityVersion(Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1);
```

<span data-ttu-id="8937a-234">Die Standardvorlagen generieren den `SetCompatibilityVersion`-Aufruf in ASP.NET Core 2.1 und 2.2.</span><span class="sxs-lookup"><span data-stu-id="8937a-234">The default templates generate the `SetCompatibilityVersion` call in ASP.NET Core 2.1 and 2.2.</span></span>

<span data-ttu-id="8937a-235">Tatsächlich setzt `SetCompatibilityVersion` die Razor Pages-Option `AllowMappingHeadRequestsToGetHandler` auf `true`.</span><span class="sxs-lookup"><span data-stu-id="8937a-235">`SetCompatibilityVersion` effectively sets the Razor Pages option `AllowMappingHeadRequestsToGetHandler` to `true`.</span></span>

<span data-ttu-id="8937a-236">Sie müssen sich nicht für alle Verhalten in `SetCompatibilityVersion` von Version 2.1 entscheiden, sondern können sich nur bestimmte Verhalten aussuchen.</span><span class="sxs-lookup"><span data-stu-id="8937a-236">Rather than opting into all 2.1 behaviors with `SetCompatibilityVersion`, you can explicitly opt-in to specific behaviors.</span></span> <span data-ttu-id="8937a-237">Mit dem folgenden Code aktivieren Sie das Zuordnen von HEAD-Anforderungen zu GET-Handlern.</span><span class="sxs-lookup"><span data-stu-id="8937a-237">The following code opts into the mapping HEAD requests to the GET handler.</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

::: moniker-end

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-razor-pages"></a><span data-ttu-id="8937a-238">XSRF/CSRF und Razor Pages</span><span class="sxs-lookup"><span data-stu-id="8937a-238">XSRF/CSRF and Razor Pages</span></span>

<span data-ttu-id="8937a-239">Sie müssen keinen Code für die [Antifälschungsvalidierung](xref:security/anti-request-forgery) schreiben.</span><span class="sxs-lookup"><span data-stu-id="8937a-239">You don't have to write any code for [antiforgery validation](xref:security/anti-request-forgery).</span></span> <span data-ttu-id="8937a-240">Die Generierung und Validierung von Antifälschungstoken ist automatisch in Razor Pages enthalten.</span><span class="sxs-lookup"><span data-stu-id="8937a-240">Antiforgery token generation and validation are automatically included in Razor Pages.</span></span>

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-razor-pages"></a><span data-ttu-id="8937a-241">Verwenden von Layouts, Teilansichten, Vorlagen und Taghilfsprogrammen mit Razor Pages</span><span class="sxs-lookup"><span data-stu-id="8937a-241">Using Layouts, partials, templates, and Tag Helpers with Razor Pages</span></span>

<span data-ttu-id="8937a-242">Razor Pages beinhaltet alle Funktionen der Razor-Anzeige-Engine.</span><span class="sxs-lookup"><span data-stu-id="8937a-242">Pages work with all the capabilities of the Razor view engine.</span></span> <span data-ttu-id="8937a-243">Layouts, Teilansichten, Vorlagen, Taghilfsprogramme, *_ViewStart.cshtml*, *_ViewImports.cshtml* funktionieren auf die gleiche Weise wie für herkömmliche Razor-Ansichten.</span><span class="sxs-lookup"><span data-stu-id="8937a-243">Layouts, partials, templates, Tag Helpers, *_ViewStart.cshtml*, *_ViewImports.cshtml* work in the same way they do for conventional Razor views.</span></span>

<span data-ttu-id="8937a-244">Strukturieren Sie diese Seite mit einigen dieser praktischen Funktionen.</span><span class="sxs-lookup"><span data-stu-id="8937a-244">Let's declutter this page by taking advantage of some of those capabilities.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="8937a-245">Fügen Sie der *Pages/Shared/_Layout.cshtml* eine [Layoutseite](xref:mvc/views/layout) hinzu:</span><span class="sxs-lookup"><span data-stu-id="8937a-245">Add a [layout page](xref:mvc/views/layout) to *Pages/Shared/_Layout.cshtml*:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="8937a-246">Fügen Sie einer *Pages/_Layout.cshtml* eine [Layoutseite](xref:mvc/views/layout) hinzu:</span><span class="sxs-lookup"><span data-stu-id="8937a-246">Add a [layout page](xref:mvc/views/layout) to *Pages/_Layout.cshtml*:</span></span>

::: moniker-end

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

<span data-ttu-id="8937a-247">Das [Layout](xref:mvc/views/layout):</span><span class="sxs-lookup"><span data-stu-id="8937a-247">The [Layout](xref:mvc/views/layout):</span></span>

* <span data-ttu-id="8937a-248">Steuert das Layout der einzelnen Seiten, es sei denn, das Layout wird für eine Seite deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="8937a-248">Controls the layout of each page (unless the page opts out of layout).</span></span>
* <span data-ttu-id="8937a-249">Importiert HTML-Strukturen, z.B. JavaScript und Stylesheets.</span><span class="sxs-lookup"><span data-stu-id="8937a-249">Imports HTML structures such as JavaScript and stylesheets.</span></span>

<span data-ttu-id="8937a-250">Weitere Informationen finden Sie unter [Layoutseite](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="8937a-250">See [layout page](xref:mvc/views/layout) for more information.</span></span>

<span data-ttu-id="8937a-251">Die Eigenschaft [Layout](xref:mvc/views/layout#specifying-a-layout) wird in *Pages/_ViewStart.cshtml* festgelegt:</span><span class="sxs-lookup"><span data-stu-id="8937a-251">The [Layout](xref:mvc/views/layout#specifying-a-layout) property is set in *Pages/_ViewStart.cshtml*:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="8937a-252">Das Layout befindet sich im Ordner *Pages/Shared*.</span><span class="sxs-lookup"><span data-stu-id="8937a-252">The layout is in the *Pages/Shared* folder.</span></span> <span data-ttu-id="8937a-253">Seiten suchen hierarchisch nach anderen Ansichten (Layouts, Vorlagen oder Teilansichten) und beginnen im gleichen Ordner wie die aktuelle Seite.</span><span class="sxs-lookup"><span data-stu-id="8937a-253">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="8937a-254">Ein Layout im Ordner *Pages/Shared* kann von jeder Razor-Seite aus unter dem Ordner *Pages* verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="8937a-254">A layout in the *Pages/Shared* folder can be used from any Razor page under the *Pages* folder.</span></span>

<span data-ttu-id="8937a-255">Die Layoutdatei sollte im Ordner *Pages/Shared* gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="8937a-255">The layout file should go in the *Pages/Shared* folder.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="8937a-256">Das Layout befindet sich im Ordner *Pages* (Seiten).</span><span class="sxs-lookup"><span data-stu-id="8937a-256">The layout is in the *Pages* folder.</span></span> <span data-ttu-id="8937a-257">Seiten suchen hierarchisch nach anderen Ansichten (Layouts, Vorlagen oder Teilansichten) und beginnen im gleichen Ordner wie die aktuelle Seite.</span><span class="sxs-lookup"><span data-stu-id="8937a-257">Pages look for other views (layouts, templates, partials) hierarchically, starting in the same folder as the current page.</span></span> <span data-ttu-id="8937a-258">Ein Layout im Ordner *Seiten* kann aus jeder Razor Page unter dem Ordner *Pages* verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="8937a-258">A layout in the *Pages* folder can be used from any Razor page under the *Pages* folder.</span></span>

::: moniker-end

<span data-ttu-id="8937a-259">Wir empfehlen Ihnen, die Layoutdatei **nicht** im Ordner *Views/Shared* (Ansichten/Freigegeben) zu platzieren.</span><span class="sxs-lookup"><span data-stu-id="8937a-259">We recommend you **not** put the layout file in the *Views/Shared* folder.</span></span> <span data-ttu-id="8937a-260">*Views/Shared* ist ein MVC-Ansichtsmuster.</span><span class="sxs-lookup"><span data-stu-id="8937a-260">*Views/Shared* is an MVC views pattern.</span></span> <span data-ttu-id="8937a-261">Razor Pages basieren auf der Ordnerhierarchie, nicht auf Pfadkonventionen.</span><span class="sxs-lookup"><span data-stu-id="8937a-261">Razor Pages are meant to rely on folder hierarchy, not path conventions.</span></span>

<span data-ttu-id="8937a-262">Die Ansichtensuche in einer Razor Page enthält den Ordner *Pages*.</span><span class="sxs-lookup"><span data-stu-id="8937a-262">View search from a Razor Page includes the *Pages* folder.</span></span> <span data-ttu-id="8937a-263">Die Layouts, Vorlagen und Teilansichten, die Sie mit MVC-Controllern und herkömmlichen Razor-Ansichten verwenden, *funktionieren einfach so*.</span><span class="sxs-lookup"><span data-stu-id="8937a-263">The layouts, templates, and partials you're using with MVC controllers and conventional Razor views *just work*.</span></span>

<span data-ttu-id="8937a-264">Fügen Sie eine Datei *Pages/_ViewImports.cshtml* hinzu:</span><span class="sxs-lookup"><span data-stu-id="8937a-264">Add a *Pages/_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

<span data-ttu-id="8937a-265">`@namespace` wird weiter unten im Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="8937a-265">`@namespace` is explained later in the tutorial.</span></span> <span data-ttu-id="8937a-266">Die `@addTagHelper`-Anweisung bringt die [integrierten Taghilfsprogramme](xref:mvc/views/tag-helpers/builtin-th/Index) zu allen Seiten in der Ordner *Pages*.</span><span class="sxs-lookup"><span data-stu-id="8937a-266">The `@addTagHelper` directive brings in the [built-in Tag Helpers](xref:mvc/views/tag-helpers/builtin-th/Index) to all the pages in the *Pages* folder.</span></span>

<a name="namespace"></a>

<span data-ttu-id="8937a-267">Wenn die `@namespace`-Anweisung explizit auf eine Seite angewendet wird:</span><span class="sxs-lookup"><span data-stu-id="8937a-267">When the `@namespace` directive is used explicitly on a page:</span></span>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

<span data-ttu-id="8937a-268">Die Anweisung legt den Namespace für die Seite fest.</span><span class="sxs-lookup"><span data-stu-id="8937a-268">The directive sets the namespace for the page.</span></span> <span data-ttu-id="8937a-269">Die `@model`-Anweisung muss den Namespace nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="8937a-269">The `@model` directive doesn't need to include the namespace.</span></span>

<span data-ttu-id="8937a-270">Wenn sich die `@namespace`-Anweisung in *_ViewImports.cshtml* befindet, stellt der angegebene Namespace das Präfix für den generierten Namespace auf der Seite bereit, die die `@namespace`-Anweisung importiert.</span><span class="sxs-lookup"><span data-stu-id="8937a-270">When the `@namespace` directive is contained in *_ViewImports.cshtml*, the specified namespace supplies the prefix for the generated namespace in the Page that imports the `@namespace` directive.</span></span> <span data-ttu-id="8937a-271">Der Rest der generierten Namespaces (der Suffixteil) ist der durch Punkte getrennte relative Pfad zwischen dem Ordner mit *_ViewImports.cshtml* und dem Ordner, der die Seite enthält.</span><span class="sxs-lookup"><span data-stu-id="8937a-271">The rest of the generated namespace (the suffix portion) is the dot-separated relative path between the folder containing *_ViewImports.cshtml* and the folder containing the page.</span></span>

<span data-ttu-id="8937a-272">Die `PageModel`-Klasse in *Pages/Customers/Edit.cshtml.cs* legt den Namespace z.B. explizit fest:</span><span class="sxs-lookup"><span data-stu-id="8937a-272">For example, the `PageModel` class *Pages/Customers/Edit.cshtml.cs* explicitly sets the namespace:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

<span data-ttu-id="8937a-273">Die Datei *Pages/_ViewImports.cshtml* legt den folgenden Namespace fest:</span><span class="sxs-lookup"><span data-stu-id="8937a-273">The *Pages/_ViewImports.cshtml* file sets the following namespace:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

<span data-ttu-id="8937a-274">Der generierte Namespace für die Razor Page *Pages/Customers/Edit.cshtml* ist identisch mit der `PageModel`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="8937a-274">The generated namespace for the *Pages/Customers/Edit.cshtml* Razor Page is the same as the `PageModel` class.</span></span>

<span data-ttu-id="8937a-275">`@namespace` *funktioniert auch mit konventionellen Razor-Ansichten.*</span><span class="sxs-lookup"><span data-stu-id="8937a-275">`@namespace` *also works with conventional Razor views.*</span></span>

<span data-ttu-id="8937a-276">Die ursprüngliche Umgebungsdatei *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="8937a-276">The original *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

<span data-ttu-id="8937a-277">Die aktualisierte Umgebungsdatei *Pages/Create.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="8937a-277">The updated *Pages/Create.cshtml* view file:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

<span data-ttu-id="8937a-278">Die [Razor Pages-Startprojekt](#rpvs17) enthält die Seite *Pages/_ValidationScriptsPartial.cshtml*, die die clientseitige Validierung bindet.</span><span class="sxs-lookup"><span data-stu-id="8937a-278">The [Razor Pages starter project](#rpvs17) contains the *Pages/_ValidationScriptsPartial.cshtml*, which hooks up client-side validation.</span></span>

<span data-ttu-id="8937a-279">Weitere Informationen zu Teilansichten finden Sie unter <xref:mvc/views/partial>.</span><span class="sxs-lookup"><span data-stu-id="8937a-279">For more information on partial views, see <xref:mvc/views/partial>.</span></span>

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a><span data-ttu-id="8937a-280">URL-Generierung für Seiten</span><span class="sxs-lookup"><span data-stu-id="8937a-280">URL generation for Pages</span></span>

<span data-ttu-id="8937a-281">Die zuvor gezeigte `Create`-Seite verwendet `RedirectToPage`:</span><span class="sxs-lookup"><span data-stu-id="8937a-281">The `Create` page, shown previously, uses `RedirectToPage`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

<span data-ttu-id="8937a-282">Die App hat die folgende Datei/Ordner-Struktur:</span><span class="sxs-lookup"><span data-stu-id="8937a-282">The app has the following file/folder structure:</span></span>

* <span data-ttu-id="8937a-283">*/Pages*</span><span class="sxs-lookup"><span data-stu-id="8937a-283">*/Pages*</span></span>

  * <span data-ttu-id="8937a-284">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="8937a-284">*Index.cshtml*</span></span>
  * <span data-ttu-id="8937a-285">*/Customers*</span><span class="sxs-lookup"><span data-stu-id="8937a-285">*/Customers*</span></span>

    * <span data-ttu-id="8937a-286">*Create.cshtml*</span><span class="sxs-lookup"><span data-stu-id="8937a-286">*Create.cshtml*</span></span>
    * <span data-ttu-id="8937a-287">*Edit.cshtml*</span><span class="sxs-lookup"><span data-stu-id="8937a-287">*Edit.cshtml*</span></span>
    * <span data-ttu-id="8937a-288">*Index.cshtml*</span><span class="sxs-lookup"><span data-stu-id="8937a-288">*Index.cshtml*</span></span>

<span data-ttu-id="8937a-289">Die Seiten *Pages/Customers/Create.cshtml* und *Pages/Customers/Edit.cshtml* leiten bei Erfolg an *Pages/Index.cshtml* weiter.</span><span class="sxs-lookup"><span data-stu-id="8937a-289">The *Pages/Customers/Create.cshtml* and *Pages/Customers/Edit.cshtml* pages redirect to *Pages/Index.cshtml* after success.</span></span> <span data-ttu-id="8937a-290">Die Zeichenfolge `/Index` ist Teil des URI, der auf die vorhergehende Seite zugreifen soll.</span><span class="sxs-lookup"><span data-stu-id="8937a-290">The string `/Index` is part of the URI to access the preceding page.</span></span> <span data-ttu-id="8937a-291">Die Zeichenfolge `/Index` kann für das Generieren von URIs für die Seite *Pages/Index.cshtml* verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="8937a-291">The string `/Index` can be used to generate URIs to the *Pages/Index.cshtml* page.</span></span> <span data-ttu-id="8937a-292">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8937a-292">For example:</span></span>

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

<span data-ttu-id="8937a-293">Der Seitenname ist der Pfad zu der Seite vom Stammordner */Pages* (einschließlich eines vorangestellten `/`, z.B. `/Index`).</span><span class="sxs-lookup"><span data-stu-id="8937a-293">The page name is the path to the page from the root */Pages* folder including a leading `/` (for example, `/Index`).</span></span> <span data-ttu-id="8937a-294">Die oben stehenden Beispiele für eine URL-Generierung bieten erweiterte Optionen und Funktionen, durch die Sie URLs nicht mehr hartcodieren müssen.</span><span class="sxs-lookup"><span data-stu-id="8937a-294">The preceding URL generation samples offer enhanced options and functional capabilities over hardcoding a URL.</span></span> <span data-ttu-id="8937a-295">Bei der URL-Generierung wird [Routing](xref:mvc/controllers/routing) verwendet. Außerdem können damit Parameter generiert und entsprechend der Definition der Route im Zielpfad codiert werden.</span><span class="sxs-lookup"><span data-stu-id="8937a-295">URL generation uses [routing](xref:mvc/controllers/routing) and can generate and encode parameters according to how the route is defined in the destination path.</span></span>

<span data-ttu-id="8937a-296">Die URL-Generierung für Seiten unterstützt relative Namen.</span><span class="sxs-lookup"><span data-stu-id="8937a-296">URL generation for pages supports relative names.</span></span> <span data-ttu-id="8937a-297">In der folgenden Tabelle wird dargestellt, welche Indexseite für verschiedene `RedirectToPage`-Parameter aus *Pages/Customers/Create.cshtml* ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="8937a-297">The following table shows which Index page is selected with different `RedirectToPage` parameters from *Pages/Customers/Create.cshtml*:</span></span>

| <span data-ttu-id="8937a-298">RedirectToPage(x)</span><span class="sxs-lookup"><span data-stu-id="8937a-298">RedirectToPage(x)</span></span>| <span data-ttu-id="8937a-299">Seite</span><span class="sxs-lookup"><span data-stu-id="8937a-299">Page</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="8937a-300">RedirectToPage("/Index")</span><span class="sxs-lookup"><span data-stu-id="8937a-300">RedirectToPage("/Index")</span></span> | <span data-ttu-id="8937a-301">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="8937a-301">*Pages/Index*</span></span> |
| <span data-ttu-id="8937a-302">RedirectToPage("./Index");</span><span class="sxs-lookup"><span data-stu-id="8937a-302">RedirectToPage("./Index");</span></span> | <span data-ttu-id="8937a-303">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="8937a-303">*Pages/Customers/Index*</span></span> |
| <span data-ttu-id="8937a-304">RedirectToPage("../Index")</span><span class="sxs-lookup"><span data-stu-id="8937a-304">RedirectToPage("../Index")</span></span> | <span data-ttu-id="8937a-305">*Pages/Index*</span><span class="sxs-lookup"><span data-stu-id="8937a-305">*Pages/Index*</span></span> |
| <span data-ttu-id="8937a-306">RedirectToPage("Index")</span><span class="sxs-lookup"><span data-stu-id="8937a-306">RedirectToPage("Index")</span></span>  | <span data-ttu-id="8937a-307">*Pages/Customers/Index*</span><span class="sxs-lookup"><span data-stu-id="8937a-307">*Pages/Customers/Index*</span></span> |

<span data-ttu-id="8937a-308">`RedirectToPage("Index")`, `RedirectToPage("./Index")` und `RedirectToPage("../Index")` sind *relative Namen*.</span><span class="sxs-lookup"><span data-stu-id="8937a-308">`RedirectToPage("Index")`, `RedirectToPage("./Index")`, and `RedirectToPage("../Index")`  are *relative names*.</span></span> <span data-ttu-id="8937a-309">Der `RedirectToPage`-Parameter wird mit dem Pfad der aktuellen Seite *kombiniert*, um den Namen der Zielseite zu berechnen.</span><span class="sxs-lookup"><span data-stu-id="8937a-309">The `RedirectToPage` parameter is *combined* with the path of the current page to compute the name of the destination page.</span></span>  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

<span data-ttu-id="8937a-310">Das Verknüpfen relativer Namen eignet sich beim Erstellen von Websites mit einer komplexen Struktur.</span><span class="sxs-lookup"><span data-stu-id="8937a-310">Relative name linking is useful when building sites with a complex structure.</span></span> <span data-ttu-id="8937a-311">Wenn Sie relative Namen verwenden, um Seiten in einem Ordner zu verknüpfen, können Sie diesen Ordner umbenennen.</span><span class="sxs-lookup"><span data-stu-id="8937a-311">If you use relative names to link between pages in a folder, you can rename that folder.</span></span> <span data-ttu-id="8937a-312">Alle Links funktionieren weiterhin, da sie nicht den Namen des Ordners enthalten.</span><span class="sxs-lookup"><span data-stu-id="8937a-312">All the links still work (because they didn't include the folder name).</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="8937a-313">Um auf eine Seite in einem anderen [Bereich](xref:mvc/controllers/areas) umzuleiten, geben Sie den Bereich an:</span><span class="sxs-lookup"><span data-stu-id="8937a-313">To redirect to a page in a different [Area](xref:mvc/controllers/areas), specify the area:</span></span>

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

<span data-ttu-id="8937a-314">Weitere Informationen finden Sie unter <xref:mvc/controllers/areas>.</span><span class="sxs-lookup"><span data-stu-id="8937a-314">For more information, see <xref:mvc/controllers/areas>.</span></span>

## <a name="viewdata-attribute"></a><span data-ttu-id="8937a-315">Attribut „ViewData“</span><span class="sxs-lookup"><span data-stu-id="8937a-315">ViewData attribute</span></span>

<span data-ttu-id="8937a-316">Daten können mit [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute) an eine Seite übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="8937a-316">Data can be passed to a page with [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="8937a-317">Die Werte der Eigenschaften auf Controllern oder Razor Pages-Modellen, die mit `[ViewData]` versehen sind, werden in [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) gespeichert und daraus geladen.</span><span class="sxs-lookup"><span data-stu-id="8937a-317">Properties on controllers or Razor Page models decorated with `[ViewData]` have their values stored and loaded from the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary).</span></span>

<span data-ttu-id="8937a-318">Im folgenden Beispiel enthält `AboutModel` die Eigenschaft `Title`, die mit `[ViewData]` versehen ist.</span><span class="sxs-lookup"><span data-stu-id="8937a-318">In the following example, the `AboutModel` contains a `Title` property decorated with `[ViewData]`.</span></span> <span data-ttu-id="8937a-319">Die Eigenschaft `Title` wird auf den Titel der Infoseite festgelegt:</span><span class="sxs-lookup"><span data-stu-id="8937a-319">The `Title` property is set to the title of the About page:</span></span>

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

<span data-ttu-id="8937a-320">Greifen Sie auf der Infoseite auf die Eigenschaft `Title` als Modelleigenschaft zu:</span><span class="sxs-lookup"><span data-stu-id="8937a-320">In the About page, access the `Title` property as a model property:</span></span>

```cshtml
<h1>@Model.Title</h1>
```

<span data-ttu-id="8937a-321">Im Layout wird der Titel aus dem ViewData-Wörterbuch gelesen:</span><span class="sxs-lookup"><span data-stu-id="8937a-321">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

::: moniker-end

## <a name="tempdata"></a><span data-ttu-id="8937a-322">TempData</span><span class="sxs-lookup"><span data-stu-id="8937a-322">TempData</span></span>

<span data-ttu-id="8937a-323">ASP.NET Core macht die Eigenschaft [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) auf einem [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller) verfügbar.</span><span class="sxs-lookup"><span data-stu-id="8937a-323">ASP.NET Core exposes the [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) property on a [controller](/dotnet/api/microsoft.aspnetcore.mvc.controller).</span></span> <span data-ttu-id="8937a-324">Diese Eigenschaft speichert Daten, bis sie gelesen wurden.</span><span class="sxs-lookup"><span data-stu-id="8937a-324">This property stores data until it's read.</span></span> <span data-ttu-id="8937a-325">Die Methoden `Keep` und `Peek` können verwendet werden, um die Daten zu überprüfen, ohne sie zu löschen.</span><span class="sxs-lookup"><span data-stu-id="8937a-325">The `Keep` and `Peek` methods can be used to examine the data without deletion.</span></span> <span data-ttu-id="8937a-326">`TempData` eignet sich für die Weiterleitung, wenn Daten für mehr als eine Anforderung benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="8937a-326">`TempData` is  useful for redirection, when data is needed for more than a single request.</span></span>

<span data-ttu-id="8937a-327">Das `[TempData]`-Attribut ist neu in ASP.NET Core 2.0 und wird auf Domänencontrollern und Seiten unterstützt.</span><span class="sxs-lookup"><span data-stu-id="8937a-327">The `[TempData]` attribute is new in ASP.NET Core 2.0 and is supported on controllers and pages.</span></span>

<span data-ttu-id="8937a-328">Im folgenden Code wird der Wert von `Message` mit `TempData` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="8937a-328">The following code sets the value of `Message` using `TempData`:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

<span data-ttu-id="8937a-329">Das folgende Markup in der Datei *Pages/Customers/Index.cshtml* zeigt den Wert von `Message` mit `TempData` an.</span><span class="sxs-lookup"><span data-stu-id="8937a-329">The following markup in the *Pages/Customers/Index.cshtml* file displays the value of `Message` using `TempData`.</span></span>

```cshtml
<h3>Msg: @Model.Message</h3>
```

<span data-ttu-id="8937a-330">Das Seitenmodell *Pages/Customers/Index.cshtml.cs* wendet das `[TempData]`-Attribut auf die Eigenschaft `Message` an.</span><span class="sxs-lookup"><span data-stu-id="8937a-330">The *Pages/Customers/Index.cshtml.cs* page model applies the `[TempData]` attribute to the `Message` property.</span></span>

```cs
[TempData]
public string Message { get; set; }
```

<span data-ttu-id="8937a-331">Weitere Informationen finden Sie unter [TempData](xref:fundamentals/app-state#tempdata).</span><span class="sxs-lookup"><span data-stu-id="8937a-331">For more information, see [TempData](xref:fundamentals/app-state#tempdata) .</span></span>

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a><span data-ttu-id="8937a-332">Mehrere Handler pro Seite</span><span class="sxs-lookup"><span data-stu-id="8937a-332">Multiple handlers per page</span></span>

<span data-ttu-id="8937a-333">Die folgende Seite generiert mit dem `asp-page-handler`-Taghilfsprogramm Markup für zwei Handler:</span><span class="sxs-lookup"><span data-stu-id="8937a-333">The following page generates markup for two page handlers using the `asp-page-handler` Tag Helper:</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

<span data-ttu-id="8937a-334">Das Formular im vorherigen Beispiel hat zwei Sendeschaltflächen, und jede verwendet `FormActionTagHelper`, um an eine andere URL zu übermitteln.</span><span class="sxs-lookup"><span data-stu-id="8937a-334">The form in the preceding example has two submit buttons, each using the `FormActionTagHelper` to submit to a different URL.</span></span> <span data-ttu-id="8937a-335">Das `asp-page-handler`-Attribut ist eine Ergänzung für `asp-page`.</span><span class="sxs-lookup"><span data-stu-id="8937a-335">The `asp-page-handler` attribute is a companion to `asp-page`.</span></span> <span data-ttu-id="8937a-336">`asp-page-handler` generiert URLs, die als Übermittlungsziel jeweils die durch eine Seite festgelegte Handlermethode verwenden.</span><span class="sxs-lookup"><span data-stu-id="8937a-336">`asp-page-handler` generates URLs that submit to each of the handler methods defined by a page.</span></span> <span data-ttu-id="8937a-337">`asp-page` wird nicht angegeben, weil das Beispiel mit der aktuellen Seite verknüpft.</span><span class="sxs-lookup"><span data-stu-id="8937a-337">`asp-page` isn't specified because the sample is linking to the current page.</span></span>

<span data-ttu-id="8937a-338">Das Seitenmodell:</span><span class="sxs-lookup"><span data-stu-id="8937a-338">The page model:</span></span>

[!code-cs[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

<span data-ttu-id="8937a-339">Der vorherige Code verwendet *benannte Handlermethoden*.</span><span class="sxs-lookup"><span data-stu-id="8937a-339">The preceding code uses *named handler methods*.</span></span> <span data-ttu-id="8937a-340">Benannte Handlermethoden werden aus dem Text im Namen nach `On<HTTP Verb>` und vor `Async` (falls vorhanden) erstellt.</span><span class="sxs-lookup"><span data-stu-id="8937a-340">Named handler methods are created by taking the text in the name after `On<HTTP Verb>` and before `Async` (if present).</span></span> <span data-ttu-id="8937a-341">Im vorherigen Beispiel sind OnPost**JoinList**Async und OnPost**JoinListUC**Async die Seitenmethoden.</span><span class="sxs-lookup"><span data-stu-id="8937a-341">In the preceding example, the page methods are OnPost**JoinList**Async and OnPost**JoinListUC**Async.</span></span> <span data-ttu-id="8937a-342">Wenn Sie *OnPost* und *Async* entfernen, lauten die Handlernamen `JoinList` und `JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="8937a-342">With *OnPost* and *Async* removed, the handler names are `JoinList` and `JoinListUC`.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

<span data-ttu-id="8937a-343">Mit dem vorherigen Code lautet der URL-Pfad, der an `OnPostJoinListAsync` übermittelt, `http://localhost:5000/Customers/CreateFATH?handler=JoinList`.</span><span class="sxs-lookup"><span data-stu-id="8937a-343">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `http://localhost:5000/Customers/CreateFATH?handler=JoinList`.</span></span> <span data-ttu-id="8937a-344">Der URL-Pfad, der an `OnPostJoinListUCAsync` übermittelt, lautet `http://localhost:5000/Customers/CreateFATH?handler=JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="8937a-344">The URL path that submits to `OnPostJoinListUCAsync` is `http://localhost:5000/Customers/CreateFATH?handler=JoinListUC`.</span></span>

## <a name="custom-routes"></a><span data-ttu-id="8937a-345">Benutzerdefinierte Routen</span><span class="sxs-lookup"><span data-stu-id="8937a-345">Custom routes</span></span>

<span data-ttu-id="8937a-346">Verwenden Sie die `@page`-Anweisung für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="8937a-346">Use the `@page` directive to:</span></span>

* <span data-ttu-id="8937a-347">Das Angeben einer benutzerdefinierten Route zu einer Seite.</span><span class="sxs-lookup"><span data-stu-id="8937a-347">Specify a custom route to a page.</span></span> <span data-ttu-id="8937a-348">Die Route zur Seite „Info“ kann mit `@page "/Some/Other/Path"` beispielsweise auf `/Some/Other/Path` festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="8937a-348">For example, the route to the About page can be set to `/Some/Other/Path` with `@page "/Some/Other/Path"`.</span></span>
* <span data-ttu-id="8937a-349">Das Anfügen von Segmenten an die Standardroute einer Seite.</span><span class="sxs-lookup"><span data-stu-id="8937a-349">Append segments to a page's default route.</span></span> <span data-ttu-id="8937a-350">Mit `@page "item"` kann beispielsweise ein item-Segment an die Standardroute der Seite angefügt werden.</span><span class="sxs-lookup"><span data-stu-id="8937a-350">For example, an "item" segment can be added to a page's default route with `@page "item"`.</span></span>
* <span data-ttu-id="8937a-351">Das Anfügen von Parametern an die Standardroute einer Seite.</span><span class="sxs-lookup"><span data-stu-id="8937a-351">Append parameters to a page's default route.</span></span> <span data-ttu-id="8937a-352">Mit `@page "{id}"` kann beispielsweise ein ID-Parameter (`id`) für eine Seite angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="8937a-352">For example, an ID parameter, `id`, can be required for a page with `@page "{id}"`.</span></span>

<span data-ttu-id="8937a-353">Es wird ein relativer Pfad zum Stamm unterstützt, der durch eine Tilde (`~`) festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="8937a-353">A root-relative path designated by a tilde (`~`) at the beginning of the path is supported.</span></span> <span data-ttu-id="8937a-354">`@page "~/Some/Other/Path"` entspricht beispielsweise `@page "/Some/Other/Path"`.</span><span class="sxs-lookup"><span data-stu-id="8937a-354">For example, `@page "~/Some/Other/Path"` is the same as `@page "/Some/Other/Path"`.</span></span>

<span data-ttu-id="8937a-355">Sie können die Abfragezeichenfolge `?handler=JoinList` in der URL in ein Routensegment (`/JoinList`) ändern, indem Sie die Routenvorlage `@page "{handler?}"` angeben.</span><span class="sxs-lookup"><span data-stu-id="8937a-355">You can change the query string `?handler=JoinList` in the URL to a route segment `/JoinList` by specifying the route template `@page "{handler?}"`.</span></span>

<span data-ttu-id="8937a-356">Wenn Sie nicht möchten, dass die Abfragezeichenfolge `?handler=JoinList` in der URL enthalten ist, können Sie die Route ändern, damit der Handlername im Pfadteil der URL eingefügt wird.</span><span class="sxs-lookup"><span data-stu-id="8937a-356">If you don't like the query string `?handler=JoinList` in the URL, you can change the route to put the handler name in the path portion of the URL.</span></span> <span data-ttu-id="8937a-357">Sie können die Route anpassen, indem Sie eine Routenvorlage in doppelten Anführungszeichen nach der `@page`-Anweisung hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="8937a-357">You can customize the route by adding a route template enclosed in double quotes after the `@page` directive.</span></span>

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

<span data-ttu-id="8937a-358">Mit dem vorherigen Code lautet der URL-Pfad, der an `OnPostJoinListAsync` übermittelt, `http://localhost:5000/Customers/CreateFATH/JoinList`.</span><span class="sxs-lookup"><span data-stu-id="8937a-358">Using the preceding code, the URL path that submits to `OnPostJoinListAsync` is `http://localhost:5000/Customers/CreateFATH/JoinList`.</span></span> <span data-ttu-id="8937a-359">Der URL-Pfad, der an `OnPostJoinListUCAsync` übermittelt, lautet `http://localhost:5000/Customers/CreateFATH/JoinListUC`.</span><span class="sxs-lookup"><span data-stu-id="8937a-359">The URL path that submits to `OnPostJoinListUCAsync` is `http://localhost:5000/Customers/CreateFATH/JoinListUC`.</span></span>

<span data-ttu-id="8937a-360">Das `?` nach `handler` bedeutet, dass der Routenparameter optional ist.</span><span class="sxs-lookup"><span data-stu-id="8937a-360">The `?` following `handler` means the route parameter is optional.</span></span>

## <a name="configuration-and-settings"></a><span data-ttu-id="8937a-361">Konfiguration und Einstellungen</span><span class="sxs-lookup"><span data-stu-id="8937a-361">Configuration and settings</span></span>

<span data-ttu-id="8937a-362">Um die erweiterten Optionen zu konfigurieren, verwenden Sie die Erweiterungsmethode `AddRazorPagesOptions` auf dem MVC-Generator:</span><span class="sxs-lookup"><span data-stu-id="8937a-362">To configure advanced options, use the extension method `AddRazorPagesOptions` on the MVC builder:</span></span>

[!code-cs[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

<span data-ttu-id="8937a-363">Derzeit können Sie `RazorPagesOptions` verwenden, um das Stammverzeichnis für Seiten festzulegen oder Anwendungsmodellkonventionen für Seiten hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="8937a-363">Currently you can use the `RazorPagesOptions` to set the root directory for pages, or add application model conventions for pages.</span></span> <span data-ttu-id="8937a-364">Auf diese Weise wird in Zukunft eine höhere Erweiterbarkeit erreicht.</span><span class="sxs-lookup"><span data-stu-id="8937a-364">We'll enable more extensibility this way in the future.</span></span>

<span data-ttu-id="8937a-365">Informationen zum Vorkompilieren von Ansichten finden Sie unter [Razor view compilation and precompilation in ASP.NET Core (Razor-Ansichtenkompilierung und Vorkompilierung in ASP.NET)](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="8937a-365">To precompile views, see [Razor view compilation](xref:mvc/views/view-compilation) .</span></span>

<span data-ttu-id="8937a-366">[Laden Sie Beispielcode herunter, oder zeigen Sie ihn an](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span><span class="sxs-lookup"><span data-stu-id="8937a-366">[Download or view sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).</span></span>

<span data-ttu-id="8937a-367">Lesen Sie auch den Artikel [Erste Schritte mit Razor-Seiten](xref:tutorials/razor-pages/razor-pages-start), der auf dieser Einführung aufbaut.</span><span class="sxs-lookup"><span data-stu-id="8937a-367">See [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start), which builds on this introduction.</span></span>

### <a name="specify-that-razor-pages-are-at-the-content-root"></a><span data-ttu-id="8937a-368">Festlegen des Inhaltsstammverzeichnisses für Razor Pages</span><span class="sxs-lookup"><span data-stu-id="8937a-368">Specify that Razor Pages are at the content root</span></span>

<span data-ttu-id="8937a-369">Standardmäßig lautet das Stammverzeichnis für Razor Pages */Pages*.</span><span class="sxs-lookup"><span data-stu-id="8937a-369">By default, Razor Pages are rooted in the */Pages* directory.</span></span> <span data-ttu-id="8937a-370">Fügen Sie [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) zu [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) hinzu, um anzugeben, dass sich Ihre Razor-Dateien im Inhaltsstammverzeichnis ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) der App befinden:</span><span class="sxs-lookup"><span data-stu-id="8937a-370">Add [WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at the content root ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) of the app:</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-razor-pages-are-at-a-custom-root-directory"></a><span data-ttu-id="8937a-371">Festlegen eines benutzerdefinierten Stammverzeichnisses für Razor Pages</span><span class="sxs-lookup"><span data-stu-id="8937a-371">Specify that Razor Pages are at a custom root directory</span></span>

<span data-ttu-id="8937a-372">Fügen Sie [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) zu [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) hinzu, um anzugeben, dass Ihre Razor Pages sich in einem benutzerdefinierten Stammverzeichnis in der App befinden (geben Sie einen relativen Pfad an):</span><span class="sxs-lookup"><span data-stu-id="8937a-372">Add [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) to [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) to specify that your Razor Pages are at a custom root directory in the app (provide a relative path):</span></span>

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a><span data-ttu-id="8937a-373">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="8937a-373">Additional resources</span></span>

* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
