---
title: Verwenden von Teilansichten in ASP.NET Core
author: ardalis
description: Erfahren Sie, wie Sie Teilansichten verwenden können, um große Markupdateien aufzuteilen und die Duplizierung von allgemeinem Markup auf Webseiten in ASP.NET Core-Anwendungen zu verringern.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
uid: mvc/views/partial
ms.openlocfilehash: e13b2ea974697bb12c121d1a70fb5079d6aadb2d
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64887465"
---
# <a name="partial-views-in-aspnet-core"></a><span data-ttu-id="83ad6-103">Verwenden von Teilansichten in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="83ad6-103">Partial views in ASP.NET Core</span></span>

<span data-ttu-id="83ad6-104">Von [Steve Smith](https://ardalis.com/), [Luke Latham](https://github.com/guardrex), [Maher JENDOUBI](https://twitter.com/maherjend), [Rick Anderson](https://twitter.com/RickAndMSFT) und [Scott Sauber](https://twitter.com/scottsauber)</span><span class="sxs-lookup"><span data-stu-id="83ad6-104">By [Steve Smith](https://ardalis.com/), [Luke Latham](https://github.com/guardrex), [Maher JENDOUBI](https://twitter.com/maherjend), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Scott Sauber](https://twitter.com/scottsauber)</span></span>

<span data-ttu-id="83ad6-105">Eine Teilansicht ist eine [Razor](xref:mvc/views/razor)-Markupdatei (*CSHTML-Datei*), die HTML-Ausgabe *innerhalb* der gerenderten Ausgabe einer anderen Markupdatei rendert.</span><span class="sxs-lookup"><span data-stu-id="83ad6-105">A partial view is a [Razor](xref:mvc/views/razor) markup file (*.cshtml*) that renders HTML output *within* another markup file's rendered output.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="83ad6-106">Der Begriff *Teilansicht* wird verwendet, wenn entweder eine MVC-App entwickelt wird, in der Markupdateien *Ansichten* genannt werden, oder eine Razor Pages-App, in der Markupdateien *Seiten* genannt werden.</span><span class="sxs-lookup"><span data-stu-id="83ad6-106">The term *partial view* is used when developing either an MVC app, where markup files are called *views*, or a Razor Pages app, where markup files are called *pages*.</span></span> <span data-ttu-id="83ad6-107">In diesem Thema werden MVC-Ansichten und Razor Pages-Seiten allgemein als *Markupdateien* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="83ad6-107">This topic generically refers to MVC views and Razor Pages pages as *markup files*.</span></span>

::: moniker-end

<span data-ttu-id="83ad6-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/partial/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="83ad6-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/partial/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-partial-views"></a><span data-ttu-id="83ad6-109">Wann werden Teilansichten verwendet?</span><span class="sxs-lookup"><span data-stu-id="83ad6-109">When to use partial views</span></span>

<span data-ttu-id="83ad6-110">Teilansichten bieten eine effektive Möglichkeit, um Folgendes zu erreichen:</span><span class="sxs-lookup"><span data-stu-id="83ad6-110">Partial views are an effective way to:</span></span>

* <span data-ttu-id="83ad6-111">Aufteilen großer Markupdateien in kleinere Komponenten.</span><span class="sxs-lookup"><span data-stu-id="83ad6-111">Break up large markup files into smaller components.</span></span>

  <span data-ttu-id="83ad6-112">In einer großen, komplexen Markupdatei, die aus mehreren logischen Teilen besteht, bringt es Vorteile mit sich, mit jeder einzelnen Komponente isoliert in einer Teilansicht zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="83ad6-112">In a large, complex markup file composed of several logical pieces, there's an advantage to working with each piece isolated into a partial view.</span></span> <span data-ttu-id="83ad6-113">Der Code in der Markupdatei ist verwaltbar, weil das Markup nur die allgemeine Seitenstruktur und Verweise auf Teilansichten enthält.</span><span class="sxs-lookup"><span data-stu-id="83ad6-113">The code in the markup file is manageable because the markup only contains the overall page structure and references to partial views.</span></span>
* <span data-ttu-id="83ad6-114">Verringern der Duplizierung von allgemeinem Markupinhalt in Markupdateien.</span><span class="sxs-lookup"><span data-stu-id="83ad6-114">Reduce the duplication of common markup content across markup files.</span></span>

  <span data-ttu-id="83ad6-115">Wenn die gleichen Markupelemente in verschiedenen Markupdateien verwendet werden, entfernt eine Teilansicht die Duplizierung von Markupinhalten in einer Teilansichtsdatei.</span><span class="sxs-lookup"><span data-stu-id="83ad6-115">When the same markup elements are used across markup files, a partial view removes the duplication of markup content into one partial view file.</span></span> <span data-ttu-id="83ad6-116">Wenn das Markup in der Teilansicht geändert wird, wird die gerenderte Ausgabe der Markupdateien aktualisiert, die die Teilansicht verwenden.</span><span class="sxs-lookup"><span data-stu-id="83ad6-116">When the markup is changed in the partial view, it updates the rendered output of the markup files that use the partial view.</span></span>

<span data-ttu-id="83ad6-117">Teilansichten sollte nicht verwendet werden, um allgemeine Layoutelemente zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="83ad6-117">Partial views shouldn't be used to maintain common layout elements.</span></span> <span data-ttu-id="83ad6-118">Häufig verwendete Layoutelemente müssen in [_Layout.cshtml](xref:mvc/views/layout)-Dateien angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="83ad6-118">Common layout elements should be specified in [_Layout.cshtml](xref:mvc/views/layout) files.</span></span>

<span data-ttu-id="83ad6-119">Verwenden Sie keine Teilansicht, wenn für das Rendern des Markups eine komplexe Renderinglogik oder Codeausführung erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="83ad6-119">Don't use a partial view where complex rendering logic or code execution is required to render the markup.</span></span> <span data-ttu-id="83ad6-120">Verwenden Sie anstelle einer Teilansicht eine [Ansichtskomponente](xref:mvc/views/view-components).</span><span class="sxs-lookup"><span data-stu-id="83ad6-120">Instead of a partial view, use a [view component](xref:mvc/views/view-components).</span></span>

## <a name="declare-partial-views"></a><span data-ttu-id="83ad6-121">Deklarieren von Teilansichten</span><span class="sxs-lookup"><span data-stu-id="83ad6-121">Declare partial views</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="83ad6-122">Eine Teilansicht ist eine *CSHTML*-Markupdatei, die innerhalb des Ordners *Views* (MVC) oder des Ordners *Pages* (Razor Pages) verwaltet wird.</span><span class="sxs-lookup"><span data-stu-id="83ad6-122">A partial view is a *.cshtml* markup file maintained within the *Views* folder (MVC) or *Pages* folder (Razor Pages).</span></span>

<span data-ttu-id="83ad6-123">In ASP.NET Core MVC kann <xref:Microsoft.AspNetCore.Mvc.ViewResult> eines Controllers eine Ansicht oder eine Teilansicht zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="83ad6-123">In ASP.NET Core MVC, a controller's <xref:Microsoft.AspNetCore.Mvc.ViewResult> is capable of returning either a view or a partial view.</span></span> <span data-ttu-id="83ad6-124">Eine analoge Funktion ist für Razor Pages in ASP.NET Core 2.2 geplant.</span><span class="sxs-lookup"><span data-stu-id="83ad6-124">An analogous capability is planned for Razor Pages in ASP.NET Core 2.2.</span></span> <span data-ttu-id="83ad6-125">In Razor Pages kann <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> ein <xref:Microsoft.AspNetCore.Mvc.PartialViewResult> zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="83ad6-125">In Razor Pages, a <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> can return a <xref:Microsoft.AspNetCore.Mvc.PartialViewResult>.</span></span> <span data-ttu-id="83ad6-126">Das Verweisen auf und Rendern von Teilansichten wird im Abschnitt [Verweisen auf eine Teilansicht](#reference-a-partial-view) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="83ad6-126">Referencing and rendering partial views is described in the [Reference a partial view](#reference-a-partial-view) section.</span></span>

<span data-ttu-id="83ad6-127">Im Gegensatz zu MVC-Ansichten oder Seitenrendering führt eine Teilansicht *_ViewStart.cshtml* nicht aus.</span><span class="sxs-lookup"><span data-stu-id="83ad6-127">Unlike MVC view or page rendering, a partial view doesn't run *_ViewStart.cshtml*.</span></span> <span data-ttu-id="83ad6-128">Weitere Informationen zu *_ViewStart.cshtml* finden Sie unter <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="83ad6-128">For more information on *_ViewStart.cshtml*, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="83ad6-129">Dateinamen von Teilansichten beginnen häufig mit einem Unterstrich (`_`).</span><span class="sxs-lookup"><span data-stu-id="83ad6-129">Partial view file names often begin with an underscore (`_`).</span></span> <span data-ttu-id="83ad6-130">Diese Namenskonvention ist keine Voraussetzung, sie hilft aber dabei, Teilansichten von Ansichten und Seiten visuell zu unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="83ad6-130">This naming convention isn't required, but it helps to visually differentiate partial views from views and pages.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="83ad6-131">Eine Teilansicht ist eine *CSHTML*-Markupdatei, die innerhalb des Ordners *Views* verwaltet wird.</span><span class="sxs-lookup"><span data-stu-id="83ad6-131">A partial view is a *.cshtml* markup file maintained within the *Views* folder.</span></span>

<span data-ttu-id="83ad6-132"><xref:Microsoft.AspNetCore.Mvc.ViewResult> eines Controllers kann eine Ansicht oder eine Teilansicht zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="83ad6-132">A controller's <xref:Microsoft.AspNetCore.Mvc.ViewResult> is capable of returning either a view or a partial view.</span></span>

<span data-ttu-id="83ad6-133">Im Gegensatz zu MVC-Ansichten oder Rendering führt eine Teilansicht *_ViewStart.cshtml* nicht aus.</span><span class="sxs-lookup"><span data-stu-id="83ad6-133">Unlike MVC view rendering, a partial view doesn't run *_ViewStart.cshtml*.</span></span> <span data-ttu-id="83ad6-134">Weitere Informationen zu *_ViewStart.cshtml* finden Sie unter <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="83ad6-134">For more information on *_ViewStart.cshtml*, see <xref:mvc/views/layout>.</span></span>

<span data-ttu-id="83ad6-135">Dateinamen von Teilansichten beginnen häufig mit einem Unterstrich (`_`).</span><span class="sxs-lookup"><span data-stu-id="83ad6-135">Partial view file names often begin with an underscore (`_`).</span></span> <span data-ttu-id="83ad6-136">Diese Namenskonvention ist keine Voraussetzung, sie hilft aber dabei, Teilansichten von Ansichten visuell zu unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="83ad6-136">This naming convention isn't required, but it helps to visually differentiate partial views from views.</span></span>

::: moniker-end

## <a name="reference-a-partial-view"></a><span data-ttu-id="83ad6-137">Verweisen auf eine Teilansicht</span><span class="sxs-lookup"><span data-stu-id="83ad6-137">Reference a partial view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="83ad6-138">Innerhalb einer Markupdatei gibt es mehrere Möglichkeiten, auf eine Teilansicht zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="83ad6-138">Within a markup file, there are several ways to reference a partial view.</span></span> <span data-ttu-id="83ad6-139">Es wird empfohlen, dass Apps einen der folgenden Ansätze für asynchrones Rendering verwenden:</span><span class="sxs-lookup"><span data-stu-id="83ad6-139">We recommend that apps use one of the following asynchronous rendering approaches:</span></span>

* [<span data-ttu-id="83ad6-140">Hilfsprogramm für Teiltags</span><span class="sxs-lookup"><span data-stu-id="83ad6-140">Partial Tag Helper</span></span>](#partial-tag-helper)
* [<span data-ttu-id="83ad6-141">Asynchrones HTML-Hilfsprogramm</span><span class="sxs-lookup"><span data-stu-id="83ad6-141">Asynchronous HTML Helper</span></span>](#asynchronous-html-helper)

::: moniker-end

::: moniker range="< aspnetcore-2.1"

<span data-ttu-id="83ad6-142">Innerhalb einer Markupdatei gibt es zwei Möglichkeiten, auf eine Teilansicht zu verweisen:</span><span class="sxs-lookup"><span data-stu-id="83ad6-142">Within a markup file, there are two ways to reference a partial view:</span></span>

* [<span data-ttu-id="83ad6-143">Asynchrones HTML-Hilfsprogramm</span><span class="sxs-lookup"><span data-stu-id="83ad6-143">Asynchronous HTML Helper</span></span>](#asynchronous-html-helper)
* [<span data-ttu-id="83ad6-144">Synchrones HTML-Hilfsprogramm</span><span class="sxs-lookup"><span data-stu-id="83ad6-144">Synchronous HTML Helper</span></span>](#synchronous-html-helper)

<span data-ttu-id="83ad6-145">Es wird empfohlen, dass Apps das [asynchrone HTML-Hilfsprogramm](#asynchronous-html-helper) verwenden.</span><span class="sxs-lookup"><span data-stu-id="83ad6-145">We recommend that apps use the [Asynchronous HTML Helper](#asynchronous-html-helper).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="partial-tag-helper"></a><span data-ttu-id="83ad6-146">Hilfsprogramm für Teiltags</span><span class="sxs-lookup"><span data-stu-id="83ad6-146">Partial Tag Helper</span></span>

<span data-ttu-id="83ad6-147">Das [Hilfsprogramm für Teiltags](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) erfordert ASP.NET Core 2.1 oder höher.</span><span class="sxs-lookup"><span data-stu-id="83ad6-147">The [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) requires ASP.NET Core 2.1 or later.</span></span>

<span data-ttu-id="83ad6-148">Das Hilfsprogramm für Teiltags rendert Inhalte asynchron und verwendet eine HTML-ähnliche Syntax:</span><span class="sxs-lookup"><span data-stu-id="83ad6-148">The Partial Tag Helper renders content asynchronously and uses an HTML-like syntax:</span></span>

```cshtml
<partial name="_PartialName" />
```

<span data-ttu-id="83ad6-149">Wenn eine Dateierweiterung vorhanden ist, verweist das Hilfsprogramm für Teiltags auf eine Teilansicht, die sich im gleichen Ordner wie die Markupdatei befinden muss, die die Teilansicht aufruft:</span><span class="sxs-lookup"><span data-stu-id="83ad6-149">When a file extension is present, the Tag Helper references a partial view that must be in the same folder as the markup file calling the partial view:</span></span>

```cshtml
<partial name="_PartialName.cshtml" />
```

<span data-ttu-id="83ad6-150">Im folgende Beispiel wird aus dem Stammverzeichnis der App auf eine Teilansicht verwiesen.</span><span class="sxs-lookup"><span data-stu-id="83ad6-150">The following example references a partial view from the app root.</span></span> <span data-ttu-id="83ad6-151">Pfade, die mit einer Tilde und einem Schrägstrich (`~/`) oder einem Schrägstrich (`/`) beginnen, verweisen auf den Stamm der App:</span><span class="sxs-lookup"><span data-stu-id="83ad6-151">Paths that start with a tilde-slash (`~/`) or a slash (`/`) refer to the app root:</span></span>

<span data-ttu-id="83ad6-152">**Razor Pages**</span><span class="sxs-lookup"><span data-stu-id="83ad6-152">**Razor Pages**</span></span>

```cshtml
<partial name="~/Pages/Folder/_PartialName.cshtml" />
<partial name="/Pages/Folder/_PartialName.cshtml" />
```

<span data-ttu-id="83ad6-153">**MVC**</span><span class="sxs-lookup"><span data-stu-id="83ad6-153">**MVC**</span></span>

```cshtml
<partial name="~/Views/Folder/_PartialName.cshtml" />
<partial name="/Views/Folder/_PartialName.cshtml" />
```

<span data-ttu-id="83ad6-154">Das folgende Beispiel verweist auf eine Teilansicht mit einem relativen Pfad:</span><span class="sxs-lookup"><span data-stu-id="83ad6-154">The following example references a partial view with a relative path:</span></span>

```cshtml
<partial name="../Account/_PartialName.cshtml" />
```

<span data-ttu-id="83ad6-155">Weitere Informationen finden Sie unter <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="83ad6-155">For more information, see <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.</span></span>

::: moniker-end

### <a name="asynchronous-html-helper"></a><span data-ttu-id="83ad6-156">Hilfsprogramm für asynchrone HTML</span><span class="sxs-lookup"><span data-stu-id="83ad6-156">Asynchronous HTML Helper</span></span>

<span data-ttu-id="83ad6-157">Wenn Sie ein HTML-Hilfsprogramm verwenden, stellt das Verwenden von <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*> eine bewährte Methode dar.</span><span class="sxs-lookup"><span data-stu-id="83ad6-157">When using an HTML Helper, the best practice is to use <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync*>.</span></span> <span data-ttu-id="83ad6-158">`PartialAsync` gibt einen <xref:Microsoft.AspNetCore.Html.IHtmlContent>-Typ in einem <xref:System.Threading.Tasks.Task%601> als Wrapper zurück.</span><span class="sxs-lookup"><span data-stu-id="83ad6-158">`PartialAsync` returns an <xref:Microsoft.AspNetCore.Html.IHtmlContent> type wrapped in a <xref:System.Threading.Tasks.Task%601>.</span></span> <span data-ttu-id="83ad6-159">Sie können auf die Methode verweisen, indem Sie dem erwarteten Aufruf das Zeichen `@` als Präfix voranstellen:</span><span class="sxs-lookup"><span data-stu-id="83ad6-159">The method is referenced by prefixing the awaited call with an `@` character:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName")
```

<span data-ttu-id="83ad6-160">Wenn die Dateierweiterung vorhanden ist, verweist das HTML-Hilfsprogramm auf eine Teilansicht, die sich im gleichen Ordner wie die Markupdatei befinden muss, die die Teilansicht aufruft:</span><span class="sxs-lookup"><span data-stu-id="83ad6-160">When the file extension is present, the HTML Helper references a partial view that must be in the same folder as the markup file calling the partial view:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName.cshtml")
```

<span data-ttu-id="83ad6-161">Im folgende Beispiel wird aus dem Stammverzeichnis der App auf eine Teilansicht verwiesen.</span><span class="sxs-lookup"><span data-stu-id="83ad6-161">The following example references a partial view from the app root.</span></span> <span data-ttu-id="83ad6-162">Pfade, die mit einer Tilde und einem Schrägstrich (`~/`) oder einem Schrägstrich (`/`) beginnen, verweisen auf den Stamm der App:</span><span class="sxs-lookup"><span data-stu-id="83ad6-162">Paths that start with a tilde-slash (`~/`) or a slash (`/`) refer to the app root:</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="83ad6-163">**Razor Pages**</span><span class="sxs-lookup"><span data-stu-id="83ad6-163">**Razor Pages**</span></span>

```cshtml
@await Html.PartialAsync("~/Pages/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Pages/Folder/_PartialName.cshtml")
```

<span data-ttu-id="83ad6-164">**MVC**</span><span class="sxs-lookup"><span data-stu-id="83ad6-164">**MVC**</span></span>

::: moniker-end

```cshtml
@await Html.PartialAsync("~/Views/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Views/Folder/_PartialName.cshtml")
```

<span data-ttu-id="83ad6-165">Das folgende Beispiel verweist auf eine Teilansicht mit einem relativen Pfad:</span><span class="sxs-lookup"><span data-stu-id="83ad6-165">The following example references a partial view with a relative path:</span></span>

```cshtml
@await Html.PartialAsync("../Account/_LoginPartial.cshtml")
```

<span data-ttu-id="83ad6-166">Alternativ können Sie eine Teilansicht auch mit <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*> rendern.</span><span class="sxs-lookup"><span data-stu-id="83ad6-166">Alternatively, you can render a partial view with <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync*>.</span></span> <span data-ttu-id="83ad6-167">Diese Methode gibt keinen <xref:Microsoft.AspNetCore.Html.IHtmlContent> zurück.</span><span class="sxs-lookup"><span data-stu-id="83ad6-167">This method doesn't return an <xref:Microsoft.AspNetCore.Html.IHtmlContent>.</span></span> <span data-ttu-id="83ad6-168">Sie streamt die gerenderte Ausgabe direkt an die Antwort.</span><span class="sxs-lookup"><span data-stu-id="83ad6-168">It streams the rendered output directly to the response.</span></span> <span data-ttu-id="83ad6-169">Da die Methode kein Ergebnis zurückgibt, muss sie in einem Razor-Codeblock aufgerufen werden:</span><span class="sxs-lookup"><span data-stu-id="83ad6-169">Because the method doesn't return a result, it must be called within a Razor code block:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Home/Discovery.cshtml?name=snippet_RenderPartialAsync)]

<span data-ttu-id="83ad6-170">Da `RenderPartialAsync` gerenderte Inhalte streamt, bietet diese Vorgehensweise in einigen Szenarien eine bessere Leistung.</span><span class="sxs-lookup"><span data-stu-id="83ad6-170">Since `RenderPartialAsync` streams rendered content, it provides better performance in some scenarios.</span></span> <span data-ttu-id="83ad6-171">In leistungskritischen Situationen sollten Sie die Seite mit beiden Ansätzen einem Benchmarktest unterziehen und den Ansatz verwenden, der eine schnellere Antwort generiert.</span><span class="sxs-lookup"><span data-stu-id="83ad6-171">In performance-critical situations, benchmark the page using both approaches and use the approach that generates a faster response.</span></span>

### <a name="synchronous-html-helper"></a><span data-ttu-id="83ad6-172">Hilfsprogramm für synchrone HTML</span><span class="sxs-lookup"><span data-stu-id="83ad6-172">Synchronous HTML Helper</span></span>

<span data-ttu-id="83ad6-173"><xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> und <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> sind die synchronen Entsprechungen von `PartialAsync` bzw. `RenderPartialAsync`.</span><span class="sxs-lookup"><span data-stu-id="83ad6-173"><xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial*> and <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial*> are the synchronous equivalents of `PartialAsync` and `RenderPartialAsync`, respectively.</span></span> <span data-ttu-id="83ad6-174">Diese synchronen Entsprechungen werden nicht empfohlen, da sie in manchen Szenarien zu einem Deadlock führen können.</span><span class="sxs-lookup"><span data-stu-id="83ad6-174">The synchronous equivalents aren't recommended because there are scenarios in which they deadlock.</span></span> <span data-ttu-id="83ad6-175">Die synchronen Methoden sollen in einem zukünftigen Release entfernt werden.</span><span class="sxs-lookup"><span data-stu-id="83ad6-175">The synchronous methods are targeted for removal in a future release.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="83ad6-176">Wenn Sie Code ausführen müssen, verwenden Sie anstelle von Teilansichten [Ansichtskomponenten](xref:mvc/views/view-components).</span><span class="sxs-lookup"><span data-stu-id="83ad6-176">If you need to execute code, use a [view component](xref:mvc/views/view-components) instead of a partial view.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="83ad6-177">Das Aufrufen von `Partial` oder `RenderPartial` führt zu einer Visual Studio Analyzer-Warnung.</span><span class="sxs-lookup"><span data-stu-id="83ad6-177">Calling `Partial` or `RenderPartial` results in a Visual Studio analyzer warning.</span></span> <span data-ttu-id="83ad6-178">Beispielsweise führt das Vorhandensein von `Partial` zu folgender Warnmeldung:</span><span class="sxs-lookup"><span data-stu-id="83ad6-178">For example, the presence of `Partial` yields the following warning message:</span></span>

> <span data-ttu-id="83ad6-179">Die Verwendung von IHtmlHelper.Partial kann zu Anwendungsdeadlocks führen.</span><span class="sxs-lookup"><span data-stu-id="83ad6-179">Use of IHtmlHelper.Partial may result in application deadlocks.</span></span> <span data-ttu-id="83ad6-180">Erwägen Sie die Verwendung des Hilfsprogramms für &lt;Teiltags&gt; oder von IHtmlHelper.PartialAsync.</span><span class="sxs-lookup"><span data-stu-id="83ad6-180">Consider using &lt;partial&gt; Tag Helper or IHtmlHelper.PartialAsync.</span></span>

<span data-ttu-id="83ad6-181">Ersetzen Sie Aufrufe von `@Html.Partial` durch `@await Html.PartialAsync` oder durch das [Hilfsprogramm für Teiltags](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="83ad6-181">Replace calls to `@Html.Partial` with `@await Html.PartialAsync` or the [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper).</span></span> <span data-ttu-id="83ad6-182">Weiter Informationen zur Migration des Teiltaghilfsprogramms finden Sie unter [Migrate from an HTML Helper (Migration von einem HTML-Hilfsprogramm)](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper)</span><span class="sxs-lookup"><span data-stu-id="83ad6-182">For more information on Partial Tag Helper migration, see [Migrate from an HTML Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper#migrate-from-an-html-helper).</span></span>

::: moniker-end

## <a name="partial-view-discovery"></a><span data-ttu-id="83ad6-183">Ermittlung von Teilansichten</span><span class="sxs-lookup"><span data-stu-id="83ad6-183">Partial view discovery</span></span>

<span data-ttu-id="83ad6-184">Wenn auf eine Teilansicht anhand des Namens ohne eine Dateierweiterung verwiesen wird, werden die folgenden Speicherorte in der angegebenen Reihenfolge durchsucht:</span><span class="sxs-lookup"><span data-stu-id="83ad6-184">When a partial view is referenced by name without a file extension, the following locations are searched in the stated order:</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="83ad6-185">**Razor Pages**</span><span class="sxs-lookup"><span data-stu-id="83ad6-185">**Razor Pages**</span></span>

1. <span data-ttu-id="83ad6-186">Der Ordner der aktuell ausgeführten Seite</span><span class="sxs-lookup"><span data-stu-id="83ad6-186">Currently executing page's folder</span></span>
1. <span data-ttu-id="83ad6-187">Der Verzeichnisgraph über dem Ordner der Seite</span><span class="sxs-lookup"><span data-stu-id="83ad6-187">Directory graph above the page's folder</span></span>
1. `/Shared`
1. `/Pages/Shared`
1. `/Views/Shared`

<span data-ttu-id="83ad6-188">**MVC**</span><span class="sxs-lookup"><span data-stu-id="83ad6-188">**MVC**</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`
1. `/Pages/Shared`

::: moniker-end

::: moniker range="< aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`

::: moniker-end

<span data-ttu-id="83ad6-189">Für die Ermittlung von Teilansichten gelten die folgenden Konventionen:</span><span class="sxs-lookup"><span data-stu-id="83ad6-189">The following conventions apply to partial view discovery:</span></span>

* <span data-ttu-id="83ad6-190">Unterschiedliche Teilansichten mit dem gleichen Dateinamen sind zulässig, wenn sich die Teilansichten in verschiedenen Ordnern befinden.</span><span class="sxs-lookup"><span data-stu-id="83ad6-190">Different partial views with the same file name are allowed when the partial views are in different folders.</span></span>
* <span data-ttu-id="83ad6-191">Wenn auf eine Teilansicht über den Namen ohne Dateierweiterung verwiesen wird und die Teilansicht sowohl im Ordner des Aufrufers als auch im Ordner *Shared* vorhanden ist, stellt die Teilansicht im Ordner des Aufrufers die Teilansicht bereit.</span><span class="sxs-lookup"><span data-stu-id="83ad6-191">When referencing a partial view by name without a file extension and the partial view is present in both the caller's folder and the *Shared* folder, the partial view in the caller's folder supplies the partial view.</span></span> <span data-ttu-id="83ad6-192">Wenn die Teilansicht nicht im Ordner des Aufrufers vorhanden ist, wird die Teilansicht aus dem Ordner *Shared* bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="83ad6-192">If the partial view isn't present in the caller's folder, the partial view is provided from the *Shared* folder.</span></span> <span data-ttu-id="83ad6-193">Teilansichten im Ordner *Shared* werden als *Freigegebene Teilansichten* oder *Standardteilansichten* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="83ad6-193">Partial views in the *Shared* folder are called *shared partial views* or *default partial views*.</span></span>
* <span data-ttu-id="83ad6-194">Teilansichten können *verkettet* sein: Eine Teilansicht kann eine andere Teilansicht aufrufen, wenn durch den Aufruf kein Zirkelbezug entsteht.</span><span class="sxs-lookup"><span data-stu-id="83ad6-194">Partial views can be *chained*&mdash;a partial view can call another partial view if a circular reference isn't formed by the calls.</span></span> <span data-ttu-id="83ad6-195">Relative Pfade sind immer relativ zur aktuellen Datei, nicht zum Stamm- oder übergeordneten Verzeichnis der Datei.</span><span class="sxs-lookup"><span data-stu-id="83ad6-195">Relative paths are always relative to the current file, not to the root or parent of the file.</span></span>

> [!NOTE]
> <span data-ttu-id="83ad6-196">Ein [Razor](xref:mvc/views/razor) `section`, der in einer Teilansicht definiert ist, ist für übergeordnete Markupdateien nicht sichtbar.</span><span class="sxs-lookup"><span data-stu-id="83ad6-196">A [Razor](xref:mvc/views/razor) `section` defined in a partial view is invisible to parent markup files.</span></span> <span data-ttu-id="83ad6-197">Die `section`-Anweisung ist nur für die Teilansicht, in der sie definiert ist, sichtbar.</span><span class="sxs-lookup"><span data-stu-id="83ad6-197">The `section` is only visible to the partial view in which it's defined.</span></span>

## <a name="access-data-from-partial-views"></a><span data-ttu-id="83ad6-198">Zugriff auf Daten aus Teilansichten</span><span class="sxs-lookup"><span data-stu-id="83ad6-198">Access data from partial views</span></span>

<span data-ttu-id="83ad6-199">Wenn eine Teilansicht instanziiert wird, erhält sie eine *Kopie* des `ViewData`-Wörterbuchs der übergeordneten Ansicht.</span><span class="sxs-lookup"><span data-stu-id="83ad6-199">When a partial view is instantiated, it receives a *copy* of the parent's `ViewData` dictionary.</span></span> <span data-ttu-id="83ad6-200">An den Daten vorgenommene Änderungen in der Teilansicht werden in der übergeordneten Ansicht nicht beibehalten.</span><span class="sxs-lookup"><span data-stu-id="83ad6-200">Updates made to the data within the partial view aren't persisted to the parent view.</span></span> <span data-ttu-id="83ad6-201">Ein `ViewData`-Wörterbuch, das in einer Teilansicht verändert wird, geht verloren, wenn die Teilansicht eine Rückgabe ausgibt.</span><span class="sxs-lookup"><span data-stu-id="83ad6-201">`ViewData` changes in a partial view are lost when the partial view returns.</span></span>

<span data-ttu-id="83ad6-202">Das folgende Beispiel zeigt, wie eine Instanz von [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) an eine Teilansicht übergeben wird:</span><span class="sxs-lookup"><span data-stu-id="83ad6-202">The following example demonstrates how to pass an instance of [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) to a partial view:</span></span>

```cshtml
@await Html.PartialAsync("_PartialName", customViewData)
```

<span data-ttu-id="83ad6-203">Außerdem können Sie ein Modell an eine Teilansicht übergeben.</span><span class="sxs-lookup"><span data-stu-id="83ad6-203">You can pass a model into a partial view.</span></span> <span data-ttu-id="83ad6-204">Das Modell kann ein benutzerdefiniertes Objekt sein.</span><span class="sxs-lookup"><span data-stu-id="83ad6-204">The model can be a custom object.</span></span> <span data-ttu-id="83ad6-205">Sie können ein Modell mit `PartialAsync` (rendert einen Inhaltsblock für den Aufrufer) oder `RenderPartialAsync` (streamt den Inhalt in die Ausgabe) übergeben:</span><span class="sxs-lookup"><span data-stu-id="83ad6-205">You can pass a model with `PartialAsync` (renders a block of content to the caller) or `RenderPartialAsync` (streams the content to the output):</span></span>

```cshtml
@await Html.PartialAsync("_PartialName", model)
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="83ad6-206">**Razor Pages**</span><span class="sxs-lookup"><span data-stu-id="83ad6-206">**Razor Pages**</span></span>

<span data-ttu-id="83ad6-207">Das folgende Markup in der Beispiel-App stammt von der Seite *Pages/ArticlesRP/ReadRP.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="83ad6-207">The following markup in the sample app is from the *Pages/ArticlesRP/ReadRP.cshtml* page.</span></span> <span data-ttu-id="83ad6-208">Diese Seite enthält zwei Teilansichten.</span><span class="sxs-lookup"><span data-stu-id="83ad6-208">The page contains two partial views.</span></span> <span data-ttu-id="83ad6-209">Die zweite Teilansicht übergibt ein Modell und `ViewData` an die Teilansicht.</span><span class="sxs-lookup"><span data-stu-id="83ad6-209">The second partial view passes in a model and `ViewData` to the partial view.</span></span> <span data-ttu-id="83ad6-210">Die `ViewDataDictionary`-Konstruktorüberladung wird verwendet, um ein neues `ViewData`-Wörterbuch zu übergeben, während das vorhandene `ViewData`-Wörterbuch beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="83ad6-210">The `ViewDataDictionary` constructor overload is used to pass a new `ViewData` dictionary while retaining the existing `ViewData` dictionary.</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/ReadRP.cshtml?name=snippet_ReadPartialViewRP&highlight=5,15-19)]

<span data-ttu-id="83ad6-211">*Pages/Shared/_AuthorPartialRP.cshtml* ist die erste Teilansicht, auf die durch die Markupdatei *ReadRP.cshtml* verwiesen wird:</span><span class="sxs-lookup"><span data-stu-id="83ad6-211">*Pages/Shared/_AuthorPartialRP.cshtml* is the first partial view referenced by the *ReadRP.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/Shared/_AuthorPartialRP.cshtml)]

<span data-ttu-id="83ad6-212">*Pages/ArticlesRP/_ArticleSectionRP.cshtml* ist die zweite Teilansicht, auf die durch die Markupdatei *ReadRP.cshtml* verwiesen wird:</span><span class="sxs-lookup"><span data-stu-id="83ad6-212">*Pages/ArticlesRP/_ArticleSectionRP.cshtml* is the second partial view referenced by the *ReadRP.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Pages/ArticlesRP/_ArticleSectionRP.cshtml)]

<span data-ttu-id="83ad6-213">**MVC**</span><span class="sxs-lookup"><span data-stu-id="83ad6-213">**MVC**</span></span>

::: moniker-end

<span data-ttu-id="83ad6-214">Das folgende Markup in der Beispiel-App zeigt die Ansicht *Views/Articles/Read.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="83ad6-214">The following markup in the sample app shows the *Views/Articles/Read.cshtml* view.</span></span> <span data-ttu-id="83ad6-215">Die Ansicht enthält die zwei Teilansichten.</span><span class="sxs-lookup"><span data-stu-id="83ad6-215">The view contains two partial views.</span></span> <span data-ttu-id="83ad6-216">Die zweite Teilansicht übergibt ein Modell und `ViewData` an die Teilansicht.</span><span class="sxs-lookup"><span data-stu-id="83ad6-216">The second partial view passes in a model and `ViewData` to the partial view.</span></span> <span data-ttu-id="83ad6-217">Die `ViewDataDictionary`-Konstruktorüberladung wird verwendet, um ein neues `ViewData`-Wörterbuch zu übergeben, während das vorhandene `ViewData`-Wörterbuch beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="83ad6-217">The `ViewDataDictionary` constructor overload is used to pass a new `ViewData` dictionary while retaining the existing `ViewData` dictionary.</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/Read.cshtml?name=snippet_ReadPartialView&highlight=5,15-19)]

<span data-ttu-id="83ad6-218">*Views/Shared/_AuthorPartial.cshtml* ist die erste Teilansicht, auf die durch die Markupdatei *ReadRP.cshtml* verwiesen wird:</span><span class="sxs-lookup"><span data-stu-id="83ad6-218">*Views/Shared/_AuthorPartial.cshtml* is the first partial view referenced by the *ReadRP.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Shared/_AuthorPartial.cshtml)]

<span data-ttu-id="83ad6-219">*Views/Articles/_ArticleSection.cshtml* ist die zweite Teilansicht, auf die durch die Markupdatei *ReadRP.cshtml* verwiesen wird:</span><span class="sxs-lookup"><span data-stu-id="83ad6-219">*Views/Articles/_ArticleSection.cshtml* is the second partial view referenced by the *Read.cshtml* markup file:</span></span>

[!code-cshtml[](partial/sample/PartialViewsSample/Views/Articles/_ArticleSection.cshtml)]

<span data-ttu-id="83ad6-220">Die Teilansichten werden zur Laufzeit in der gerenderten Ausgabe der übergeordneten Markupdatei gerendert, die wiederum in der freigegebenen Datei *_Layout.cshtml* gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="83ad6-220">At runtime, the partials are rendered into the parent markup file's rendered output, which itself is rendered within the shared *_Layout.cshtml*.</span></span> <span data-ttu-id="83ad6-221">Die erste Teilansicht rendert den Namen des Artikelautors und das Erscheinungsdatum:</span><span class="sxs-lookup"><span data-stu-id="83ad6-221">The first partial view renders the article author's name and publication date:</span></span>

> <span data-ttu-id="83ad6-222">Abraham Lincoln</span><span class="sxs-lookup"><span data-stu-id="83ad6-222">Abraham Lincoln</span></span>
>
> <span data-ttu-id="83ad6-223">Diese Teilansicht aus dem &lt;Dateipfad der freigegebenen Teilansicht&gt;.</span><span class="sxs-lookup"><span data-stu-id="83ad6-223">This partial view from &lt;shared partial view file path&gt;.</span></span>
> <span data-ttu-id="83ad6-224">11/19/1863 12:00:00 AM</span><span class="sxs-lookup"><span data-stu-id="83ad6-224">11/19/1863 12:00:00 AM</span></span>

<span data-ttu-id="83ad6-225">Die zweite Teilansicht rendert die Abschnitte des Artikels:</span><span class="sxs-lookup"><span data-stu-id="83ad6-225">The second partial view renders the article's sections:</span></span>

> <span data-ttu-id="83ad6-226">Section One Index: 0</span><span class="sxs-lookup"><span data-stu-id="83ad6-226">Section One Index: 0</span></span>
>
> <span data-ttu-id="83ad6-227">Four score and seven years ago ...</span><span class="sxs-lookup"><span data-stu-id="83ad6-227">Four score and seven years ago ...</span></span>
>
> <span data-ttu-id="83ad6-228">Section Two Index: 1</span><span class="sxs-lookup"><span data-stu-id="83ad6-228">Section Two Index: 1</span></span>
>
> <span data-ttu-id="83ad6-229">Now we are engaged in a great civil war, testing ...</span><span class="sxs-lookup"><span data-stu-id="83ad6-229">Now we are engaged in a great civil war, testing ...</span></span>
>
> <span data-ttu-id="83ad6-230">Section Three Index: 2</span><span class="sxs-lookup"><span data-stu-id="83ad6-230">Section Three Index: 2</span></span>
>
> <span data-ttu-id="83ad6-231">But, in a larger sense, we can not dedicate ...</span><span class="sxs-lookup"><span data-stu-id="83ad6-231">But, in a larger sense, we can not dedicate ...</span></span>

## <a name="additional-resources"></a><span data-ttu-id="83ad6-232">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="83ad6-232">Additional resources</span></span>

::: moniker range=">= aspnetcore-2.1"

* <xref:mvc/views/razor>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end

::: moniker range="< aspnetcore-2.1"

* <xref:mvc/views/razor>
* <xref:mvc/views/view-components>
* <xref:mvc/controllers/areas>

::: moniker-end
