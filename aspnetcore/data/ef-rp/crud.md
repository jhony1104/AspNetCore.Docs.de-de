---
title: 'Razor-Seiten mit EF Core in ASP.NET Core: CRUD (2 von 8)'
author: rick-anderson
description: In diesem Tutorial wird veranschaulicht, wie mit EF Core Erstellungs-, Lese-, Aktualisierungs- und Löschvorgänge durchgeführt werden
ms.author: riande
ms.date: 6/31/2017
uid: data/ef-rp/crud
ms.openlocfilehash: c104498a4411ac36672ffbb9660948d4e96bf1ee
ms.sourcegitcommit: 10e14b85490f064395e9b2f423d21e3c2d39ed8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58142393"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---crud---2-of-8"></a><span data-ttu-id="22ec5-103">Razor-Seiten mit EF Core in ASP.NET Core: CRUD (2 von 8)</span><span class="sxs-lookup"><span data-stu-id="22ec5-103">Razor Pages with EF Core in ASP.NET Core - CRUD - 2 of 8</span></span>

[!INCLUDE[2.0 version](~/includes/RP-EF/20-pdf.md)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="22ec5-104">Von [Tom Dykstra](https://github.com/tdykstra), [Jon P. Smith](https://twitter.com/thereformedprog) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="22ec5-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

<span data-ttu-id="22ec5-105">In diesem Tutorial wird der erstellte CRUD-Code (CRUD = Create, Read, Update, Delete; Erstellen, Lesen, Aktualisieren, Löschen) überprüft und angepasst.</span><span class="sxs-lookup"><span data-stu-id="22ec5-105">In this tutorial, the scaffolded CRUD (create, read, update, delete) code is reviewed and customized.</span></span>

<span data-ttu-id="22ec5-106">Zur Minimierung der Komplexität und damit EF Core im Fokus dieser Tutorials bleibt, wird in den Seitenmodellen EF Core-Code verwendet.</span><span class="sxs-lookup"><span data-stu-id="22ec5-106">To minimize complexity and keep these tutorials focused on EF Core, EF Core code is used in the page models.</span></span> <span data-ttu-id="22ec5-107">Einige Entwickler verwenden eine Dienstschicht oder ein Repositorymuster für die Erstellung einer Abstraktionsschicht zwischen der Benutzeroberfläche (Razor Pages) und der Datenzugriffsschicht.</span><span class="sxs-lookup"><span data-stu-id="22ec5-107">Some developers use a service layer or repository pattern in to create an abstraction layer between the UI (Razor Pages) and the data access layer.</span></span>

<span data-ttu-id="22ec5-108">In diesem Tutorial werden die Razor Pages „Create“ (Erstellen), „Edit“ (Bearbeiten), „Delete“ (Löschen) und „Details“ im Ordner *Students* erläutert.</span><span class="sxs-lookup"><span data-stu-id="22ec5-108">In this tutorial, the Create, Edit, Delete, and Details Razor Pages in the *Students* folder are examined.</span></span>

<span data-ttu-id="22ec5-109">Der erstellte Code verwendet folgendes Muster für die Seiten „Create“ (Erstellen), „Edit“ (Bearbeiten) und „Delete“ (Löschen):</span><span class="sxs-lookup"><span data-stu-id="22ec5-109">The scaffolded code uses the following pattern for Create, Edit, and Delete pages:</span></span>

* <span data-ttu-id="22ec5-110">Abrufen und Anzeigen der angeforderten Daten mit der HTTP GET-Methode `OnGetAsync`.</span><span class="sxs-lookup"><span data-stu-id="22ec5-110">Get and display the requested data with the HTTP GET method `OnGetAsync`.</span></span>
* <span data-ttu-id="22ec5-111">Speichern der an Daten vorgenommenen Änderungen mit der HTTP POST-Methode `OnPostAsync`.</span><span class="sxs-lookup"><span data-stu-id="22ec5-111">Save changes to the data with the HTTP POST method `OnPostAsync`.</span></span>

<span data-ttu-id="22ec5-112">Auf den Seiten „Index“ und „Details“ werden die angeforderten Daten mit der HTTP GET-Methode `OnGetAsync` abgerufen und angezeigt.</span><span class="sxs-lookup"><span data-stu-id="22ec5-112">The Index and Details pages get and display the requested data with the HTTP GET method `OnGetAsync`</span></span>

## <a name="singleordefaultasync-vs-firstordefaultasync"></a><span data-ttu-id="22ec5-113">„SingleOrDefaultAsync“ im Vergleich zu „FirstOrDefaultAsync“</span><span class="sxs-lookup"><span data-stu-id="22ec5-113">SingleOrDefaultAsync vs. FirstOrDefaultAsync</span></span>

<span data-ttu-id="22ec5-114">Der generierte Code verwendet [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_). Im Allgemeinen wird dies [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) vorgezogen.</span><span class="sxs-lookup"><span data-stu-id="22ec5-114">The generated code uses [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Threading_CancellationToken_), which is generally preferred over [SingleOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).</span></span>

 <span data-ttu-id="22ec5-115">`FirstOrDefaultAsync` ist beim Abrufen einer Entität effizienter als `SingleOrDefaultAsync`:</span><span class="sxs-lookup"><span data-stu-id="22ec5-115">`FirstOrDefaultAsync` is more efficient than `SingleOrDefaultAsync` at fetching one entity:</span></span>

* <span data-ttu-id="22ec5-116">Sofern mit dem Code nicht überprüft werden muss, ob von der Abfrage mehrere Entitäten zurückgegeben wurden.</span><span class="sxs-lookup"><span data-stu-id="22ec5-116">Unless the code needs to verify that there's not more than one entity returned from the query.</span></span>
* <span data-ttu-id="22ec5-117">`SingleOrDefaultAsync` ruft weitere Daten ab und führt unnötige Arbeiten aus.</span><span class="sxs-lookup"><span data-stu-id="22ec5-117">`SingleOrDefaultAsync` fetches more data and does unnecessary work.</span></span>
* <span data-ttu-id="22ec5-118">`SingleOrDefaultAsync` löst eine Ausnahme aus, wenn mehrere Entitäten in den Filterabschnitt passen.</span><span class="sxs-lookup"><span data-stu-id="22ec5-118">`SingleOrDefaultAsync` throws an exception if there's more than one entity that fits the filter part.</span></span>
* <span data-ttu-id="22ec5-119">`FirstOrDefaultAsync` löst keine Ausnahme aus, wenn mehrere Entitäten in den Filterabschnitt passen.</span><span class="sxs-lookup"><span data-stu-id="22ec5-119">`FirstOrDefaultAsync` doesn't throw if there's more than one entity that fits the filter part.</span></span>

<a name="FindAsync"></a>

### <a name="findasync"></a><span data-ttu-id="22ec5-120">FindAsync</span><span class="sxs-lookup"><span data-stu-id="22ec5-120">FindAsync</span></span>

<span data-ttu-id="22ec5-121">In einem Großteil des Codes kann [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) anstelle von `FirstOrDefaultAsync` verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="22ec5-121">In much of the scaffolded code, [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.findasync#Microsoft_EntityFrameworkCore_DbContext_FindAsync_System_Type_System_Object___) can be used in place of `FirstOrDefaultAsync`.</span></span>

<span data-ttu-id="22ec5-122">`FindAsync`:</span><span class="sxs-lookup"><span data-stu-id="22ec5-122">`FindAsync`:</span></span>

* <span data-ttu-id="22ec5-123">Sucht eine Entität mit dem Primärschlüssel (PS).</span><span class="sxs-lookup"><span data-stu-id="22ec5-123">Finds an entity with the primary key (PK).</span></span> <span data-ttu-id="22ec5-124">Wenn eine Entität mit dem PS vom Kontext nachverfolgt wird, wird sie ohne eine Anforderung an die Datenbank zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="22ec5-124">If an entity with the PK is being tracked by the context, it's returned without a request to the DB.</span></span>
* <span data-ttu-id="22ec5-125">Ist einfach und präzise.</span><span class="sxs-lookup"><span data-stu-id="22ec5-125">Is simple and concise.</span></span>
* <span data-ttu-id="22ec5-126">Wurde für die Suche nach einer einzelnen Entität optimiert.</span><span class="sxs-lookup"><span data-stu-id="22ec5-126">Is optimized to look up a single entity.</span></span>
* <span data-ttu-id="22ec5-127">Kann manchmal Leistungsvorteile haben, für normale Web-Apps ist dies jedoch selten der Fall.</span><span class="sxs-lookup"><span data-stu-id="22ec5-127">Can have perf benefits in some situations, but that rarely happens for typical web apps.</span></span>
* <span data-ttu-id="22ec5-128">Verwendet implizit [FirstAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) anstelle von [SingleAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).</span><span class="sxs-lookup"><span data-stu-id="22ec5-128">Implicitly uses [FirstAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_) instead of [SingleAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.singleasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_SingleAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_).</span></span>

<span data-ttu-id="22ec5-129">Wenn Sie jedoch weitere Entitäten mithilfe von `Include` einschließen möchten, ist `FindAsync` jedoch nicht mehr sinnvoll.</span><span class="sxs-lookup"><span data-stu-id="22ec5-129">But if you want to `Include` other entities, then `FindAsync` is no longer appropriate.</span></span> <span data-ttu-id="22ec5-130">Dies bedeutet, dass Sie `FindAsync` möglicherweise abbrechen und in eine Abfrage verschieben müssen, während Ihre App weiter ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="22ec5-130">This means that you may need to abandon `FindAsync` and move to a query as your app progresses.</span></span>

## <a name="customize-the-details-page"></a><span data-ttu-id="22ec5-131">Anpassen der Seite „Details“</span><span class="sxs-lookup"><span data-stu-id="22ec5-131">Customize the Details page</span></span>

<span data-ttu-id="22ec5-132">Navigieren Sie zur Seite `Pages/Students`.</span><span class="sxs-lookup"><span data-stu-id="22ec5-132">Browse to `Pages/Students` page.</span></span> <span data-ttu-id="22ec5-133">Die Links **Bearbeiten**, **Details** und **Löschen** werden mithilfe des [Anchor-Taghilfsprogramms](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in der Datei *Pages/Movies/Index.cshtml* generiert.</span><span class="sxs-lookup"><span data-stu-id="22ec5-133">The **Edit**, **Details**, and **Delete** links are generated by the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) in the *Pages/Students/Index.cshtml* file.</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Students/Index1.cshtml?name=snippet)]

<span data-ttu-id="22ec5-134">Führen Sie die App aus, und klicken Sie auf den Link **Details**.</span><span class="sxs-lookup"><span data-stu-id="22ec5-134">Run the app and select a **Details** link.</span></span> <span data-ttu-id="22ec5-135">Die URL weist das Format `http://localhost:5000/Students/Details?id=2` auf.</span><span class="sxs-lookup"><span data-stu-id="22ec5-135">The URL is of the form `http://localhost:5000/Students/Details?id=2`.</span></span> <span data-ttu-id="22ec5-136">Die Studenten-ID wird mit einer Abfragezeichenfolge (`?id=2`) übergeben.</span><span class="sxs-lookup"><span data-stu-id="22ec5-136">The Student ID is passed using a query string (`?id=2`).</span></span>

<span data-ttu-id="22ec5-137">Aktualisieren Sie die Razor Pages „Edit“ (Bearbeiten), „Details“ und „Delete“ (Löschen) so, dass die Routenvorlage `"{id:int}"` verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="22ec5-137">Update the Edit, Details, and Delete Razor Pages to use the `"{id:int}"` route template.</span></span> <span data-ttu-id="22ec5-138">Ändern Sie die „page“-Direktive für jede dieser Seiten aus `@page` in `@page "{id:int}"`.</span><span class="sxs-lookup"><span data-stu-id="22ec5-138">Change the page directive for each of these pages from `@page` to `@page "{id:int}"`.</span></span>

<span data-ttu-id="22ec5-139">Eine Anforderung an die Seite mit der Routenvorlage „{id:int}“, die **nicht** den Integer enthält, gibt den HTTP-Fehler „404 – Nicht gefunden“ zurück.</span><span class="sxs-lookup"><span data-stu-id="22ec5-139">A request to the page with the "{id:int}" route template that does **not** include a integer route value returns an HTTP 404 (not found) error.</span></span> <span data-ttu-id="22ec5-140">`http://localhost:5000/Students/Details` gibt beispielsweise den Fehler 404 zurück.</span><span class="sxs-lookup"><span data-stu-id="22ec5-140">For example, `http://localhost:5000/Students/Details` returns a 404 error.</span></span> <span data-ttu-id="22ec5-141">Um die ID optional zu machen, fügen Sie `?` an die Routeneinschränkung an:</span><span class="sxs-lookup"><span data-stu-id="22ec5-141">To make the ID optional, append `?` to the route constraint:</span></span>

 ```cshtml
@page "{id:int?}"
```

<span data-ttu-id="22ec5-142">Führen Sie die App aus, klicken Sie auf einen Details-Link, und überprüfen Sie, ob die ID in der URL in Form von Routendaten übergeben wird (`http://localhost:5000/Students/Details/2`).</span><span class="sxs-lookup"><span data-stu-id="22ec5-142">Run the app, click on a Details link, and verify the URL is passing the ID as route data (`http://localhost:5000/Students/Details/2`).</span></span>

<span data-ttu-id="22ec5-143">Ändern Sie `@page` nicht global in `@page "{id:int}"`. Dadurch werden die Links zu den Seiten „Home“ und „Create“ (Erstellen) getrennt.</span><span class="sxs-lookup"><span data-stu-id="22ec5-143">Don't globally change `@page` to `@page "{id:int}"`, doing so breaks the links to the Home and Create pages.</span></span>

<!-- See https://github.com/aspnet/Scaffolding/issues/590 -->

### <a name="add-related-data"></a><span data-ttu-id="22ec5-144">Hinzufügen relevanter Daten</span><span class="sxs-lookup"><span data-stu-id="22ec5-144">Add related data</span></span>

<span data-ttu-id="22ec5-145">Der für die Indexseite „Students“ erstellte Code umfasst nicht die Eigenschaft `Enrollments`.</span><span class="sxs-lookup"><span data-stu-id="22ec5-145">The scaffolded code for the Students Index page doesn't include the `Enrollments` property.</span></span> <span data-ttu-id="22ec5-146">In diesem Abschnitt werden die Inhalte der `Enrollments`-Auflistung auf der Seite „Details“ angezeigt.</span><span class="sxs-lookup"><span data-stu-id="22ec5-146">In this section, the contents of the `Enrollments` collection is displayed in the Details page.</span></span>

<span data-ttu-id="22ec5-147">Die Methode `OnGetAsync` der Datei *Pages/Students/Details.cshtml.cs* verwendet die Methode `FirstOrDefaultAsync` zum Abrufen einer einzelnen `Student`-Entität.</span><span class="sxs-lookup"><span data-stu-id="22ec5-147">The `OnGetAsync` method of *Pages/Students/Details.cshtml.cs* uses the `FirstOrDefaultAsync` method to retrieve a single `Student` entity.</span></span> <span data-ttu-id="22ec5-148">Fügen Sie folgenden hervorgehobenen Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="22ec5-148">Add the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Details.cshtml.cs?name=snippet_Details&highlight=8-12)]

