---
title: Autorisierungskonventionen für Razor Pages in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie Sie Zugriff auf Seiten mit den Konventionen steuern, die Autorisierung von Benutzern und Fall jeder anonyme Benutzer den Zugriff auf Seiten oder Ordner von Seiten.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/03/2019
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: ff061f96f30cd893b903403de760a172c924cf06
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64895417"
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a><span data-ttu-id="4d043-103">Autorisierungskonventionen für Razor Pages in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4d043-103">Razor Pages authorization conventions in ASP.NET Core</span></span>

<span data-ttu-id="4d043-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="4d043-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="4d043-105">Eine Möglichkeit zum Steuern des Zugriffs in Ihrer app für Razor Pages ist autorisierungskonventionen beim Start verwendet.</span><span class="sxs-lookup"><span data-stu-id="4d043-105">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="4d043-106">Diese Konventionen können Sie zum Autorisieren von Benutzern und Fall jeder anonyme Benutzer den Zugriff auf einzelne Seiten oder Ordner von Seiten.</span><span class="sxs-lookup"><span data-stu-id="4d043-106">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="4d043-107">Das Übernehmen von Konventionen, die automatisch in diesem Thema beschriebenen [Autorisierungsfilter](xref:mvc/controllers/filters#authorization-filters) zum Steuern des Zugriffs.</span><span class="sxs-lookup"><span data-stu-id="4d043-107">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="4d043-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4d043-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="4d043-109">Die beispielanwendung verwendet [Cookieauthentifizierung ohne ASP.NET Core Identity](xref:security/authentication/cookie).</span><span class="sxs-lookup"><span data-stu-id="4d043-109">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="4d043-110">Die Konzepte und die Beispiele in diesem Thema gelten auch für apps, die ASP.NET Core Identity verwenden.</span><span class="sxs-lookup"><span data-stu-id="4d043-110">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="4d043-111">Um ASP.NET Core Identity zu verwenden, befolgen Sie die Anleitung im <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="4d043-111">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="4d043-112">Eine Autorisierung auf eine Seite zuzugreifen</span><span class="sxs-lookup"><span data-stu-id="4d043-112">Require authorization to access a page</span></span>

<span data-ttu-id="4d043-113">Verwenden der <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> zur Seite mit den angegebenen Pfad:</span><span class="sxs-lookup"><span data-stu-id="4d043-113">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="4d043-114">Der angegebene Pfad ist der Ansichts-Engine-Pfad, der Razor-Seiten Stamm relative Pfad ohne eine Erweiterung und enthält, die nur Schrägstriche handelt.</span><span class="sxs-lookup"><span data-stu-id="4d043-114">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="4d043-115">Angeben einer [Autorisierungsrichtlinie](xref:security/authorization/policies), verwenden Sie eine [AuthorizePage Überladung](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span><span class="sxs-lookup"><span data-stu-id="4d043-115">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="4d043-116">Ein <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> kann angewendet werden, um eine Seite-Model-Klasse mit dem `[Authorize]` Standardsicherheitsfilter-Attribut.</span><span class="sxs-lookup"><span data-stu-id="4d043-116">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="4d043-117">Weitere Informationen finden Sie unter [Authorize-Filter-Attribut](xref:razor-pages/filter#authorize-filter-attribute).</span><span class="sxs-lookup"><span data-stu-id="4d043-117">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="4d043-118">Eine Autorisierung auf einen Ordner der Seiten zuzugreifen</span><span class="sxs-lookup"><span data-stu-id="4d043-118">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="4d043-119">Verwenden der <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> für alle Seiten in einem Ordner unter dem angegebenen Pfad:</span><span class="sxs-lookup"><span data-stu-id="4d043-119">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="4d043-120">Der angegebene Pfad ist der Ansichts-Engine-Pfad, der relative Pfad des Razor-Seiten-Stamm handelt.</span><span class="sxs-lookup"><span data-stu-id="4d043-120">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="4d043-121">Angeben einer [Autorisierungsrichtlinie](xref:security/authorization/policies), verwenden Sie eine [AuthorizeFolder Überladung](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span><span class="sxs-lookup"><span data-stu-id="4d043-121">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="4d043-122">Autorisierung zum Zugriff auf die Bereichsseite ein erforderlich</span><span class="sxs-lookup"><span data-stu-id="4d043-122">Require authorization to access an area page</span></span>

<span data-ttu-id="4d043-123">Verwenden der <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> auf der Seite "Bereich", unter dem angegebenen Pfad:</span><span class="sxs-lookup"><span data-stu-id="4d043-123">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="4d043-124">Der Seitenname ist der Pfad der Datei ohne Erweiterung relativ zum Stammverzeichnis Seiten für den angegebenen Bereich.</span><span class="sxs-lookup"><span data-stu-id="4d043-124">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="4d043-125">Z. B. der Seitenname für die Datei *Areas/Identity/Pages/Manage/Accounts.cshtml* ist */Manage/Konten*.</span><span class="sxs-lookup"><span data-stu-id="4d043-125">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="4d043-126">Angeben einer [Autorisierungsrichtlinie](xref:security/authorization/policies), verwenden Sie eine [AuthorizeAreaPage Überladung](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span><span class="sxs-lookup"><span data-stu-id="4d043-126">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="4d043-127">Eine Autorisierung auf einen Ordner mit Bereichen</span><span class="sxs-lookup"><span data-stu-id="4d043-127">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="4d043-128">Verwenden der <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> auf alle Bereiche in einem Ordner unter dem angegebenen Pfad:</span><span class="sxs-lookup"><span data-stu-id="4d043-128">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="4d043-129">Der Ordnerpfad ist der Pfad des Ordners, relativ zum Stammverzeichnis Seiten für den angegebenen Bereich.</span><span class="sxs-lookup"><span data-stu-id="4d043-129">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="4d043-130">Beispielsweise ist der Ordnerpfad für die Dateien unter *Bereiche/Identity/Pages/verwalten/* ist */Verwalten von*.</span><span class="sxs-lookup"><span data-stu-id="4d043-130">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="4d043-131">Angeben einer [Autorisierungsrichtlinie](xref:security/authorization/policies), verwenden Sie eine [AuthorizeAreaFolder Überladung](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span><span class="sxs-lookup"><span data-stu-id="4d043-131">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="4d043-132">Anonymen Zugriff auf eine Seite zulassen</span><span class="sxs-lookup"><span data-stu-id="4d043-132">Allow anonymous access to a page</span></span>

<span data-ttu-id="4d043-133">Verwenden der <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> zu einer Seite unter dem angegebenen Pfad:</span><span class="sxs-lookup"><span data-stu-id="4d043-133">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="4d043-134">Der angegebene Pfad ist der Ansichts-Engine-Pfad, der Razor-Seiten Stamm relative Pfad ohne eine Erweiterung und enthält, die nur Schrägstriche handelt.</span><span class="sxs-lookup"><span data-stu-id="4d043-134">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="4d043-135">Zulassen des anonymen Zugriffs in einem Ordner von Seiten</span><span class="sxs-lookup"><span data-stu-id="4d043-135">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="4d043-136">Verwenden der <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> für alle Seiten in einem Ordner unter dem angegebenen Pfad:</span><span class="sxs-lookup"><span data-stu-id="4d043-136">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="4d043-137">Der angegebene Pfad ist der Ansichts-Engine-Pfad, der relative Pfad des Razor-Seiten-Stamm handelt.</span><span class="sxs-lookup"><span data-stu-id="4d043-137">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="4d043-138">Hinweis zur Kombination von autorisiert und anonymer Zugriff</span><span class="sxs-lookup"><span data-stu-id="4d043-138">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="4d043-139">Es ist zulässig, die angeben, dass ein Ordner der Seiten, die Autorisierung erforderlich ist und als anzugeben, dass eine Seite in diesem Ordner den anonymen Zugriff zulässt:</span><span class="sxs-lookup"><span data-stu-id="4d043-139">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="4d043-140">Es ist allerdings das Gegenteil ist ungültig.</span><span class="sxs-lookup"><span data-stu-id="4d043-140">The reverse, however, isn't valid.</span></span> <span data-ttu-id="4d043-141">Sie können nicht deklariert einen Ordner der Seiten, die für den anonymen Zugriff, und geben Sie dann auf eine Seite in diesem Ordner, die Autorisierung erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="4d043-141">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="4d043-142">Erfordert Autorisierung auf der Seite "Privat" schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="4d043-142">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="4d043-143">Wenn sowohl die <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> und <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> gelten auf der Seite die <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> hat Vorrang vor und steuert den Zugriff.</span><span class="sxs-lookup"><span data-stu-id="4d043-143">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4d043-144">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="4d043-144">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>
