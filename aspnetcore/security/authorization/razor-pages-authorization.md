---
title: RazorSeiten Autorisierungs Konventionen in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie den Zugriff auf Seiten mit Konventionen steuern, die Benutzer autorisieren und anonymen Benutzern den Zugriff auf Seiten oder Ordner Seiten gestatten.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/razor-pages-authorization
ms.openlocfilehash: 0492dd3d9b2aee7e844e944bea96259c3ddf18d0
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408720"
---
# <a name="razor-pages-authorization-conventions-in-aspnet-core"></a>Razor<span data-ttu-id="cd184-103">Seiten Autorisierungs Konventionen in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="cd184-103"> Pages authorization conventions in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cd184-104">Eine Möglichkeit, den Zugriff in ihrer Pages-APP zu steuern, Razor besteht darin, beim Start Autorisierungs Konventionen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="cd184-104">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="cd184-105">Diese Konventionen ermöglichen es Ihnen, Benutzer zu autorisieren und anonymen Benutzern den Zugriff auf einzelne Seiten oder Ordner Seiten zu gestatten.</span><span class="sxs-lookup"><span data-stu-id="cd184-105">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="cd184-106">In den in diesem Thema beschriebenen Konventionen werden automatisch [Autorisierungs Filter](xref:mvc/controllers/filters#authorization-filters) angewendet, um den Zugriff zu steuern.</span><span class="sxs-lookup"><span data-stu-id="cd184-106">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="cd184-107">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cd184-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="cd184-108">Die Beispiel-App verwendet die [Cookie- Identity Authentifizierung ohne ASP.net Core ](xref:security/authentication/cookie).</span><span class="sxs-lookup"><span data-stu-id="cd184-108">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="cd184-109">Die Konzepte und Beispiele in diesem Thema gelten auch für apps, die ASP.net Core verwenden Identity .</span><span class="sxs-lookup"><span data-stu-id="cd184-109">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="cd184-110">Um ASP.net Core zu verwenden Identity , befolgen Sie die Anweisungen unter <xref:security/authentication/identity> .</span><span class="sxs-lookup"><span data-stu-id="cd184-110">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="cd184-111">Autorisierung für den Zugriff auf eine Seite erforderlich</span><span class="sxs-lookup"><span data-stu-id="cd184-111">Require authorization to access a page</span></span>