<span data-ttu-id="22ec5-149">Aufgrund der Methoden [Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) und [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) lädt der Kontext die Navigationseigenschaft `Student.Enrollments` und in jeder Registrierung die Navigationseigenschaft `Enrollment.Course`.</span><span class="sxs-lookup"><span data-stu-id="22ec5-149">The [Include](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.include) and [ThenInclude](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.theninclude#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_ThenInclude__3_Microsoft_EntityFrameworkCore_Query_IIncludableQueryable___0_System_Collections_Generic_IEnumerable___1___System_Linq_Expressions_Expression_System_Func___1___2___) methods cause the context to load the `Student.Enrollments` navigation property, and within each enrollment the `Enrollment.Course` navigation property.</span></span> <span data-ttu-id="22ec5-150">Diese Methoden werden im lesebezogenen Datentutorial ausführlich überprüft.</span><span class="sxs-lookup"><span data-stu-id="22ec5-150">These methods are examined in detail in the reading-related data tutorial.</span></span>

<span data-ttu-id="22ec5-151">Die Methode [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) verbessert die Leistung in Szenarios, wenn die zurückgegebenen Entitäten nicht im aktuellen Kontext aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="22ec5-151">The [AsNoTracking](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.asnotracking#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_AsNoTracking__1_System_Linq_IQueryable___0__) method improves performance in scenarios when the entities returned are not updated in the current context.</span></span> <span data-ttu-id="22ec5-152">`AsNoTracking` wird später in diesem Tutorial behandelt.</span><span class="sxs-lookup"><span data-stu-id="22ec5-152">`AsNoTracking` is discussed later in this tutorial.</span></span>

### <a name="display-related-enrollments-on-the-details-page"></a><span data-ttu-id="22ec5-153">Anzeigen verknüpfter Registrierungen auf der Seite „Details“</span><span class="sxs-lookup"><span data-stu-id="22ec5-153">Display related enrollments on the Details page</span></span>

<span data-ttu-id="22ec5-154">Öffnen Sie die Datei *Pages/Students/Details.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="22ec5-154">Open *Pages/Students/Details.cshtml*.</span></span> <span data-ttu-id="22ec5-155">Fügen Sie folgenden hervorgehobenen Code hinzu, um eine Liste mit Registrierungen anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="22ec5-155">Add the following highlighted code to display a list of enrollments:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Students/Details.cshtml?highlight=32-53)]

