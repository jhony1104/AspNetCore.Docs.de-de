---
title: Razor Pages Autorisierungs Konventionen in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie den Zugriff auf Seiten mit Konventionen steuern, die Benutzer autorisieren und anonymen Benutzern den Zugriff auf Seiten oder Ordner Seiten gestatten.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: 00fc487c6ac802f213bcf83994ecc2b1a1468589
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653113"
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a><span data-ttu-id="99a89-103">Razor Pages Autorisierungs Konventionen in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="99a89-103">Razor Pages authorization conventions in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="99a89-104">Eine Möglichkeit, den Zugriff in ihrer Razor Pages-APP zu steuern, besteht darin, beim Start Autorisierungs Konventionen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="99a89-104">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="99a89-105">Diese Konventionen ermöglichen es Ihnen, Benutzer zu autorisieren und anonymen Benutzern den Zugriff auf einzelne Seiten oder Ordner Seiten zu gestatten.</span><span class="sxs-lookup"><span data-stu-id="99a89-105">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="99a89-106">In den in diesem Thema beschriebenen Konventionen werden automatisch [Autorisierungs Filter](xref:mvc/controllers/filters#authorization-filters) angewendet, um den Zugriff zu steuern.</span><span class="sxs-lookup"><span data-stu-id="99a89-106">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="99a89-107">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="99a89-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="99a89-108">Die Beispiel-App verwendet die [Cookie-Authentifizierung ohne ASP.net Core Identität](xref:security/authentication/cookie).</span><span class="sxs-lookup"><span data-stu-id="99a89-108">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="99a89-109">Die in diesem Thema gezeigten Konzepte und Beispiele gelten für apps, die ASP.net Core Identität verwenden, gleichermaßen.</span><span class="sxs-lookup"><span data-stu-id="99a89-109">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="99a89-110">Um ASP.net Core Identität zu verwenden, befolgen Sie die Anweisungen in <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="99a89-110">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="99a89-111">Autorisierung für den Zugriff auf eine Seite erforderlich</span><span class="sxs-lookup"><span data-stu-id="99a89-111">Require authorization to access a page</span></span>

<span data-ttu-id="99a89-112">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>, um der Seite eine <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> unter dem angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="99a89-112">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="99a89-113">Der angegebene Pfad ist der Ansichts-Engine-Pfad. Dies ist der Razor Pages relative relative Pfad ohne Erweiterung und enthält nur Schrägstriche.</span><span class="sxs-lookup"><span data-stu-id="99a89-113">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="99a89-114">Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)Richtlinie angeben möchten:</span><span class="sxs-lookup"><span data-stu-id="99a89-114">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="99a89-115">Eine <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> kann mit dem `[Authorize]` Filter-Attribut auf eine Seiten Modell Klasse angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="99a89-115">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="99a89-116">Weitere Informationen finden Sie unter [Autorisieren eines Filter Attributs](xref:razor-pages/filter#authorize-filter-attribute).</span><span class="sxs-lookup"><span data-stu-id="99a89-116">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="99a89-117">Autorisierung für den Zugriff auf einen Ordner von Seiten erforderlich</span><span class="sxs-lookup"><span data-stu-id="99a89-117">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="99a89-118">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>, um allen Seiten in einem Ordner unter dem angegebenen Pfad eine <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="99a89-118">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="99a89-119">Der angegebene Pfad ist der Pfad der Ansichts-Engine, der der relative Pfad des Razor Pages Stamms ist.</span><span class="sxs-lookup"><span data-stu-id="99a89-119">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="99a89-120">Verwenden Sie zum Angeben einer [Autorisierungs Richtlinie](xref:security/authorization/policies)eine [autorizefolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)-Überladung:</span><span class="sxs-lookup"><span data-stu-id="99a89-120">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="99a89-121">Autorisierung für den Zugriff auf eine Bereichs Seite erforderlich</span><span class="sxs-lookup"><span data-stu-id="99a89-121">Require authorization to access an area page</span></span>

<span data-ttu-id="99a89-122">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>, um der Bereichs Seite unter dem angegebenen Pfad eine <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="99a89-122">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="99a89-123">Der Seiten Name ist der Pfad der Datei ohne Erweiterung relativ zum Seiten Stammverzeichnis für den angegebenen Bereich.</span><span class="sxs-lookup"><span data-stu-id="99a89-123">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="99a89-124">Beispielsweise ist der Seiten Name für die Datei *Bereiche/Identity/Pages/manage/accounts. cshtml* */Manage/Accounts*.</span><span class="sxs-lookup"><span data-stu-id="99a89-124">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="99a89-125">Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)Richtlinie angeben möchten:</span><span class="sxs-lookup"><span data-stu-id="99a89-125">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="99a89-126">Autorisierung für den Zugriff auf einen Ordner von Bereichen erforderlich</span><span class="sxs-lookup"><span data-stu-id="99a89-126">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="99a89-127">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>, um allen Bereichen in einem Ordner unter dem angegebenen Pfad eine <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="99a89-127">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="99a89-128">Der Ordner Pfad ist der Pfad des Ordners relativ zum Seiten Stammverzeichnis für den angegebenen Bereich.</span><span class="sxs-lookup"><span data-stu-id="99a89-128">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="99a89-129">Beispielsweise wird der Ordner Pfad für die Dateien unter *Bereiche/Identität/Seiten/verwalten/* ist */Manage*.</span><span class="sxs-lookup"><span data-stu-id="99a89-129">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="99a89-130">Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)Richtlinie angeben möchten:</span><span class="sxs-lookup"><span data-stu-id="99a89-130">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="99a89-131">Anonymen Zugriff auf eine Seite zulassen</span><span class="sxs-lookup"><span data-stu-id="99a89-131">Allow anonymous access to a page</span></span>

