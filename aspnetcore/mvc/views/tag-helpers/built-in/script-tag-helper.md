---
title: Skript-Taghilfsprogramm in ASP.NET Core
author: rick-anderson
ms.author: riande
description: Lernen Sie die Attribute für das ASP.NET Core-Skript-Taghilfsprogramm kennen, und erfahren Sie, welche Rolle jedes Attribut bei der Erweiterung des Verhaltens des HTML-Skripttags spielt.
ms.custom: mvc
ms.date: 12/18/2018
uid: mvc/views/tag-helpers/builtin-th/script-tag-helper
ms.openlocfilehash: 5f2fb8a45048804afa8aff2989cd53489e45a33b
ms.sourcegitcommit: fae6f0e253f9d62d8f39de5884d2ba2b4b2a6050
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71256465"
---
# <a name="script-tag-helper-in-aspnet-core"></a><span data-ttu-id="216e6-103">Skript-Taghilfsprogramm in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="216e6-103">Script Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="216e6-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="216e6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="216e6-105">Das [Skript-Taghilfsprogramm](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) generiert einen Link zu einer primären oder Fallback-Skriptdatei.</span><span class="sxs-lookup"><span data-stu-id="216e6-105">The [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) generates a link to a primary or fall back script file.</span></span> <span data-ttu-id="216e6-106">Die primäre Skriptdatei befindet sich in der Regel in einem [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span><span class="sxs-lookup"><span data-stu-id="216e6-106">Typically the primary script file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="216e6-107">Mit dem Skript-Taghilfsprogramm können Sie ein CDN für die Skriptdatei und eine Fallbackquelle angeben, wenn das CDN nicht verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="216e6-107">The Script Tag Helper allows you to specify a CDN for the script file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="216e6-108">Das Skript-Taghilfsprogramm bietet den Leistungsvorteil eines CDN kombiniert mit der Stabilität des lokalen Hostings.</span><span class="sxs-lookup"><span data-stu-id="216e6-108">The Script Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="216e6-109">Das folgende Razor-Markup zeigt das `script`-Element einer Layoutdatei, die mit der Web-App-Vorlage von ASP.NET Core erstellt wurde:</span><span class="sxs-lookup"><span data-stu-id="216e6-109">The following Razor markup shows the `script` element of a layout file created with the ASP.NET Core web app template:</span></span>

[!code-html[](link-tag-helper/sample/_Layout.cshtml?name=snippet2)]

<span data-ttu-id="216e6-110">Der folgende Code ähnelt dem gerenderten HTML-Code im vorherigen Beispiel (in einer nicht zur Entwicklung dienenden Umgebung):</span><span class="sxs-lookup"><span data-stu-id="216e6-110">The following is similar to the rendered HTML from the preceding code (in a non-Development environment):</span></span>

[!code-csharp[](link-tag-helper/sample/HtmlPage2.html)]

<span data-ttu-id="216e6-111">Im vorherigen Code generiert das Skript-Taghilfsprogramm das zweite Skriptelement (`<script>  (window.jQuery || document.write(`), das auf `window.jQuery` testet.</span><span class="sxs-lookup"><span data-stu-id="216e6-111">In the preceding code, the Script Tag Helper generated the second script ( `<script>  (window.jQuery || document.write(`) element, which tests for `window.jQuery`.</span></span> <span data-ttu-id="216e6-112">Wenn `window.jQuery` nicht gefunden wird, wird `document.write(` ausgeführt und erstellt ein Skript.</span><span class="sxs-lookup"><span data-stu-id="216e6-112">If `window.jQuery` is not found, `document.write(` runs and creates a script</span></span> 

## <a name="commonly-used-script-tag-helper-attributes"></a><span data-ttu-id="216e6-113">Häufig verwendete Attribute des Skript-Taghilfsprogramms</span><span class="sxs-lookup"><span data-stu-id="216e6-113">Commonly used Script Tag Helper attributes</span></span>

<span data-ttu-id="216e6-114">Sie finden alle Attribute, Eigenschaften und Methoden des Skript-Taghilfsprogramms in der Referenz zur [ScriptTagHelper-Klasse](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper).</span><span class="sxs-lookup"><span data-stu-id="216e6-114">See [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) for all the Script Tag Helper attributes, properties, and methods.</span></span>

### <a name="href"></a><span data-ttu-id="216e6-115">href</span><span class="sxs-lookup"><span data-stu-id="216e6-115">href</span></span>

<span data-ttu-id="216e6-116">Die bevorzugte Adresse der verknüpften Ressource.</span><span class="sxs-lookup"><span data-stu-id="216e6-116">Preferred address of the linked resource.</span></span> <span data-ttu-id="216e6-117">Die Adresse wird in allen Fällen an den generierten HTML-Code übergeben.</span><span class="sxs-lookup"><span data-stu-id="216e6-117">The address is passed thought to the generated HTML in all cases.</span></span>

### <a name="asp-fallback-href"></a><span data-ttu-id="216e6-118">asp-fallback-href</span><span class="sxs-lookup"><span data-stu-id="216e6-118">asp-fallback-href</span></span>

<span data-ttu-id="216e6-119">Die URL eines Cascading Stylesheet (CSS), das als Fallback verwendet wird, falls die primäre URL nicht verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="216e6-119">The URL of a CSS stylesheet to fallback to in the case the primary URL fails.</span></span>

### <a name="asp-fallback-test-class"></a><span data-ttu-id="216e6-120">asp-fallback-test-class</span><span class="sxs-lookup"><span data-stu-id="216e6-120">asp-fallback-test-class</span></span>

<span data-ttu-id="216e6-121">Der in der Formatvorlage definierte Klassenname, der zum Fallbacktest verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="216e6-121">The class name defined in the stylesheet to use for the fallback test.</span></span> <span data-ttu-id="216e6-122">Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span><span class="sxs-lookup"><span data-stu-id="216e6-122">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span></span>

### <a name="asp-fallback-test-property"></a><span data-ttu-id="216e6-123">asp-fallback-test-property</span><span class="sxs-lookup"><span data-stu-id="216e6-123">asp-fallback-test-property</span></span>

<span data-ttu-id="216e6-124">Der CSS-Eigenschaftsname, der für den Fallbacktest verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="216e6-124">The CSS property name to use for the fallback test.</span></span> <span data-ttu-id="216e6-125">Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span><span class="sxs-lookup"><span data-stu-id="216e6-125">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span></span>

### <a name="asp-fallback-test-value"></a><span data-ttu-id="216e6-126">asp-fallback-test-value</span><span class="sxs-lookup"><span data-stu-id="216e6-126">asp-fallback-test-value</span></span>

<span data-ttu-id="216e6-127">Der CSS-Eigenschaftswert, der für den Fallbacktest verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="216e6-127">The CSS property value to use for the fallback test.</span></span> <span data-ttu-id="216e6-128">Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span><span class="sxs-lookup"><span data-stu-id="216e6-128">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span></span>

### <a name="asp-fallback-test-value"></a><span data-ttu-id="216e6-129">asp-fallback-test-value</span><span class="sxs-lookup"><span data-stu-id="216e6-129">asp-fallback-test-value</span></span>

<span data-ttu-id="216e6-130">Der CSS-Eigenschaftswert, der für den Fallbacktest verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="216e6-130">The CSS property value to use for the fallback test.</span></span> <span data-ttu-id="216e6-131">Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue></span><span class="sxs-lookup"><span data-stu-id="216e6-131">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue></span></span>

## <a name="additional-resources"></a><span data-ttu-id="216e6-132">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="216e6-132">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