<span data-ttu-id="22ec5-156">Wenn der Codeeinzug nach dem Einfügen des Codes falsch ist, drücken Sie Strg+K+D, um diesen zu korrigieren.</span><span class="sxs-lookup"><span data-stu-id="22ec5-156">If code indentation is wrong after the code is pasted, press CTRL-K-D to correct it.</span></span>

<span data-ttu-id="22ec5-157">Der vorangehende Code durchläuft die Entitäten in der Navigationseigenschaft `Enrollments`.</span><span class="sxs-lookup"><span data-stu-id="22ec5-157">The preceding code loops through the entities in the `Enrollments` navigation property.</span></span> <span data-ttu-id="22ec5-158">Für jede Registrierung werden der Kurstitel und die Klasse angezeigt.</span><span class="sxs-lookup"><span data-stu-id="22ec5-158">For each enrollment, it displays the course title and the grade.</span></span> <span data-ttu-id="22ec5-159">Der Kurstitel wird von der Entität „Course“ abgerufen, die in der Navigationseigenschaft `Course` der Entität „Enrollments“ gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="22ec5-159">The course title is retrieved from the Course entity that's stored in the `Course` navigation property of the Enrollments entity.</span></span>

<span data-ttu-id="22ec5-160">Führen Sie die App aus, wählen Sie die Registerkarte **Studenten** aus, und klicken Sie bei einem Studenten auf den Link **Details**.</span><span class="sxs-lookup"><span data-stu-id="22ec5-160">Run the app, select the **Students** tab, and click the **Details** link for a student.</span></span> <span data-ttu-id="22ec5-161">Die Liste der Kurse und Klassen für den ausgewählten Studenten wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="22ec5-161">The list of courses and grades for the selected student is displayed.</span></span>