<span data-ttu-id="99a89-132">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>, um eine <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> zu einer Seite unter dem angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="99a89-132">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="99a89-133">Der angegebene Pfad ist der Ansichts-Engine-Pfad. Dies ist der Razor Pages relative relative Pfad ohne Erweiterung und enthält nur Schrägstriche.</span><span class="sxs-lookup"><span data-stu-id="99a89-133">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="99a89-134">Anonymen Zugriff auf einen Ordner von Seiten zulassen</span><span class="sxs-lookup"><span data-stu-id="99a89-134">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="99a89-135">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>, um allen Seiten in einem Ordner unter dem angegebenen Pfad eine <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="99a89-135">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="99a89-136">Der angegebene Pfad ist der Pfad der Ansichts-Engine, der der relative Pfad des Razor Pages Stamms ist.</span><span class="sxs-lookup"><span data-stu-id="99a89-136">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="99a89-137">Hinweis zum Kombinieren von autorisiertem und anonymem Zugriff</span><span class="sxs-lookup"><span data-stu-id="99a89-137">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="99a89-138">Es ist zulässig, anzugeben, dass für einen Ordner von Seiten eine Autorisierung erforderlich ist, und dann anzugeben, dass eine Seite in diesem Ordner anonymen Zugriff zulässt:</span><span class="sxs-lookup"><span data-stu-id="99a89-138">It's valid to specify that a folder of pages requires authorization and then specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="99a89-139">Das Gegenteil ist jedoch nicht gültig.</span><span class="sxs-lookup"><span data-stu-id="99a89-139">The reverse, however, isn't valid.</span></span> <span data-ttu-id="99a89-140">Sie können keinen Ordner von Seiten für den anonymen Zugriff deklarieren und dann eine Seite in diesem Ordner angeben, für die eine Autorisierung erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="99a89-140">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="99a89-141">Das Verlangen der Autorisierung auf der privaten Seite schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="99a89-141">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="99a89-142">Wenn die <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> und <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> auf die Seite angewendet werden, hat der <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> Vorrang und steuert den Zugriff.</span><span class="sxs-lookup"><span data-stu-id="99a89-142">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="99a89-143">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="99a89-143">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="99a89-144">Eine Möglichkeit, den Zugriff in ihrer Razor Pages-APP zu steuern, besteht darin, beim Start Autorisierungs Konventionen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="99a89-144">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="99a89-145">Diese Konventionen ermöglichen es Ihnen, Benutzer zu autorisieren und anonymen Benutzern den Zugriff auf einzelne Seiten oder Ordner Seiten zu gestatten.</span><span class="sxs-lookup"><span data-stu-id="99a89-145">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="99a89-146">In den in diesem Thema beschriebenen Konventionen werden automatisch [Autorisierungs Filter](xref:mvc/controllers/filters#authorization-filters) angewendet, um den Zugriff zu steuern.</span><span class="sxs-lookup"><span data-stu-id="99a89-146">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="99a89-147">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="99a89-147">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="99a89-148">Die Beispiel-App verwendet die [Cookie-Authentifizierung ohne ASP.net Core Identität](xref:security/authentication/cookie).</span><span class="sxs-lookup"><span data-stu-id="99a89-148">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="99a89-149">Die in diesem Thema gezeigten Konzepte und Beispiele gelten für apps, die ASP.net Core Identität verwenden, gleichermaßen.</span><span class="sxs-lookup"><span data-stu-id="99a89-149">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="99a89-150">Um ASP.net Core Identität zu verwenden, befolgen Sie die Anweisungen in <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="99a89-150">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="99a89-151">Autorisierung für den Zugriff auf eine Seite erforderlich</span><span class="sxs-lookup"><span data-stu-id="99a89-151">Require authorization to access a page</span></span>

<span data-ttu-id="99a89-152">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>, um der Seite eine <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> unter dem angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="99a89-152">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="99a89-153">Der angegebene Pfad ist der Ansichts-Engine-Pfad. Dies ist der Razor Pages relative relative Pfad ohne Erweiterung und enthält nur Schrägstriche.</span><span class="sxs-lookup"><span data-stu-id="99a89-153">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="99a89-154">Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)Richtlinie angeben möchten:</span><span class="sxs-lookup"><span data-stu-id="99a89-154">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="99a89-155">Eine <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> kann mit dem `[Authorize]` Filter-Attribut auf eine Seiten Modell Klasse angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="99a89-155">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="99a89-156">Weitere Informationen finden Sie unter [Autorisieren eines Filter Attributs](xref:razor-pages/filter#authorize-filter-attribute).</span><span class="sxs-lookup"><span data-stu-id="99a89-156">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="99a89-157">Autorisierung für den Zugriff auf einen Ordner von Seiten erforderlich</span><span class="sxs-lookup"><span data-stu-id="99a89-157">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="99a89-158">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>, um allen Seiten in einem Ordner unter dem angegebenen Pfad eine <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="99a89-158">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="99a89-159">Der angegebene Pfad ist der Pfad der Ansichts-Engine, der der relative Pfad des Razor Pages Stamms ist.</span><span class="sxs-lookup"><span data-stu-id="99a89-159">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="99a89-160">Verwenden Sie zum Angeben einer [Autorisierungs Richtlinie](xref:security/authorization/policies)eine [autorizefolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)-Überladung:</span><span class="sxs-lookup"><span data-stu-id="99a89-160">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="99a89-161">Autorisierung für den Zugriff auf eine Bereichs Seite erforderlich</span><span class="sxs-lookup"><span data-stu-id="99a89-161">Require authorization to access an area page</span></span>

<span data-ttu-id="99a89-162">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>, um der Bereichs Seite unter dem angegebenen Pfad eine <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="99a89-162">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="99a89-163">Der Seiten Name ist der Pfad der Datei ohne Erweiterung relativ zum Seiten Stammverzeichnis für den angegebenen Bereich.</span><span class="sxs-lookup"><span data-stu-id="99a89-163">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="99a89-164">Beispielsweise ist der Seiten Name für die Datei *Bereiche/Identity/Pages/manage/accounts. cshtml* */Manage/Accounts*.</span><span class="sxs-lookup"><span data-stu-id="99a89-164">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="99a89-165">Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)Richtlinie angeben möchten:</span><span class="sxs-lookup"><span data-stu-id="99a89-165">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="99a89-166">Autorisierung für den Zugriff auf einen Ordner von Bereichen erforderlich</span><span class="sxs-lookup"><span data-stu-id="99a89-166">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="99a89-167">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>, um allen Bereichen in einem Ordner unter dem angegebenen Pfad eine <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="99a89-167">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="99a89-168">Der Ordner Pfad ist der Pfad des Ordners relativ zum Seiten Stammverzeichnis für den angegebenen Bereich.</span><span class="sxs-lookup"><span data-stu-id="99a89-168">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="99a89-169">Beispielsweise wird der Ordner Pfad für die Dateien unter *Bereiche/Identität/Seiten/verwalten/* ist */Manage*.</span><span class="sxs-lookup"><span data-stu-id="99a89-169">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="99a89-170">Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)Richtlinie angeben möchten:</span><span class="sxs-lookup"><span data-stu-id="99a89-170">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="99a89-171">Anonymen Zugriff auf eine Seite zulassen</span><span class="sxs-lookup"><span data-stu-id="99a89-171">Allow anonymous access to a page</span></span>

