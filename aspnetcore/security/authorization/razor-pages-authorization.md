---
title: Razor Pages Autorisierungs Konventionen in ASP.net Core
author: guardrex
description: Erfahren Sie, wie Sie den Zugriff auf Seiten mit Konventionen steuern, die Benutzer autorisieren und anonymen Benutzern den Zugriff auf Seiten oder Ordner Seiten gestatten.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: e0102ff64921a83f0330acb6f5d9bfd90f64ca7a
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68994035"
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a><span data-ttu-id="60930-103">Razor Pages Autorisierungs Konventionen in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="60930-103">Razor Pages authorization conventions in ASP.NET Core</span></span>

<span data-ttu-id="60930-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="60930-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="60930-105">Eine Möglichkeit, den Zugriff in ihrer Razor Pages-APP zu steuern, besteht darin, beim Start Autorisierungs Konventionen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="60930-105">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="60930-106">Diese Konventionen ermöglichen es Ihnen, Benutzer zu autorisieren und anonymen Benutzern den Zugriff auf einzelne Seiten oder Ordner Seiten zu gestatten.</span><span class="sxs-lookup"><span data-stu-id="60930-106">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="60930-107">In den in diesem Thema beschriebenen Konventionen werden automatisch [Autorisierungs Filter](xref:mvc/controllers/filters#authorization-filters) angewendet, um den Zugriff zu steuern.</span><span class="sxs-lookup"><span data-stu-id="60930-107">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="60930-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="60930-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="60930-109">Die Beispiel-App verwendet die [Cookie-Authentifizierung ohne ASP.net Core Identität](xref:security/authentication/cookie).</span><span class="sxs-lookup"><span data-stu-id="60930-109">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="60930-110">Die in diesem Thema gezeigten Konzepte und Beispiele gelten für apps, die ASP.net Core Identität verwenden, gleichermaßen.</span><span class="sxs-lookup"><span data-stu-id="60930-110">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="60930-111">Um ASP.net Core Identität zu verwenden, befolgen Sie die <xref:security/authentication/identity>Anweisungen unter.</span><span class="sxs-lookup"><span data-stu-id="60930-111">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="60930-112">Autorisierung für den Zugriff auf eine Seite erforderlich</span><span class="sxs-lookup"><span data-stu-id="60930-112">Require authorization to access a page</span></span>

<span data-ttu-id="60930-113">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> die Konvention <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> über, um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> der Seite auf dem angegebenen Pfad ein hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="60930-113">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="60930-114">Der angegebene Pfad ist der Ansichts-Engine-Pfad. Dies ist der Razor Pages relative relative Pfad ohne Erweiterung und enthält nur Schrägstriche.</span><span class="sxs-lookup"><span data-stu-id="60930-114">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="60930-115">Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)Richtlinie angeben möchten:</span><span class="sxs-lookup"><span data-stu-id="60930-115">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="60930-116">Ein <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> kann auf eine Seiten Modell Klasse mit dem `[Authorize]` Filter-Attribut angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="60930-116">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="60930-117">Weitere Informationen finden Sie unter [Autorisieren eines Filter Attributs](xref:razor-pages/filter#authorize-filter-attribute).</span><span class="sxs-lookup"><span data-stu-id="60930-117">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="60930-118">Autorisierung für den Zugriff auf einen Ordner von Seiten erforderlich</span><span class="sxs-lookup"><span data-stu-id="60930-118">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="60930-119">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> die Konvention <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> über, um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> auf allen Seiten in einem Ordner unter dem angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="60930-119">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="60930-120">Der angegebene Pfad ist der Pfad der Ansichts-Engine, der der relative Pfad des Razor Pages Stamms ist.</span><span class="sxs-lookup"><span data-stu-id="60930-120">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="60930-121">Verwenden Sie zum Angeben einer [Autorisierungs Richtlinie](xref:security/authorization/policies)eine [autorizefolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)-Überladung:</span><span class="sxs-lookup"><span data-stu-id="60930-121">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="60930-122">Autorisierung für den Zugriff auf eine Bereichs Seite erforderlich</span><span class="sxs-lookup"><span data-stu-id="60930-122">Require authorization to access an area page</span></span>

<span data-ttu-id="60930-123">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> die Konvention <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> über, um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> der Bereichs Seite einen unter dem angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="60930-123">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="60930-124">Der Seiten Name ist der Pfad der Datei ohne Erweiterung relativ zum Seiten Stammverzeichnis für den angegebenen Bereich.</span><span class="sxs-lookup"><span data-stu-id="60930-124">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="60930-125">Beispielsweise ist der Seiten Name für die Datei *Bereiche/Identity/Pages/manage/accounts. cshtml* */Manage/Accounts*.</span><span class="sxs-lookup"><span data-stu-id="60930-125">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="60930-126">Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)Richtlinie angeben möchten:</span><span class="sxs-lookup"><span data-stu-id="60930-126">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="60930-127">Autorisierung für den Zugriff auf einen Ordner von Bereichen erforderlich</span><span class="sxs-lookup"><span data-stu-id="60930-127">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="60930-128">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> die Konvention <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> über, um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> einem alle Bereiche in einem Ordner unter dem angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="60930-128">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="60930-129">Der Ordner Pfad ist der Pfad des Ordners relativ zum Seiten Stammverzeichnis für den angegebenen Bereich.</span><span class="sxs-lookup"><span data-stu-id="60930-129">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="60930-130">Beispielsweise wird der Ordner Pfad für die Dateien unter *Bereiche/Identität/Seiten/verwalten/* ist */Manage*.</span><span class="sxs-lookup"><span data-stu-id="60930-130">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="60930-131">Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)Richtlinie angeben möchten:</span><span class="sxs-lookup"><span data-stu-id="60930-131">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="60930-132">Anonymen Zugriff auf eine Seite zulassen</span><span class="sxs-lookup"><span data-stu-id="60930-132">Allow anonymous access to a page</span></span>