<span data-ttu-id="cd184-112">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> , um der <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> Seite auf dem angegebenen Pfad ein hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="cd184-112">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="cd184-113">Der angegebene Pfad ist der Ansichts-Engine-Pfad. dabei handelt es sich um den Razor relativen Pfad des Seiten Stamms ohne Erweiterung, der nur Schrägstriche enthält.</span><span class="sxs-lookup"><span data-stu-id="cd184-113">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="cd184-114">Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)Richtlinie angeben möchten:</span><span class="sxs-lookup"><span data-stu-id="cd184-114">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="cd184-115">Ein <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> kann auf eine Seiten Modell Klasse mit dem Filter- `[Authorize]` Attribut angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="cd184-115">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="cd184-116">Weitere Informationen finden Sie unter [Autorisieren eines Filter Attributs](xref:razor-pages/filter#authorize-filter-attribute).</span><span class="sxs-lookup"><span data-stu-id="cd184-116">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="cd184-117">Autorisierung für den Zugriff auf einen Ordner von Seiten erforderlich</span><span class="sxs-lookup"><span data-stu-id="cd184-117">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="cd184-118">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> , um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> auf allen Seiten in einem Ordner unter dem angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="cd184-118">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="cd184-119">Der angegebene Pfad ist der Pfad der Ansichts-Engine, der der relative Pfad des Seiten Stamms ist Razor .</span><span class="sxs-lookup"><span data-stu-id="cd184-119">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="cd184-120">Verwenden Sie zum Angeben einer [Autorisierungs Richtlinie](xref:security/authorization/policies)eine [autorizefolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)-Überladung:</span><span class="sxs-lookup"><span data-stu-id="cd184-120">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="cd184-121">Autorisierung für den Zugriff auf eine Bereichs Seite erforderlich</span><span class="sxs-lookup"><span data-stu-id="cd184-121">Require authorization to access an area page</span></span>

<span data-ttu-id="cd184-122">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> , um der <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> Bereichs Seite einen unter dem angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="cd184-122">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="cd184-123">Der Seiten Name ist der Pfad der Datei ohne Erweiterung relativ zum Seiten Stammverzeichnis für den angegebenen Bereich.</span><span class="sxs-lookup"><span data-stu-id="cd184-123">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="cd184-124">Beispielsweise ist der Seiten Name für die Datei *Bereiche/ Identity /pages/manage/accounts.cshtml* */Manage/Accounts*.</span><span class="sxs-lookup"><span data-stu-id="cd184-124">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="cd184-125">Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)Richtlinie angeben möchten:</span><span class="sxs-lookup"><span data-stu-id="cd184-125">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="cd184-126">Autorisierung für den Zugriff auf einen Ordner von Bereichen erforderlich</span><span class="sxs-lookup"><span data-stu-id="cd184-126">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="cd184-127">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> , um einem <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> alle Bereiche in einem Ordner unter dem angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="cd184-127">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="cd184-128">Der Ordner Pfad ist der Pfad des Ordners relativ zum Seiten Stammverzeichnis für den angegebenen Bereich.</span><span class="sxs-lookup"><span data-stu-id="cd184-128">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="cd184-129">Beispielsweise ist der Ordner Pfad für die Dateien unter " *Areas/ Identity /pages/Manage/* " */Manage*.</span><span class="sxs-lookup"><span data-stu-id="cd184-129">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="cd184-130">Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)Richtlinie angeben möchten:</span><span class="sxs-lookup"><span data-stu-id="cd184-130">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="cd184-131">Anonymen Zugriff auf eine Seite zulassen</span><span class="sxs-lookup"><span data-stu-id="cd184-131">Allow anonymous access to a page</span></span>

