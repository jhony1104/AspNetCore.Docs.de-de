---
title: Skript-Taghilfsprogramm in ASP.NET Core
author: rick-anderson
ms.author: riande
description: Lernen Sie die Attribute für das ASP.NET Core-Skript-Taghilfsprogramm kennen, und erfahren Sie, welche Rolle jedes Attribut bei der Erweiterung des Verhaltens des HTML-Skripttags spielt.
ms.custom: mvc
ms.date: 12/02/2019
uid: mvc/views/tag-helpers/builtin-th/script-tag-helper
ms.openlocfilehash: a037abb6a454e6d06305e7d7f6ecad0c2a0ca717
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652093"
---
# <a name="script-tag-helper-in-aspnet-core"></a>Skript-Taghilfsprogramm in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

Das [Skript-Taghilfsprogramm](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) generiert einen Link zu einer primären oder Fallback-Skriptdatei. Die primäre Skriptdatei befindet sich in der Regel in einem [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).

[!INCLUDE[](~/includes/cdn.md)]

Mit dem Skript-Taghilfsprogramm können Sie ein CDN für die Skriptdatei und eine Fallbackquelle angeben, wenn das CDN nicht verfügbar ist. Das Skript-Taghilfsprogramm bietet den Leistungsvorteil eines CDN kombiniert mit der Stabilität des lokalen Hostings.

Im folgenden Razor-Markup wird ein `script`-Element mit einem Fallback veranschaulicht:

```html
<script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-3.3.1.min.js"
        asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
        asp-fallback-test="window.jQuery"
        crossorigin="anonymous"
        integrity="sha384-tsQFqpEReu7ZLhBV2VZlAu7zcOV+rXbYlF2cqB8txI/8aZajjp4Bqd+V6D5IgvKT">
</script>
```

Verwenden Sie nicht das `<script>`defer[-Attribut des ](https://developer.mozilla.org/docs/Web/HTML/Element/script)-Elements, um das Laden des CDN-Skripts auszusetzen. Das Hilfsprogramm für Skripttags rendert JavaScript-Code, der sofort den Ausdruck [asp-fallback-test](#asp-fallback-test) ausführt. Der Ausdruck schlägt fehl, wenn das Laden des CDN-Skripts ausgesetzt wird.

## <a name="commonly-used-script-tag-helper-attributes"></a>Häufig verwendete Attribute des Skript-Taghilfsprogramms

Sie finden alle Attribute, Eigenschaften und Methoden des Skript-Taghilfsprogramms in der Referenz zur [ScriptTagHelper-Klasse](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper).

### <a name="asp-fallback-test"></a>asp-fallback-test

Die im primären Skript definierte Skriptmethode, die für den Fallbacktest verwendet wird. Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackTestExpression>.

### <a name="asp-fallback-src"></a>asp-fallback-src

Die URL eines Skripttags, das als Fallback verwendet wird, falls die primäre URL nicht verfügbar ist. Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper.FallbackSrc>.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