<span data-ttu-id="99a89-172">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>, um eine <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> zu einer Seite unter dem angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="99a89-172">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="99a89-173">Der angegebene Pfad ist der Ansichts-Engine-Pfad. Dies ist der Razor Pages relative relative Pfad ohne Erweiterung und enthält nur Schrägstriche.</span><span class="sxs-lookup"><span data-stu-id="99a89-173">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="99a89-174">Anonymen Zugriff auf einen Ordner von Seiten zulassen</span><span class="sxs-lookup"><span data-stu-id="99a89-174">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="99a89-175">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>, um allen Seiten in einem Ordner unter dem angegebenen Pfad eine <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="99a89-175">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="99a89-176">Der angegebene Pfad ist der Pfad der Ansichts-Engine, der der relative Pfad des Razor Pages Stamms ist.</span><span class="sxs-lookup"><span data-stu-id="99a89-176">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="99a89-177">Hinweis zum Kombinieren von autorisiertem und anonymem Zugriff</span><span class="sxs-lookup"><span data-stu-id="99a89-177">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="99a89-178">Es ist zulässig, einen Ordner mit Seiten anzugeben, die eine Autorisierung erfordern, und als anzugeben, dass eine Seite in diesem Ordner anonymen Zugriff zulässt:</span><span class="sxs-lookup"><span data-stu-id="99a89-178">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="99a89-179">Das Gegenteil ist jedoch nicht gültig.</span><span class="sxs-lookup"><span data-stu-id="99a89-179">The reverse, however, isn't valid.</span></span> <span data-ttu-id="99a89-180">Sie können keinen Ordner von Seiten für den anonymen Zugriff deklarieren und dann eine Seite in diesem Ordner angeben, für die eine Autorisierung erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="99a89-180">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="99a89-181">Das Verlangen der Autorisierung auf der privaten Seite schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="99a89-181">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="99a89-182">Wenn die <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> und <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> auf die Seite angewendet werden, hat der <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> Vorrang und steuert den Zugriff.</span><span class="sxs-lookup"><span data-stu-id="99a89-182">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="99a89-183">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="99a89-183">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end