<span data-ttu-id="cd184-132">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> , um eine einer <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> Seite im angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="cd184-132">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="cd184-133">Der angegebene Pfad ist der Ansichts-Engine-Pfad. dabei handelt es sich um den Razor relativen Pfad des Seiten Stamms ohne Erweiterung, der nur Schrägstriche enthält.</span><span class="sxs-lookup"><span data-stu-id="cd184-133">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="cd184-134">Anonymen Zugriff auf einen Ordner von Seiten zulassen</span><span class="sxs-lookup"><span data-stu-id="cd184-134">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="cd184-135">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> , um <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> auf allen Seiten in einem Ordner unter dem angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="cd184-135">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/3.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="cd184-136">Der angegebene Pfad ist der Pfad der Ansichts-Engine, der der relative Pfad des Seiten Stamms ist Razor .</span><span class="sxs-lookup"><span data-stu-id="cd184-136">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="cd184-137">Hinweis zum Kombinieren von autorisiertem und anonymem Zugriff</span><span class="sxs-lookup"><span data-stu-id="cd184-137">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="cd184-138">Es ist zulässig, anzugeben, dass für einen Ordner von Seiten eine Autorisierung erforderlich ist, und dann anzugeben, dass eine Seite in diesem Ordner anonymen Zugriff zulässt:</span><span class="sxs-lookup"><span data-stu-id="cd184-138">It's valid to specify that a folder of pages requires authorization and then specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="cd184-139">Das Gegenteil ist jedoch nicht gültig.</span><span class="sxs-lookup"><span data-stu-id="cd184-139">The reverse, however, isn't valid.</span></span> <span data-ttu-id="cd184-140">Sie können keinen Ordner von Seiten für den anonymen Zugriff deklarieren und dann eine Seite in diesem Ordner angeben, für die eine Autorisierung erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="cd184-140">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="cd184-141">Das Verlangen der Autorisierung auf der privaten Seite schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="cd184-141">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="cd184-142">Wenn sowohl als <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> auch <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> auf die Seite angewendet werden, hat <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> Vorrang und steuert den Zugriff.</span><span class="sxs-lookup"><span data-stu-id="cd184-142">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cd184-143">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="cd184-143">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cd184-144">Eine Möglichkeit, den Zugriff in ihrer Pages-APP zu steuern, Razor besteht darin, beim Start Autorisierungs Konventionen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="cd184-144">One way to control access in your Razor Pages app is to use authorization conventions at startup.</span></span> <span data-ttu-id="cd184-145">Diese Konventionen ermöglichen es Ihnen, Benutzer zu autorisieren und anonymen Benutzern den Zugriff auf einzelne Seiten oder Ordner Seiten zu gestatten.</span><span class="sxs-lookup"><span data-stu-id="cd184-145">These conventions allow you to authorize users and allow anonymous users to access individual pages or folders of pages.</span></span> <span data-ttu-id="cd184-146">In den in diesem Thema beschriebenen Konventionen werden automatisch [Autorisierungs Filter](xref:mvc/controllers/filters#authorization-filters) angewendet, um den Zugriff zu steuern.</span><span class="sxs-lookup"><span data-stu-id="cd184-146">The conventions described in this topic automatically apply [authorization filters](xref:mvc/controllers/filters#authorization-filters) to control access.</span></span>

<span data-ttu-id="cd184-147">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cd184-147">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/razor-pages-authorization/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="cd184-148">Die Beispiel-App verwendet die [Cookie- Identity Authentifizierung ohne ASP.net Core ](xref:security/authentication/cookie).</span><span class="sxs-lookup"><span data-stu-id="cd184-148">The sample app uses [cookie authentication without ASP.NET Core Identity](xref:security/authentication/cookie).</span></span> <span data-ttu-id="cd184-149">Die Konzepte und Beispiele in diesem Thema gelten auch für apps, die ASP.net Core verwenden Identity .</span><span class="sxs-lookup"><span data-stu-id="cd184-149">The concepts and examples shown in this topic apply equally to apps that use ASP.NET Core Identity.</span></span> <span data-ttu-id="cd184-150">Um ASP.net Core zu verwenden Identity , befolgen Sie die Anweisungen unter <xref:security/authentication/identity> .</span><span class="sxs-lookup"><span data-stu-id="cd184-150">To use ASP.NET Core Identity, follow the guidance in <xref:security/authentication/identity>.</span></span>

## <a name="require-authorization-to-access-a-page"></a><span data-ttu-id="cd184-151">Autorisierung für den Zugriff auf eine Seite erforderlich</span><span class="sxs-lookup"><span data-stu-id="cd184-151">Require authorization to access a page</span></span>

<span data-ttu-id="cd184-152">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> , um der <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> Seite auf dem angegebenen Pfad ein hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="cd184-152">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,4)]

<span data-ttu-id="cd184-153">Der angegebene Pfad ist der Ansichts-Engine-Pfad. dabei handelt es sich um den Razor relativen Pfad des Seiten Stamms ohne Erweiterung, der nur Schrägstriche enthält.</span><span class="sxs-lookup"><span data-stu-id="cd184-153">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

<span data-ttu-id="cd184-154">Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*)Richtlinie angeben möchten:</span><span class="sxs-lookup"><span data-stu-id="cd184-154">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizePage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizePage*):</span></span>

```csharp
options.Conventions.AuthorizePage("/Contact", "AtLeast21");
```

> [!NOTE]
> <span data-ttu-id="cd184-155">Ein <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> kann auf eine Seiten Modell Klasse mit dem Filter- `[Authorize]` Attribut angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="cd184-155">An <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> can be applied to a page model class with the `[Authorize]` filter attribute.</span></span> <span data-ttu-id="cd184-156">Weitere Informationen finden Sie unter [Autorisieren eines Filter Attributs](xref:razor-pages/filter#authorize-filter-attribute).</span><span class="sxs-lookup"><span data-stu-id="cd184-156">For more information, see [Authorize filter attribute](xref:razor-pages/filter#authorize-filter-attribute).</span></span>

## <a name="require-authorization-to-access-a-folder-of-pages"></a><span data-ttu-id="cd184-157">Autorisierung für den Zugriff auf einen Ordner von Seiten erforderlich</span><span class="sxs-lookup"><span data-stu-id="cd184-157">Require authorization to access a folder of pages</span></span>

<span data-ttu-id="cd184-158">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> , um <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> auf allen Seiten in einem Ordner unter dem angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="cd184-158">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,5)]

