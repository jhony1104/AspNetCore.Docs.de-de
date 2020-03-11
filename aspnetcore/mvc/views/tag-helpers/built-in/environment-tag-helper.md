---
title: Umgebungstaghilfsprogramm in ASP.NET Core
author: pkellner
description: Definition des Umgebungstaghilfsprogramms für ASP.NET Core, einschließlich aller Eigenschaften
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: 308e7db47104ebd4d6bb8d08c64f14bbd118898b
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653767"
---
# <a name="environment-tag-helper-in-aspnet-core"></a><span data-ttu-id="654b1-103">Umgebungstaghilfsprogramm in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="654b1-103">Environment Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="654b1-104">Von [Peter Kellner](https://peterkellner.net) und [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="654b1-104">By [Peter Kellner](https://peterkellner.net) and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="654b1-105">Basierend auf der aktuellen [Hostingumgebung](xref:fundamentals/environments) rendert das Environment-Taghilfsprogramm den von ihm eingeschlossenen Inhalt unter Vorbehalt.</span><span class="sxs-lookup"><span data-stu-id="654b1-105">The Environment Tag Helper conditionally renders its enclosed content based on the current [hosting environment](xref:fundamentals/environments).</span></span> <span data-ttu-id="654b1-106">Das einzelne Attribut `names` des Environment-Taghilfsprogramm ist eine durch Trennzeichen getrennte Liste von Umgebungsnamen.</span><span class="sxs-lookup"><span data-stu-id="654b1-106">The Environment Tag Helper's single attribute, `names`, is a comma-separated list of environment names.</span></span> <span data-ttu-id="654b1-107">Wenn die Namen der bereitgestellten Umgebung der aktuellen Umgebung entsprechen, wird der eingeschlossene Inhalt gerendert.</span><span class="sxs-lookup"><span data-stu-id="654b1-107">If any of the provided environment names match the current environment, the enclosed content is rendered.</span></span>

<span data-ttu-id="654b1-108">Eine Übersicht der Taghilfsprogramme finden Sie unter <xref:mvc/views/tag-helpers/intro>.</span><span class="sxs-lookup"><span data-stu-id="654b1-108">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

## <a name="environment-tag-helper-attributes"></a><span data-ttu-id="654b1-109">Attribute von Umgebungstaghilfsprogrammen</span><span class="sxs-lookup"><span data-stu-id="654b1-109">Environment Tag Helper Attributes</span></span>

### <a name="names"></a><span data-ttu-id="654b1-110">Namen</span><span class="sxs-lookup"><span data-stu-id="654b1-110">names</span></span>

<span data-ttu-id="654b1-111">`names` akzeptiert den Namen einer einzelnen Hostingumgebung oder eine durch Trennzeichen getrennte Liste mit Namen von Hostingumgebungen, die das Rendering des eingeschlossenen Inhalts auslösen.</span><span class="sxs-lookup"><span data-stu-id="654b1-111">`names` accepts a single hosting environment name or a comma-separated list of hosting environment names that trigger the rendering of the enclosed content.</span></span>

<span data-ttu-id="654b1-112">Umgebungswerte werden im Vergleich zum aktuellen Wert von [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*) zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="654b1-112">Environment values are compared to the current value returned by [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="654b1-113">Bei dem Vergleich wird die Groß-/Kleinschreibung ignoriert.</span><span class="sxs-lookup"><span data-stu-id="654b1-113">The comparison ignores case.</span></span>

<span data-ttu-id="654b1-114">Im folgenden Beispiel wird ein Environment-Taghilfsprogramm verwendet:</span><span class="sxs-lookup"><span data-stu-id="654b1-114">The following example uses an Environment Tag Helper.</span></span> <span data-ttu-id="654b1-115">Der Inhalt wird wiedergegeben, wenn es sich bei der Hostumgebung um eine Staging- oder Produktionsumgebung handelt:</span><span class="sxs-lookup"><span data-stu-id="654b1-115">The content is rendered if the hosting environment is Staging or Production:</span></span>

```cshtml
<environment names="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a><span data-ttu-id="654b1-116">Die Attribute „include“ und „exclude“</span><span class="sxs-lookup"><span data-stu-id="654b1-116">include and exclude attributes</span></span>

<span data-ttu-id="654b1-117">`include` & `exclude` Attribute das Rendern des eingeschlossenen Inhalts basierend auf den eingeschlossenen oder ausgeschlossenen hostingumgebungsnamen.</span><span class="sxs-lookup"><span data-stu-id="654b1-117">`include` & `exclude` attributes control rendering the enclosed content based on the included or excluded hosting environment names.</span></span>

### <a name="include"></a><span data-ttu-id="654b1-118">include</span><span class="sxs-lookup"><span data-stu-id="654b1-118">include</span></span>

<span data-ttu-id="654b1-119">Die `include`-Eigenschaft zeigt ein ähnliches Verhalten wie das Attribut `names`.</span><span class="sxs-lookup"><span data-stu-id="654b1-119">The `include` property exhibits similar behavior to the `names` attribute.</span></span> <span data-ttu-id="654b1-120">Eine im Attributwert `include` aufgeführte Umgebung muss der App-Hostingumgebung ([IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) entsprechen, damit der Inhalt des Tags `<environment>` gerendert werden kann.</span><span class="sxs-lookup"><span data-stu-id="654b1-120">An environment listed in the `include` attribute value must match the app's hosting environment ([IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) to render the content of the `<environment>` tag.</span></span>

```cshtml
<environment include="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a><span data-ttu-id="654b1-121">ausschließen</span><span class="sxs-lookup"><span data-stu-id="654b1-121">exclude</span></span>

<span data-ttu-id="654b1-122">Im Gegensatz zum Attribut `include` wird der Inhalt des `<environment>`-Tags gerendert, wenn die Hostingumgebung nicht einer Umgebung entspricht, die im Attributwert `exclude` aufgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="654b1-122">In contrast to the `include` attribute, the content of the `<environment>` tag is rendered when the hosting environment doesn't match an environment listed in the `exclude` attribute value.</span></span>

```cshtml
<environment exclude="Development">
    <strong>HostingEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="654b1-123">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="654b1-123">Additional resources</span></span>

* <xref:fundamentals/environments>
