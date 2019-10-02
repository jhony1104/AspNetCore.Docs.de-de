---
title: Link-Taghilfsprogramm in ASP.NET Core
author: rick-anderson
ms.author: riande
description: Lernen Sie die Attribute für das ASP.NET Core-Link-Taghilfsprogramm kennen, und erfahren Sie, welche Rolle jedes Attribut bei der Erweiterung des Verhaltens des HTML-Linktags spielt.
ms.custom: mvc
ms.date: 09/24/2019
uid: mvc/views/tag-helpers/builtin-th/link-tag-helper
ms.openlocfilehash: e1e2e58b4ab9087e1f9de5b5c03b587feb88f1b9
ms.sourcegitcommit: fae6f0e253f9d62d8f39de5884d2ba2b4b2a6050
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71256483"
---
# <a name="link-tag-helper-in-aspnet-core"></a><span data-ttu-id="da70b-103">Link-Taghilfsprogramm in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="da70b-103">Link Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="da70b-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="da70b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="da70b-105">Das [Link-Taghilfsprogramm](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) generiert einen Link zu einer primären oder Fallback-CSS-Datei.</span><span class="sxs-lookup"><span data-stu-id="da70b-105">The [Link Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) generates a link to a primary or fall back CSS file.</span></span> <span data-ttu-id="da70b-106">Die primäre CSS-Datei befindet sich in der Regel in einem [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span><span class="sxs-lookup"><span data-stu-id="da70b-106">Typically the primary CSS file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="da70b-107">Mit dem Link-Taghilfsprogramm können Sie ein CDN für die CSS-Datei und eine Fallbackquelle angeben, wenn das CDN nicht verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="da70b-107">The Link Tag Helper allows you to specify a CDN for the CSS file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="da70b-108">Das Link-Taghilfsprogramm bietet den Leistungsvorteil eines CDN kombiniert mit der Stabilität des lokalen Hostings.</span><span class="sxs-lookup"><span data-stu-id="da70b-108">The Link Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="da70b-109">Das folgende Razor-Markup zeigt das `head`-Element einer Layoutdatei, die mit der Web-App-Vorlage von ASP.NET Core erstellt wurde:</span><span class="sxs-lookup"><span data-stu-id="da70b-109">The following Razor markup shows the `head` element of a layout file created with the ASP.NET Core web app template:</span></span>

[!code-html[](link-tag-helper/sample/_Layout.cshtml?name=snippet)]

<span data-ttu-id="da70b-110">Das folgende Beispiel zeigt den gerenderten HTML-Code aus dem vorherigen Beispiel (in einer nicht zur Entwicklung dienenden Umgebung):</span><span class="sxs-lookup"><span data-stu-id="da70b-110">The following is rendered HTML from the preceding code (in a non-Development environment):</span></span>

[!code-csharp[](link-tag-helper/sample/HtmlPage1.html)]

<span data-ttu-id="da70b-111">Im vorherigen Beispiel hat das Link-Taghilfsprogramm das `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />`-Element und den folgenden JavaScript-Code generiert, mit dem überprüft wird, ob die angeforderte Datei *bootstrap.min.css* im CDN verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="da70b-111">In the preceding code, the Link Tag Helper generated the `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />` element and the following JavaScript which is used to verify the requested *bootstrap.min.css* file is available on the CDN.</span></span> <span data-ttu-id="da70b-112">In diesem Fall war die CSS-Datei verfügbar, sodass das Taghilfsprogramm das `<link />`-Element mit der CSS-Datei aus dem CDN generiert hat.</span><span class="sxs-lookup"><span data-stu-id="da70b-112">In this case, the CSS file was available so the Tag Helper generated the `<link />` element with the CDN CSS file.</span></span>

## <a name="commonly-used-link-tag-helper-attributes"></a><span data-ttu-id="da70b-113">Häufig verwendete Attribute des Link-Taghilfsprogramms</span><span class="sxs-lookup"><span data-stu-id="da70b-113">Commonly used Link Tag Helper attributes</span></span>