<span data-ttu-id="cd184-159">Der angegebene Pfad ist der Pfad der Ansichts-Engine, der der relative Pfad des Seiten Stamms ist Razor .</span><span class="sxs-lookup"><span data-stu-id="cd184-159">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

<span data-ttu-id="cd184-160">Verwenden Sie zum Angeben einer [Autorisierungs Richtlinie](xref:security/authorization/policies)eine [autorizefolder](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*)-Überladung:</span><span class="sxs-lookup"><span data-stu-id="cd184-160">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeFolder*):</span></span>

```csharp
options.Conventions.AuthorizeFolder("/Private", "AtLeast21");
```

## <a name="require-authorization-to-access-an-area-page"></a><span data-ttu-id="cd184-161">Autorisierung für den Zugriff auf eine Bereichs Seite erforderlich</span><span class="sxs-lookup"><span data-stu-id="cd184-161">Require authorization to access an area page</span></span>

<span data-ttu-id="cd184-162">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> , um der <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> Bereichs Seite einen unter dem angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="cd184-162">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to the area page at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts");
```

<span data-ttu-id="cd184-163">Der Seiten Name ist der Pfad der Datei ohne Erweiterung relativ zum Seiten Stammverzeichnis für den angegebenen Bereich.</span><span class="sxs-lookup"><span data-stu-id="cd184-163">The page name is the path of the file without an extension relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="cd184-164">Beispielsweise ist der Seiten Name für die Datei *Bereiche/ Identity /pages/manage/accounts.cshtml* */Manage/Accounts*.</span><span class="sxs-lookup"><span data-stu-id="cd184-164">For example, the page name for the file *Areas/Identity/Pages/Manage/Accounts.cshtml* is */Manage/Accounts*.</span></span>

<span data-ttu-id="cd184-165">Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*)Richtlinie angeben möchten:</span><span class="sxs-lookup"><span data-stu-id="cd184-165">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaPage overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaPage*):</span></span>

```csharp
options.Conventions.AuthorizeAreaPage("Identity", "/Manage/Accounts", "AtLeast21");
```

## <a name="require-authorization-to-access-a-folder-of-areas"></a><span data-ttu-id="cd184-166">Autorisierung für den Zugriff auf einen Ordner von Bereichen erforderlich</span><span class="sxs-lookup"><span data-stu-id="cd184-166">Require authorization to access a folder of areas</span></span>

<span data-ttu-id="cd184-167">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> , um einem <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> alle Bereiche in einem Ordner unter dem angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="cd184-167">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> to all of the areas in a folder at the specified path:</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage");
```

<span data-ttu-id="cd184-168">Der Ordner Pfad ist der Pfad des Ordners relativ zum Seiten Stammverzeichnis für den angegebenen Bereich.</span><span class="sxs-lookup"><span data-stu-id="cd184-168">The folder path is the path of the folder relative to the pages root directory for the specified area.</span></span> <span data-ttu-id="cd184-169">Beispielsweise ist der Ordner Pfad für die Dateien unter " *Areas/ Identity /pages/Manage/* " */Manage*.</span><span class="sxs-lookup"><span data-stu-id="cd184-169">For example, the folder path for the files under *Areas/Identity/Pages/Manage/* is */Manage*.</span></span>

<span data-ttu-id="cd184-170">Verwenden Sie eine [Autorisierungs Richtlinie](xref:security/authorization/policies), wenn Sie eine [Autorisierungs](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*)Richtlinie angeben möchten:</span><span class="sxs-lookup"><span data-stu-id="cd184-170">To specify an [authorization policy](xref:security/authorization/policies), use an [AuthorizeAreaFolder overload](xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AuthorizeAreaFolder*):</span></span>

```csharp
options.Conventions.AuthorizeAreaFolder("Identity", "/Manage", "AtLeast21");
```

