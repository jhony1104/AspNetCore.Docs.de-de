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
# <a name="script-tag-helper-in-aspnet-core"></a>Skript-Taghilfsprogramm in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

Das [Skript-Taghilfsprogramm](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) generiert einen Link zu einer primären oder Fallback-Skriptdatei. Die primäre Skriptdatei befindet sich in der Regel in einem [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).

[!INCLUDE[](~/includes/cdn.md)]

Mit dem Skript-Taghilfsprogramm können Sie ein CDN für die Skriptdatei und eine Fallbackquelle angeben, wenn das CDN nicht verfügbar ist. Das Skript-Taghilfsprogramm bietet den Leistungsvorteil eines CDN kombiniert mit der Stabilität des lokalen Hostings.

Das folgende Razor-Markup zeigt das `script`-Element einer Layoutdatei, die mit der Web-App-Vorlage von ASP.NET Core erstellt wurde:

[!code-html[](link-tag-helper/sample/_Layout.cshtml?name=snippet2)]

Der folgende Code ähnelt dem gerenderten HTML-Code im vorherigen Beispiel (in einer nicht zur Entwicklung dienenden Umgebung):

[!code-csharp[](link-tag-helper/sample/HtmlPage2.html)]

Im vorherigen Code generiert das Skript-Taghilfsprogramm das zweite Skriptelement (`<script>  (window.jQuery || document.write(`), das auf `window.jQuery` testet. Wenn `window.jQuery` nicht gefunden wird, wird `document.write(` ausgeführt und erstellt ein Skript. 

## <a name="commonly-used-script-tag-helper-attributes"></a>Häufig verwendete Attribute des Skript-Taghilfsprogramms

Sie finden alle Attribute, Eigenschaften und Methoden des Skript-Taghilfsprogramms in der Referenz zur [ScriptTagHelper-Klasse](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper).

### <a name="href"></a>href

Die bevorzugte Adresse der verknüpften Ressource. Die Adresse wird in allen Fällen an den generierten HTML-Code übergeben.

### <a name="asp-fallback-href"></a>asp-fallback-href

Die URL eines Cascading Stylesheet (CSS), das als Fallback verwendet wird, falls die primäre URL nicht verfügbar ist.

### <a name="asp-fallback-test-class"></a>asp-fallback-test-class

Der in der Formatvorlage definierte Klassenname, der zum Fallbacktest verwendet werden soll. Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.

### <a name="asp-fallback-test-property"></a>asp-fallback-test-property

Der CSS-Eigenschaftsname, der für den Fallbacktest verwendet werden soll. Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.

### <a name="asp-fallback-test-value"></a>asp-fallback-test-value

Der CSS-Eigenschaftswert, der für den Fallbacktest verwendet werden soll. Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.

### <a name="asp-fallback-test-value"></a>asp-fallback-test-value

Der CSS-Eigenschaftswert, der für den Fallbacktest verwendet werden soll. Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