<span data-ttu-id="60930-133">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> die Konvention <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> über, um <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> eine einer Seite im angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="60930-133">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="60930-134">Der angegebene Pfad ist der Ansichts-Engine-Pfad. Dies ist der Razor Pages relative relative Pfad ohne Erweiterung und enthält nur Schrägstriche.</span><span class="sxs-lookup"><span data-stu-id="60930-134">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="60930-135">Anonymen Zugriff auf einen Ordner von Seiten zulassen</span><span class="sxs-lookup"><span data-stu-id="60930-135">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="60930-136">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> die Konvention <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> über, um <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> auf allen Seiten in einem Ordner unter dem angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="60930-136">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="60930-137">Der angegebene Pfad ist der Pfad der Ansichts-Engine, der der relative Pfad des Razor Pages Stamms ist.</span><span class="sxs-lookup"><span data-stu-id="60930-137">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="60930-138">Hinweis zum Kombinieren von autorisiertem und anonymem Zugriff</span><span class="sxs-lookup"><span data-stu-id="60930-138">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="60930-139">Es ist zulässig, einen Ordner mit Seiten anzugeben, die eine Autorisierung erfordern, und als anzugeben, dass eine Seite in diesem Ordner anonymen Zugriff zulässt:</span><span class="sxs-lookup"><span data-stu-id="60930-139">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="60930-140">Das Gegenteil ist jedoch nicht gültig.</span><span class="sxs-lookup"><span data-stu-id="60930-140">The reverse, however, isn't valid.</span></span> <span data-ttu-id="60930-141">Sie können keinen Ordner von Seiten für den anonymen Zugriff deklarieren und dann eine Seite in diesem Ordner angeben, für die eine Autorisierung erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="60930-141">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="60930-142">Das Verlangen der Autorisierung auf der privaten Seite schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="60930-142">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="60930-143">Wenn sowohl <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> als auch <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> auf die Seite angewendet werden, hat <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> Vorrang und steuert den Zugriff.</span><span class="sxs-lookup"><span data-stu-id="60930-143">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="60930-144">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="60930-144">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="60930-145">Eine Möglichkeit, den Zugriff in ihrer Razor Pages-APP zu steuern, besteht darin, beim Start Autorisierungs Konventionen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="60930-145">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="60930-146">Diese Konventionen ermöglichen es Ihnen, Benutzer zu autorisieren und anonymen Benutzern den Zugriff auf einzelne Seiten oder Ordner Seiten zu gestatten.</span><span class="sxs-lookup"><span data-stu-id="60930-146">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="60930-147">In den in diesem Thema beschriebenen Konventionen werden automatisch [Autorisierungs Filter](xref:mvc/controllers/filters#authorization-filters) angewendet, um den Zugriff zu steuern.</span><span class="sxs-lookup"><span data-stu-id="60930-147">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="60930-148">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="60930-148">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="60930-149">Die Beispiel-App verwendet die [Cookie-Authentifizierung ohne ASP.net Core Identität](xref:security/authentication/cookie).</span><span class="sxs-lookup"><span data-stu-id="60930-149">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="60930-150">Die in diesem Thema gezeigten Konzepte und Beispiele gelten für apps, die ASP.net Core Identität verwenden, gleichermaßen.</span><span class="sxs-lookup"><span data-stu-id="60930-150">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="60930-151">Um ASP.net Core Identität zu verwenden, befolgen Sie die <xref:security/authentication/identity>Anweisungen unter.</span><span class="sxs-lookup"><span data-stu-id="60930-151">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="60930-152">Autorisierung für den Zugriff auf eine Seite erforderlich</span><span class="sxs-lookup"><span data-stu-id="60930-152">Require authorization to access a page</span></span>