## <a name="update-the-create-page"></a><span data-ttu-id="22ec5-162">Aktualisieren der Seite „Erstellen“</span><span class="sxs-lookup"><span data-stu-id="22ec5-162">Update the Create page</span></span>

<span data-ttu-id="22ec5-163">Aktualisieren Sie die Methode `OnPostAsync` in der Datei *Pages/Students/Create.cshtml.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="22ec5-163">Update the `OnPostAsync` method in *Pages/Students/Create.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_OnPostAsync)]

<a name="TryUpdateModelAsync"></a>

### <a name="tryupdatemodelasync"></a><span data-ttu-id="22ec5-164">TryUpdateModelAsync</span><span class="sxs-lookup"><span data-stu-id="22ec5-164">TryUpdateModelAsync</span></span>

<span data-ttu-id="22ec5-165">Überprüfen Sie den [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_)-Code:</span><span class="sxs-lookup"><span data-stu-id="22ec5-165">Examine the [TryUpdateModelAsync](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.tryupdatemodelasync#Microsoft_AspNetCore_Mvc_ControllerBase_TryUpdateModelAsync_System_Object_System_Type_System_String_) code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Create.cshtml.cs?name=snippet_TryUpdateModelAsync)]

<span data-ttu-id="22ec5-166">Im vorangehenden Code versucht `TryUpdateModelAsync<Student>`, das Objekt `emptyStudent` mithilfe der bereitgestellten Formularwerte aus der Eigenschaft [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) im [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="22ec5-166">In the preceding code, `TryUpdateModelAsync<Student>` tries to update the `emptyStudent` object using the posted form values from the [PageContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.pagecontext#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_PageContext) property in the [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel).</span></span> <span data-ttu-id="22ec5-167">`TryUpdateModelAsync` aktualisiert nur die aufgeführten Eigenschaften (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).</span><span class="sxs-lookup"><span data-stu-id="22ec5-167">`TryUpdateModelAsync` only updates the properties listed (`s => s.FirstMidName, s => s.LastName, s => s.EnrollmentDate`).</span></span>

<span data-ttu-id="22ec5-168">Im vorgehenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="22ec5-168">In the preceding sample:</span></span>

* <span data-ttu-id="22ec5-169">Ist das zweite Argument (`"student", // Prefix`) das Präfix, das für die Suche nach Werten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="22ec5-169">The second argument (`"student", // Prefix`) is the prefix uses to look up values.</span></span> <span data-ttu-id="22ec5-170">Die Groß-/Kleinschreibung wird hier nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="22ec5-170">It's not case sensitive.</span></span>
* <span data-ttu-id="22ec5-171">Werden die bereitgestellten Formularwerte mithilfe der [Modellbindung](xref:mvc/models/model-binding#how-model-binding-works) in die Typen im `Student`-Modell konvertiert.</span><span class="sxs-lookup"><span data-stu-id="22ec5-171">The posted form values are converted to the types in the `Student` model using [model binding](xref:mvc/models/model-binding#how-model-binding-works).</span></span>

<a id="overpost"></a>

### <a name="overposting"></a><span data-ttu-id="22ec5-172">Overposting</span><span class="sxs-lookup"><span data-stu-id="22ec5-172">Overposting</span></span>

<span data-ttu-id="22ec5-173">Die Verwendung von `TryUpdateModel` zur Aktualisierung von Feldern mit bereitgestellten Werten ist eine bewährte Sicherheitsmethode, da Overposting verhindert wird.</span><span class="sxs-lookup"><span data-stu-id="22ec5-173">Using `TryUpdateModel` to update fields with posted values is a security best practice because it prevents overposting.</span></span> <span data-ttu-id="22ec5-174">Angenommen, die Student-Entität enthält die Eigenschaft `Secret`, die auf dieser Webseite nicht aktualisiert oder hinzugefügt werden sollte:</span><span class="sxs-lookup"><span data-stu-id="22ec5-174">For example, suppose the Student entity includes a `Secret` property that this web page shouldn't update or add:</span></span>

[!code-csharp[](intro/samples/cu21/Models/StudentZsecret.cs?name=snippet_Intro&highlight=7)]

<span data-ttu-id="22ec5-175">Selbst wenn die App auf der Razor Page „Create/Update“ (Erstellen/Aktualisieren) nicht über ein Feld vom Typ `Secret` verfügt, könnte ein Hacker den `Secret`-Wert durch Overposting festlegen.</span><span class="sxs-lookup"><span data-stu-id="22ec5-175">Even if the app doesn't have a `Secret` field on the create/update Razor Page, a hacker could set the `Secret` value by overposting.</span></span> <span data-ttu-id="22ec5-176">Ein Hacker könnte ein Tool wie Fiddler verwenden oder JavaScript-Code schreiben, um einen `Secret`-Formularwert bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="22ec5-176">A hacker could use a tool such as Fiddler, or write some JavaScript, to post a `Secret` form value.</span></span> <span data-ttu-id="22ec5-177">Die Felder, die von der Modellbindung bei der Erstellung einer Student-Instanz verwendet werden, werden nicht durch den ursprünglichen Code begrenzt.</span><span class="sxs-lookup"><span data-stu-id="22ec5-177">The original code doesn't limit the fields that the model binder uses when it creates a Student instance.</span></span>

<span data-ttu-id="22ec5-178">Jeder beliebige, vom Hacker in den `Secret`-Formularfeldern angegebene Wert wird in der Datenbank aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="22ec5-178">Whatever value the hacker specified for the `Secret` form field is updated in the DB.</span></span> <span data-ttu-id="22ec5-179">Die folgende Abbildung zeigt das Fiddler-Tool beim Hinzufügen des Felds `Secret` (mit dem Wert „OverPost“) zu den bereitgestellten Formularwerten.</span><span class="sxs-lookup"><span data-stu-id="22ec5-179">The following image shows the Fiddler tool adding the `Secret` field (with the value "OverPost") to the posted form values.</span></span>

![Fiddler beim Hinzufügen des Felds „Secret“](../ef-mvc/crud/_static/fiddler.png)

<span data-ttu-id="22ec5-181">Der Wert „OverPost“ wurde erfolgreich zur Eigenschaft `Secret` der eingefügten Zeile hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="22ec5-181">The value "OverPost" is successfully added to the `Secret` property of the inserted row.</span></span> <span data-ttu-id="22ec5-182">Der App-Designer hatte nie die Absicht, die Eigenschaft `Secret` auf der Seite „Erstellen“ festzulegen.</span><span class="sxs-lookup"><span data-stu-id="22ec5-182">The app designer never intended the `Secret` property to be set with the Create page.</span></span>

<a name="vm"></a>

### <a name="view-model"></a><span data-ttu-id="22ec5-183">ViewModel-Element</span><span class="sxs-lookup"><span data-stu-id="22ec5-183">View model</span></span>

<span data-ttu-id="22ec5-184">Ein Ansichtsmodell enthält in der Regel eine Teilmenge der im Modell enthaltenen Eigenschaften, die von der App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="22ec5-184">A view model typically contains a subset of the properties included in the model used by the application.</span></span> <span data-ttu-id="22ec5-185">Das Anwendungsmodell wird häufig als Domänenmodell bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="22ec5-185">The application model is often called the domain model.</span></span> <span data-ttu-id="22ec5-186">Das Domänenmodell enthält normalerweise alle Eigenschaften, die die entsprechende Entität in der Datenbank erfordert.</span><span class="sxs-lookup"><span data-stu-id="22ec5-186">The domain model typically contains all the properties required by the corresponding entity in the DB.</span></span> <span data-ttu-id="22ec5-187">Das Ansichtsmodell enthält nur die Eigenschaften, die für die Benutzeroberflächenebene (z.B. die Seite „Erstellen“) erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="22ec5-187">The view model contains only the properties needed for the UI layer (for example, the Create page).</span></span> <span data-ttu-id="22ec5-188">Neben dem Ansichtsmodell verwenden einige Apps ein Bindungsmodell oder Eingabemodell für die Bereitstellung von Daten zwischen der Seitenmodellklasse auf den Razor Pages und dem Browser.</span><span class="sxs-lookup"><span data-stu-id="22ec5-188">In addition to the view model, some apps use a binding model or input model to pass data between the Razor Pages page model class and the browser.</span></span> <span data-ttu-id="22ec5-189">Betrachten Sie das folgende `Student`-Ansichtsmodell:</span><span class="sxs-lookup"><span data-stu-id="22ec5-189">Consider the following `Student` view model:</span></span>

[!code-csharp[](intro/samples/cu21/Models/StudentVM.cs)]

<span data-ttu-id="22ec5-190">Ansichtsmodelle bieten eine alternative Möglichkeit zum Verhindern von Overposting.</span><span class="sxs-lookup"><span data-stu-id="22ec5-190">View models provide an alternative way to prevent overposting.</span></span> <span data-ttu-id="22ec5-191">Das Ansichtsmodell enthält nur die Eigenschaften, die angezeigt oder aktualisiert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="22ec5-191">The view model contains only the properties to view (display) or update.</span></span>

<span data-ttu-id="22ec5-192">Im folgenden Code wird das `StudentVM`-Ansichtsmodell für die Erstellung eines neuen Studenten verwendet:</span><span class="sxs-lookup"><span data-stu-id="22ec5-192">The following code uses the `StudentVM` view model to create a new student:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/CreateVM.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="22ec5-193">Die Methode [SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) legt die Werte dieses Objekts fest, indem sie Werte aus einem anderen [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues)-Objekt liest.</span><span class="sxs-lookup"><span data-stu-id="22ec5-193">The [SetValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues.setvalues#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyValues_SetValues_System_Object_) method sets the values of this object by reading values from another [PropertyValues](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyvalues) object.</span></span> <span data-ttu-id="22ec5-194">`SetValues` verwendet die Übereinstimmung von Eigenschaftsnamen.</span><span class="sxs-lookup"><span data-stu-id="22ec5-194">`SetValues` uses property name matching.</span></span> <span data-ttu-id="22ec5-195">Der Ansichtsmodelltyp muss nicht mit dem Modelltyp verknüpft sein. Er muss lediglich über übereinstimmende Eigenschaften verfügen.</span><span class="sxs-lookup"><span data-stu-id="22ec5-195">The view model type doesn't need to be related to the model type, it just needs to have properties that match.</span></span>

<span data-ttu-id="22ec5-196">Für die Verwendung von `StudentVM` muss [CreateVM.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21/Pages/Students/CreateVM.cshtml) aktualisiert werden, damit `StudentVM` statt `Student` verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="22ec5-196">Using `StudentVM` requires [CreateVM.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21/Pages/Students/CreateVM.cshtml) be updated to use `StudentVM` rather than `Student`.</span></span>

<span data-ttu-id="22ec5-197">Auf Razor Pages stellt die abgeleitete `PageModel`-Klasse das Ansichtsmodell dar.</span><span class="sxs-lookup"><span data-stu-id="22ec5-197">In Razor Pages, the `PageModel` derived class is the view model.</span></span>

## <a name="update-the-edit-page"></a><span data-ttu-id="22ec5-198">Aktualisieren der Seite „Bearbeiten“</span><span class="sxs-lookup"><span data-stu-id="22ec5-198">Update the Edit page</span></span>

<span data-ttu-id="22ec5-199">Aktualisieren Sie das Seitenmodell für die Seite „Bearbeiten“:</span><span class="sxs-lookup"><span data-stu-id="22ec5-199">Update the page model for the Edit page.</span></span> <span data-ttu-id="22ec5-200">Die wichtigsten Änderungen werden hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="22ec5-200">The major changes are highlighted:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Edit.cshtml.cs?name=snippet_OnPostAsync&highlight=20,36)]

<span data-ttu-id="22ec5-201">Die Codeänderungen sind bis auf wenige Ausnahmen mit denen auf der Seite „Erstellen“ vergleichbar:</span><span class="sxs-lookup"><span data-stu-id="22ec5-201">The code changes are similar to the Create page with a few exceptions:</span></span>

* <span data-ttu-id="22ec5-202">`OnPostAsync` weist den optionalen Parameter `id` auf.</span><span class="sxs-lookup"><span data-stu-id="22ec5-202">`OnPostAsync` has an optional `id` parameter.</span></span>
* <span data-ttu-id="22ec5-203">Anstatt einen leeren Studenten zu erstellen, wird der aktuelle Student aus der Datenbank abgerufen.</span><span class="sxs-lookup"><span data-stu-id="22ec5-203">The current student is fetched from the DB, rather than creating an empty student.</span></span>
* <span data-ttu-id="22ec5-204">`FirstOrDefaultAsync` wurde durch [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync) ersetzt.</span><span class="sxs-lookup"><span data-stu-id="22ec5-204">`FirstOrDefaultAsync` has been replaced with [FindAsync](/dotnet/api/microsoft.entityframeworkcore.dbset-1.findasync).</span></span> <span data-ttu-id="22ec5-205">`FindAsync` eignet sich für die Auswahl einer Entität über den Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="22ec5-205">`FindAsync` is a good choice when selecting an entity from the primary key.</span></span> <span data-ttu-id="22ec5-206">Weitere Informationen hierzu finden Sie unter [FindAsync](#FindAsync).</span><span class="sxs-lookup"><span data-stu-id="22ec5-206">See [FindAsync](#FindAsync) for more information.</span></span>

### <a name="test-the-edit-and-create-pages"></a><span data-ttu-id="22ec5-207">Testen der Seiten „Bearbeiten“ und „Erstellen“</span><span class="sxs-lookup"><span data-stu-id="22ec5-207">Test the Edit and Create pages</span></span>

<span data-ttu-id="22ec5-208">Erstellen und bearbeiten Sie einige Student-Entitäten.</span><span class="sxs-lookup"><span data-stu-id="22ec5-208">Create and edit a few student entities.</span></span>

## <a name="entity-states"></a><span data-ttu-id="22ec5-209">Entitätsstatus</span><span class="sxs-lookup"><span data-stu-id="22ec5-209">Entity States</span></span>

<span data-ttu-id="22ec5-210">Im Datenbankkontext wird nachverfolgt, ob Entitäten im Arbeitsspeicher mit den zugehörigen Zeilen in der Datenbank synchron sind.</span><span class="sxs-lookup"><span data-stu-id="22ec5-210">The DB context keeps track of whether entities in memory are in sync with their corresponding rows in the DB.</span></span> <span data-ttu-id="22ec5-211">Die Informationen zur Datenbanksynchronisierung bestimmen, was geschieht, wenn [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="22ec5-211">The DB context sync information determines what happens when [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) is called.</span></span> <span data-ttu-id="22ec5-212">Wenn beispielsweise eine neue Entität an die Methode [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) übergeben wird, wird der Zustand dieser Entität auf [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="22ec5-212">For example, when a new entity is passed to the [AddAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.addasync) method, that entity's state is set to [Added](/dotnet/api/microsoft.entityframeworkcore.entitystate#Microsoft_EntityFrameworkCore_EntityState_Added).</span></span> <span data-ttu-id="22ec5-213">Wenn `SaveChangesAsync` aufgerufen wird, gibt der Datenbankkontext den SQL-Befehl INSERT aus.</span><span class="sxs-lookup"><span data-stu-id="22ec5-213">When `SaveChangesAsync` is called, the DB context issues a SQL INSERT command.</span></span>

<span data-ttu-id="22ec5-214">Eine Entität kann einen der [folgenden Status](/dotnet/api/microsoft.entityframeworkcore.entitystate) aufweisen:</span><span class="sxs-lookup"><span data-stu-id="22ec5-214">An entity may be in one of the [following states](/dotnet/api/microsoft.entityframeworkcore.entitystate):</span></span>

* <span data-ttu-id="22ec5-215">`Added`: Die Entität ist noch nicht in der Datenbank vorhanden.</span><span class="sxs-lookup"><span data-stu-id="22ec5-215">`Added`: The entity doesn't yet exist in the DB.</span></span> <span data-ttu-id="22ec5-216">Die Methode `SaveChanges` gibt eine INSERT-Anweisung aus.</span><span class="sxs-lookup"><span data-stu-id="22ec5-216">The `SaveChanges` method issues an INSERT statement.</span></span>

* <span data-ttu-id="22ec5-217">`Unchanged`: Für diese Entität müssen keine Änderungen gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="22ec5-217">`Unchanged`: No changes need to be saved with this entity.</span></span> <span data-ttu-id="22ec5-218">Eine Entität weist diesen Zustand auf, wenn sie von der Datenbank gelesen wird.</span><span class="sxs-lookup"><span data-stu-id="22ec5-218">An entity has this status when it's read from the DB.</span></span>

* <span data-ttu-id="22ec5-219">`Modified`: Einige oder alle Eigenschaftswerte der Entität wurden geändert.</span><span class="sxs-lookup"><span data-stu-id="22ec5-219">`Modified`: Some or all of the entity's property values have been modified.</span></span> <span data-ttu-id="22ec5-220">Die Methode `SaveChanges` gibt eine UPDATE-Anweisung aus.</span><span class="sxs-lookup"><span data-stu-id="22ec5-220">The `SaveChanges` method issues an UPDATE statement.</span></span>

* <span data-ttu-id="22ec5-221">`Deleted`: Die Entität wurde zum Löschen markiert.</span><span class="sxs-lookup"><span data-stu-id="22ec5-221">`Deleted`: The entity has been marked for deletion.</span></span> <span data-ttu-id="22ec5-222">Die Methode `SaveChanges` gibt eine DELETE-Anweisung aus.</span><span class="sxs-lookup"><span data-stu-id="22ec5-222">The `SaveChanges` method issues a DELETE statement.</span></span>

* <span data-ttu-id="22ec5-223">`Detached`: Die Entität wird nicht vom Datenbankkontext nachverfolgt.</span><span class="sxs-lookup"><span data-stu-id="22ec5-223">`Detached`: The entity isn't being tracked by the DB context.</span></span>

<span data-ttu-id="22ec5-224">Zustandsänderungen werden in einer Desktop-App in der Regel automatisch festgelegt.</span><span class="sxs-lookup"><span data-stu-id="22ec5-224">In a desktop app, state changes are typically set automatically.</span></span> <span data-ttu-id="22ec5-225">Eine Entität wird gelesen, Änderungen werden vorgenommen, und der Entitätszustand wird automatisch in `Modified` geändert.</span><span class="sxs-lookup"><span data-stu-id="22ec5-225">An entity is read, changes are made, and the entity state to automatically be changed to `Modified`.</span></span> <span data-ttu-id="22ec5-226">Durch das Aufrufen von `SaveChanges` wird eine SQL UPDATE-Anweisung generiert, die nur die geänderten Eigenschaften aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="22ec5-226">Calling `SaveChanges` generates a SQL UPDATE statement that updates only the changed properties.</span></span>

<span data-ttu-id="22ec5-227">In einer Web-App wird der `DbContext`verfügbar gemacht, der eine Entität liest und die Daten anzeigt, nachdem eine Seite gerendert wurde.</span><span class="sxs-lookup"><span data-stu-id="22ec5-227">In a web app, the `DbContext` that reads an entity and displays the data is disposed after a page is rendered.</span></span> <span data-ttu-id="22ec5-228">Wenn die Methode `OnPostAsync` einer Seite aufgerufen wird, wird eine neue Webanforderung mit einer neuen Instanz von `DbContext` gestellt.</span><span class="sxs-lookup"><span data-stu-id="22ec5-228">When a page's `OnPostAsync` method is called, a new web request is made and with a new instance of the `DbContext`.</span></span> <span data-ttu-id="22ec5-229">Durch erneutes Lesen der Entität in diesem neuen Kontext wird die Desktopverarbeitung simuliert.</span><span class="sxs-lookup"><span data-stu-id="22ec5-229">Re-reading the entity in that new context simulates desktop processing.</span></span>

## <a name="update-the-delete-page"></a><span data-ttu-id="22ec5-230">Aktualisieren der Seite „Delete“ (Löschen)</span><span class="sxs-lookup"><span data-stu-id="22ec5-230">Update the Delete page</span></span>

<span data-ttu-id="22ec5-231">In diesem Abschnitt wird Code hinzugefügt, um eine benutzerdefinierte Fehlermeldung zu implementieren, wenn der Aufruf von `SaveChanges` fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="22ec5-231">In this section, code is added to implement a custom error message when the call to `SaveChanges` fails.</span></span> <span data-ttu-id="22ec5-232">Fügen Sie eine Zeichenfolge hinzu, die mögliche Fehlermeldungen enthalten soll:</span><span class="sxs-lookup"><span data-stu-id="22ec5-232">Add a string to contain possible error messages:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet1&highlight=12)]

<span data-ttu-id="22ec5-233">Ersetzen Sie die `OnGetAsync`-Methode durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="22ec5-233">Replace the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnGetAsync&highlight=1,9,17-20)]

<span data-ttu-id="22ec5-234">Der vorangehende Code enthält den optionalen Parameter `saveChangesError`.</span><span class="sxs-lookup"><span data-stu-id="22ec5-234">The preceding code contains the optional parameter `saveChangesError`.</span></span> <span data-ttu-id="22ec5-235">`saveChangesError` gibt an, ob die Methode nach einem Fehler beim Löschen des Studentenobjekts aufgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="22ec5-235">`saveChangesError` indicates whether the method was called after a failure to delete the student object.</span></span> <span data-ttu-id="22ec5-236">Der Löschvorgang schlägt möglicherweise aufgrund von vorübergehenden Netzwerkproblemen fehl.</span><span class="sxs-lookup"><span data-stu-id="22ec5-236">The delete operation might fail because of transient network problems.</span></span> <span data-ttu-id="22ec5-237">Vorübergehende Netzwerkfehler treten eher in der Cloud auf.</span><span class="sxs-lookup"><span data-stu-id="22ec5-237">Transient network errors are more likely in the cloud.</span></span> <span data-ttu-id="22ec5-238">`saveChangesError` ist „FALSE“, wenn die Methode `OnGetAsync` auf der Seite „Löschen“ über die Benutzeroberfläche aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="22ec5-238">`saveChangesError`is false when the Delete page `OnGetAsync` is called from the UI.</span></span> <span data-ttu-id="22ec5-239">Wenn `OnGetAsync` von `OnPostAsync` aufgerufen wird (aufgrund des fehlgeschlagenen Löschvorgangs), ist der Parameter `saveChangesError` „TRUE“.</span><span class="sxs-lookup"><span data-stu-id="22ec5-239">When `OnGetAsync` is called by `OnPostAsync` (because the delete operation failed), the `saveChangesError` parameter is true.</span></span>

### <a name="the-delete-pages-onpostasync-method"></a><span data-ttu-id="22ec5-240">Die Methode „OnPostAsync“ auf „Löschen“-Seiten</span><span class="sxs-lookup"><span data-stu-id="22ec5-240">The Delete pages OnPostAsync method</span></span>

<span data-ttu-id="22ec5-241">Ersetzen Sie `OnPostAsync` durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="22ec5-241">Replace the `OnPostAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Delete.cshtml.cs?name=snippet_OnPostAsync)]

<span data-ttu-id="22ec5-242">Der vorangehende Code ruft die ausgewählte Entität ab und anschließend die Methode [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) auf, um den Zustand der Entität auf `Deleted` festzulegen.</span><span class="sxs-lookup"><span data-stu-id="22ec5-242">The preceding code retrieves the selected entity, then calls the [Remove](/dotnet/api/microsoft.entityframeworkcore.dbcontext.remove#Microsoft_EntityFrameworkCore_DbContext_Remove_System_Object_) method to set the entity's status to `Deleted`.</span></span> <span data-ttu-id="22ec5-243">Wenn `SaveChanges` aufgerufen wird, wird der SQL-Befehl DELETE generiert.</span><span class="sxs-lookup"><span data-stu-id="22ec5-243">When `SaveChanges` is called, a SQL DELETE command is generated.</span></span> <span data-ttu-id="22ec5-244">Wenn `Remove` fehlschlägt:</span><span class="sxs-lookup"><span data-stu-id="22ec5-244">If `Remove` fails:</span></span>

* <span data-ttu-id="22ec5-245">Wird die Datenbankausnahme abgefangen.</span><span class="sxs-lookup"><span data-stu-id="22ec5-245">The DB exception is caught.</span></span>
* <span data-ttu-id="22ec5-246">Wird die Methode `OnGetAsync` auf der Seite „Löschen“ mit `saveChangesError=true` aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="22ec5-246">The Delete pages `OnGetAsync` method is called with `saveChangesError=true`.</span></span>

### <a name="update-the-delete-razor-page"></a><span data-ttu-id="22ec5-247">Aktualisieren der Razor Page „Löschen“</span><span class="sxs-lookup"><span data-stu-id="22ec5-247">Update the Delete Razor Page</span></span>

<span data-ttu-id="22ec5-248">Fügen Sie folgende hervorgehobene Fehlermeldung zur Razor Page „Löschen“ hinzu.</span><span class="sxs-lookup"><span data-stu-id="22ec5-248">Add the following highlighted error message to the Delete Razor Page.</span></span>
<!--
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?name=snippet&highlight=11)]
-->
[!code-cshtml[](intro/samples/cu21/Pages/Students/Delete.cshtml?range=1-13&highlight=10)]

<span data-ttu-id="22ec5-249">Testen Sie die Seite „Löschen“.</span><span class="sxs-lookup"><span data-stu-id="22ec5-249">Test Delete.</span></span>

## <a name="common-errors"></a><span data-ttu-id="22ec5-250">Häufige Fehler</span><span class="sxs-lookup"><span data-stu-id="22ec5-250">Common errors</span></span>

<span data-ttu-id="22ec5-251">„Students/Index“ oder andere Links funktionieren nicht:</span><span class="sxs-lookup"><span data-stu-id="22ec5-251">Students/Index or other links don't work:</span></span>

<span data-ttu-id="22ec5-252">Überprüfen Sie, ob die Razor Page die richtige `@page`-Anweisung enthält.</span><span class="sxs-lookup"><span data-stu-id="22ec5-252">Verify the Razor Page contains the correct `@page` directive.</span></span> <span data-ttu-id="22ec5-253">Die Razor-Seite „Students/Index“ sollte beispielsweise **keine** Routenvorlage enthalten:</span><span class="sxs-lookup"><span data-stu-id="22ec5-253">For example, The Students/Index Razor Page should **not** contain a route template:</span></span>

```cshtml
@page "{id:int}"
```

<span data-ttu-id="22ec5-254">Die `@page`-Anweisung muss auf jeder Razor Page vorhanden sein.</span><span class="sxs-lookup"><span data-stu-id="22ec5-254">Each Razor Page must include the `@page` directive.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="22ec5-255">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="22ec5-255">Additional resources</span></span>

* [<span data-ttu-id="22ec5-256">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="22ec5-256">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=K4X1MT2jt6o)

> [!div class="step-by-step"]
> <span data-ttu-id="22ec5-257">[Zurück](xref:data/ef-rp/intro)
> [Weiter](xref:data/ef-rp/sort-filter-page)</span><span class="sxs-lookup"><span data-stu-id="22ec5-257">[Previous](xref:data/ef-rp/intro)
[Next](xref:data/ef-rp/sort-filter-page)</span></span>