## <a name="allow-anonymous-access-to-a-page"></a><span data-ttu-id="cd184-171">Anonymen Zugriff auf eine Seite zulassen</span><span class="sxs-lookup"><span data-stu-id="cd184-171">Allow anonymous access to a page</span></span>

<span data-ttu-id="cd184-172">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> , um eine einer <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> Seite im angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="cd184-172">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToPage*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to a page at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,6)]

<span data-ttu-id="cd184-173">Der angegebene Pfad ist der Ansichts-Engine-Pfad. dabei handelt es sich um den Razor relativen Pfad des Seiten Stamms ohne Erweiterung, der nur Schrägstriche enthält.</span><span class="sxs-lookup"><span data-stu-id="cd184-173">The specified path is the View Engine path, which is the Razor Pages root relative path without an extension and containing only forward slashes.</span></span>

## <a name="allow-anonymous-access-to-a-folder-of-pages"></a><span data-ttu-id="cd184-174">Anonymen Zugriff auf einen Ordner von Seiten zulassen</span><span class="sxs-lookup"><span data-stu-id="cd184-174">Allow anonymous access to a folder of pages</span></span>

<span data-ttu-id="cd184-175">Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> Konvention über <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> , um <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> auf allen Seiten in einem Ordner unter dem angegebenen Pfad hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="cd184-175">Use the <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AllowAnonymousToFolder*> convention via <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> to add an <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> to all of the pages in a folder at the specified path:</span></span>

[!code-csharp[](razor-pages-authorization/samples/2.x/AuthorizationSample/Startup.cs?name=snippet1&highlight=2,7)]

<span data-ttu-id="cd184-176">Der angegebene Pfad ist der Pfad der Ansichts-Engine, der der relative Pfad des Seiten Stamms ist Razor .</span><span class="sxs-lookup"><span data-stu-id="cd184-176">The specified path is the View Engine path, which is the Razor Pages root relative path.</span></span>

## <a name="note-on-combining-authorized-and-anonymous-access"></a><span data-ttu-id="cd184-177">Hinweis zum Kombinieren von autorisiertem und anonymem Zugriff</span><span class="sxs-lookup"><span data-stu-id="cd184-177">Note on combining authorized and anonymous access</span></span>

<span data-ttu-id="cd184-178">Es ist zulässig, einen Ordner mit Seiten anzugeben, die eine Autorisierung erfordern, und als anzugeben, dass eine Seite in diesem Ordner anonymen Zugriff zulässt:</span><span class="sxs-lookup"><span data-stu-id="cd184-178">It's valid to specify that a folder of pages that require authorization and than specify that a page within that folder allows anonymous access:</span></span>

```csharp
// This works.
.AuthorizeFolder("/Private").AllowAnonymousToPage("/Private/Public")
```

<span data-ttu-id="cd184-179">Das Gegenteil ist jedoch nicht gültig.</span><span class="sxs-lookup"><span data-stu-id="cd184-179">The reverse, however, isn't valid.</span></span> <span data-ttu-id="cd184-180">Sie können keinen Ordner von Seiten für den anonymen Zugriff deklarieren und dann eine Seite in diesem Ordner angeben, für die eine Autorisierung erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="cd184-180">You can't declare a folder of pages for anonymous access and then specify a page within that folder that requires authorization:</span></span>

```csharp
// This doesn't work!
.AllowAnonymousToFolder("/Public").AuthorizePage("/Public/Private")
```

<span data-ttu-id="cd184-181">Das Verlangen der Autorisierung auf der privaten Seite schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="cd184-181">Requiring authorization on the Private page fails.</span></span> <span data-ttu-id="cd184-182">Wenn sowohl als <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> auch <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> auf die Seite angewendet werden, hat <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> Vorrang und steuert den Zugriff.</span><span class="sxs-lookup"><span data-stu-id="cd184-182">When both the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> and <xref:Microsoft.AspNetCore.Mvc.Authorization.AuthorizeFilter> are applied to the page, the <xref:Microsoft.AspNetCore.Mvc.Authorization.AllowAnonymousFilter> takes precedence and controls access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cd184-183">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="cd184-183">Additional resources</span></span>

* <xref:razor-pages/razor-pages-conventions>
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>

::: moniker-end
