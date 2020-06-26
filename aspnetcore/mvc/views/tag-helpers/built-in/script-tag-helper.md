---
title: Skript-Taghilfsprogramm in ASP.NET Core
author: rick-anderson
ms.author: riande
description: Lernen Sie die Attribute für das ASP.NET Core-Skript-Taghilfsprogramm kennen, und erfahren Sie, welche Rolle jedes Attribut bei der Erweiterung des Verhaltens des HTML-Skripttags spielt.
ms.custom: mvc
ms.date: 12/02/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/script-tag-helper
ms.openlocfilehash: b9b90c1c40fccbc7bb6b6c9050bd525b5fa8cd92
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85407407"
---
# <a name="script-tag-helper-in-aspnet-core"></a><span data-ttu-id="2eb65-103">Skript-Taghilfsprogramm in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2eb65-103">Script Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="2eb65-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2eb65-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2eb65-105">Das [Skript-Taghilfsprogramm](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) generiert einen Link zu einer primären oder Fallback-Skriptdatei.</span><span class="sxs-lookup"><span data-stu-id="2eb65-105">The [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) generates a link to a primary or fall back script file.</span></span> <span data-ttu-id="2eb65-106">Die primäre Skriptdatei befindet sich in der Regel in einem [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span><span class="sxs-lookup"><span data-stu-id="2eb65-106">Typically the primary script file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="2eb65-107">Mit dem Skript-Taghilfsprogramm können Sie ein CDN für die Skriptdatei und eine Fallbackquelle angeben, wenn das CDN nicht verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="2eb65-107">The Script Tag Helper allows you to specify a CDN for the script file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="2eb65-108">Das Skript-Taghilfsprogramm bietet den Leistungsvorteil eines CDN kombiniert mit der Stabilität des lokalen Hostings.</span><span class="sxs-lookup"><span data-stu-id="2eb65-108">The Script Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="2eb65-109">Das folgende Razor Markup zeigt ein- `script` Element mit einem Fallback:</span><span class="sxs-lookup"><span data-stu-id="2eb65-109">The following Razor markup shows a `script` element with a fallback:</span></span>

```html
<script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-3.3.1.min.js"
        asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
        asp-fallback-test="window.jQuery"
        crossorigin="anonymous"
        integrity="sha384-tsQFqpEReu7ZLhBV2VZlAu7zcOV+rXbYlF2cqB8txI/8aZajjp4Bqd+V6D5IgvKT">
</script>
```

<span data-ttu-id="2eb65-110">Verwenden Sie nicht das [defer](https://developer.mozilla.org/docs/Web/HTML/Element/script)-Attribut des `<script>`-Elements, um das Laden des CDN-Skripts auszusetzen.</span><span class="sxs-lookup"><span data-stu-id="2eb65-110">Don't use the `<script>` element's [defer](https://developer.mozilla.org/docs/Web/HTML/Element/script) attribute to defer loading the CDN script.</span></span> <span data-ttu-id="2eb65-111">Das Hilfsprogramm für Skripttags rendert JavaScript-Code, der sofort den Ausdruck [asp-fallback-test](#asp-fallback-test) ausführt.</span><span class="sxs-lookup"><span data-stu-id="2eb65-111">The Script Tag Helper renders JavaScript that immediately executes the [asp-fallback-test](#asp-fallback-test) expression.</span></span> <span data-ttu-id="2eb65-112">Der Ausdruck schlägt fehl, wenn das Laden des CDN-Skripts ausgesetzt wird.</span><span class="sxs-lookup"><span data-stu-id="2eb65-112">The expression fails if loading the CDN script is deferred.</span></span>

## <a name="commonly-used-script-tag-helper-attributes"></a><span data-ttu-id="2eb65-113">Häufig verwendete Attribute des Skript-Taghilfsprogramms</span><span class="sxs-lookup"><span data-stu-id="2eb65-113">Commonly used Script Tag Helper attributes</span></span>

<span data-ttu-id="2eb65-114">Sie finden alle Attribute, Eigenschaften und Methoden des Skript-Taghilfsprogramms in der Referenz zur [ScriptTagHelper-Klasse](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper).</span><span class="sxs-lookup"><span data-stu-id="2eb65-114">See [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) for all the Script Tag Helper attributes, properties, and methods.</span></span>

### <a name="asp-fallback-test"></a><span data-ttu-id="2eb65-115">asp-fallback-test</span><span class="sxs-lookup"><span data-stu-id="2eb65-115">asp-fallback-test</span></span>

<span data-ttu-id="2eb65-116">Die im primären Skript definierte Skriptmethode, die für den Fallbacktest verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="2eb65-116">The script method defined in the primary script to use for the fallback test.</span></span> <span data-ttu-id="2eb65-117">Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>.</span><span class="sxs-lookup"><span data-stu-id="2eb65-117">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>.</span></span>

### <a name="asp-fallback-src"></a><span data-ttu-id="2eb65-118">asp-fallback-src</span><span class="sxs-lookup"><span data-stu-id="2eb65-118">asp-fallback-src</span></span>

<span data-ttu-id="2eb65-119">Die URL eines Skripttags, das als Fallback verwendet wird, falls die primäre URL nicht verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="2eb65-119">The URL of a Script tag to fallback to in the case the primary one fails.</span></span> <span data-ttu-id="2eb65-120">Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>.</span><span class="sxs-lookup"><span data-stu-id="2eb65-120">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2eb65-121">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="2eb65-121">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