<span data-ttu-id="60930-153">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> die Konvention <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> über, um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> der Seite auf dem angegebenen Pfad ein hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="60930-153">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="60930-154">Der angegebene Pfad ist der Ansichts-Engine-Pfad. Dies ist der Razor Pages relative relative Pfad ohne Erweiterung und enthält nur Schrägstriche.</span><span class="sxs-lookup"><span data-stu-id="60930-154">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="60930-155">Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)Richtlinie angeben möchten:</span><span class="sxs-lookup"><span data-stu-id="60930-155">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="60930-156">Ein <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> kann auf eine Seiten Modell Klasse mit dem `[Authorize]` Filter-Attribut angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="60930-156">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="60930-157">Weitere Informationen finden Sie unter [Autorisieren eines Filter Attributs](xref:razor-pages/filter#authorize-filter-attribute).</span><span class="sxs-lookup"><span data-stu-id="60930-157">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="60930-158">Autorisierung für den Zugriff auf einen Ordner von Seiten erforderlich</span><span class="sxs-lookup"><span data-stu-id="60930-158">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="60930-159">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> die Konvention <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> über, um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> auf allen Seiten in einem Ordner unter dem angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="60930-159">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="60930-160">Der angegebene Pfad ist der Pfad der Ansichts-Engine, der der relative Pfad des Razor Pages Stamms ist.</span><span class="sxs-lookup"><span data-stu-id="60930-160">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="60930-161">Verwenden Sie zum Angeben einer [Autorisierungs Richtlinie](xref:security/authorization/policies)eine [autorizefolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)-Überladung:</span><span class="sxs-lookup"><span data-stu-id="60930-161">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="60930-162">Autorisierung für den Zugriff auf eine Bereichs Seite erforderlich</span><span class="sxs-lookup"><span data-stu-id="60930-162">Require authorization to access an area page</span></span>

<span data-ttu-id="60930-163">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> die Konvention <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> über, um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> der Bereichs Seite einen unter dem angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="60930-163">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="60930-164">Der Seiten Name ist der Pfad der Datei ohne Erweiterung relativ zum Seiten Stammverzeichnis für den angegebenen Bereich.</span><span class="sxs-lookup"><span data-stu-id="60930-164">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="60930-165">Beispielsweise ist der Seiten Name für die Datei *Bereiche/Identity/Pages/manage/accounts. cshtml* */Manage/Accounts*.</span><span class="sxs-lookup"><span data-stu-id="60930-165">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="60930-166">Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)Richtlinie angeben möchten:</span><span class="sxs-lookup"><span data-stu-id="60930-166">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="60930-167">Autorisierung für den Zugriff auf einen Ordner von Bereichen erforderlich</span><span class="sxs-lookup"><span data-stu-id="60930-167">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="60930-168">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> die Konvention <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> über, um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> einem alle Bereiche in einem Ordner unter dem angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="60930-168">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="60930-169">Der Ordner Pfad ist der Pfad des Ordners relativ zum Seiten Stammverzeichnis für den angegebenen Bereich.</span><span class="sxs-lookup"><span data-stu-id="60930-169">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="60930-170">Beispielsweise wird der Ordner Pfad für die Dateien unter *Bereiche/Identität/Seiten/verwalten/* ist */Manage*.</span><span class="sxs-lookup"><span data-stu-id="60930-170">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="60930-171">Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)Richtlinie angeben möchten:</span><span class="sxs-lookup"><span data-stu-id="60930-171">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="60930-172">Anonymen Zugriff auf eine Seite zulassen</span><span class="sxs-lookup"><span data-stu-id="60930-172">Allow anonymous access to a page</span></span>

<span data-ttu-id="60930-173">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> die Konvention <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> über, um <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> eine einer Seite im angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="60930-173">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="60930-174">Der angegebene Pfad ist der Ansichts-Engine-Pfad. Dies ist der Razor Pages relative relative Pfad ohne Erweiterung und enthält nur Schrägstriche.</span><span class="sxs-lookup"><span data-stu-id="60930-174">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="60930-175">Anonymen Zugriff auf einen Ordner von Seiten zulassen</span><span class="sxs-lookup"><span data-stu-id="60930-175">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="60930-176">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> die Konvention <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> über, um <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> auf allen Seiten in einem Ordner unter dem angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="60930-176">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="60930-177">Der angegebene Pfad ist der Pfad der Ansichts-Engine, der der relative Pfad des Razor Pages Stamms ist.</span><span class="sxs-lookup"><span data-stu-id="60930-177">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="60930-178">Hinweis zum Kombinieren von autorisiertem und anonymem Zugriff</span><span class="sxs-lookup"><span data-stu-id="60930-178">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="60930-179">Es ist zulässig, einen Ordner mit Seiten anzugeben, die eine Autorisierung erfordern, und als anzugeben, dass eine Seite in diesem Ordner anonymen Zugriff zulässt:</span><span class="sxs-lookup"><span data-stu-id="60930-179">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="60930-180">Das Gegenteil ist jedoch nicht gültig.</span><span class="sxs-lookup"><span data-stu-id="60930-180">The reverse, however, isn't valid.</span></span> <span data-ttu-id="60930-181">Sie können keinen Ordner von Seiten für den anonymen Zugriff deklarieren und dann eine Seite in diesem Ordner angeben, für die eine Autorisierung erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="60930-181">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="60930-182">Das Verlangen der Autorisierung auf der privaten Seite schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="60930-182">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="60930-183">Wenn sowohl <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> als auch <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> auf die Seite angewendet werden, hat <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> Vorrang und steuert den Zugriff.</span><span class="sxs-lookup"><span data-stu-id="60930-183">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="60930-184">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="60930-184">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end