<span data-ttu-id="da70b-114">Sie finden alle Attribute, Eigenschaften und Methoden des Link-Taghilfsprogramms in der Referenz zur [LinkTagHelper-Klasse](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper).</span><span class="sxs-lookup"><span data-stu-id="da70b-114">See [Link Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper)  for all the Link Tag Helper attributes, properties, and methods.</span></span>

### <a name="href"></a><span data-ttu-id="da70b-115">href</span><span class="sxs-lookup"><span data-stu-id="da70b-115">href</span></span>

<span data-ttu-id="da70b-116">Die bevorzugte Adresse der verknüpften Ressource.</span><span class="sxs-lookup"><span data-stu-id="da70b-116">Preferred address of the linked resource.</span></span> <span data-ttu-id="da70b-117">Die Adresse wird in allen Fällen an den generierten HTML-Code übergeben.</span><span class="sxs-lookup"><span data-stu-id="da70b-117">The address is passed thought to the generated HTML in all cases.</span></span>

### <a name="asp-fallback-href"></a><span data-ttu-id="da70b-118">asp-fallback-href</span><span class="sxs-lookup"><span data-stu-id="da70b-118">asp-fallback-href</span></span>

<span data-ttu-id="da70b-119">Die URL eines Cascading Stylesheet (CSS), das als Fallback verwendet wird, falls die primäre URL nicht verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="da70b-119">The URL of a CSS stylesheet to fallback to in the case the primary URL fails.</span></span>

### <a name="asp-fallback-test-class"></a><span data-ttu-id="da70b-120">asp-fallback-test-class</span><span class="sxs-lookup"><span data-stu-id="da70b-120">asp-fallback-test-class</span></span>

<span data-ttu-id="da70b-121">Der in der Formatvorlage definierte Klassenname, der zum Fallbacktest verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="da70b-121">The class name defined in the stylesheet to use for the fallback test.</span></span> <span data-ttu-id="da70b-122">Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span><span class="sxs-lookup"><span data-stu-id="da70b-122">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span></span>

### <a name="asp-fallback-test-property"></a><span data-ttu-id="da70b-123">asp-fallback-test-property</span><span class="sxs-lookup"><span data-stu-id="da70b-123">asp-fallback-test-property</span></span>

<span data-ttu-id="da70b-124">Der CSS-Eigenschaftsname, der für den Fallbacktest verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="da70b-124">The CSS property name to use for the fallback test.</span></span> <span data-ttu-id="da70b-125">Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span><span class="sxs-lookup"><span data-stu-id="da70b-125">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span></span>

### <a name="asp-fallback-test-value"></a><span data-ttu-id="da70b-126">asp-fallback-test-value</span><span class="sxs-lookup"><span data-stu-id="da70b-126">asp-fallback-test-value</span></span>

<span data-ttu-id="da70b-127">Der CSS-Eigenschaftswert, der für den Fallbacktest verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="da70b-127">The CSS property value to use for the fallback test.</span></span> <span data-ttu-id="da70b-128">Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span><span class="sxs-lookup"><span data-stu-id="da70b-128">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span></span>

### <a name="asp-fallback-test-value"></a><span data-ttu-id="da70b-129">asp-fallback-test-value</span><span class="sxs-lookup"><span data-stu-id="da70b-129">asp-fallback-test-value</span></span>

<span data-ttu-id="da70b-130">Der CSS-Eigenschaftswert, der für den Fallbacktest verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="da70b-130">The CSS property value to use for the fallback test.</span></span> <span data-ttu-id="da70b-131">Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue></span><span class="sxs-lookup"><span data-stu-id="da70b-131">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue></span></span>

## <a name="additional-resources"></a><span data-ttu-id="da70b-132">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="da70b-132">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
