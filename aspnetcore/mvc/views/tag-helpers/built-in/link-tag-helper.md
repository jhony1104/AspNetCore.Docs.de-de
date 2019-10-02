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
# <a name="link-tag-helper-in-aspnet-core"></a>Link-Taghilfsprogramm in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

Das [Link-Taghilfsprogramm](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper) generiert einen Link zu einer primären oder Fallback-CSS-Datei. Die primäre CSS-Datei befindet sich in der Regel in einem [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).

[!INCLUDE[](~/includes/cdn.md)]

Mit dem Link-Taghilfsprogramm können Sie ein CDN für die CSS-Datei und eine Fallbackquelle angeben, wenn das CDN nicht verfügbar ist. Das Link-Taghilfsprogramm bietet den Leistungsvorteil eines CDN kombiniert mit der Stabilität des lokalen Hostings.

Das folgende Razor-Markup zeigt das `head`-Element einer Layoutdatei, die mit der Web-App-Vorlage von ASP.NET Core erstellt wurde:

[!code-html[](link-tag-helper/sample/_Layout.cshtml?name=snippet)]

Das folgende Beispiel zeigt den gerenderten HTML-Code aus dem vorherigen Beispiel (in einer nicht zur Entwicklung dienenden Umgebung):

[!code-csharp[](link-tag-helper/sample/HtmlPage1.html)]

Im vorherigen Beispiel hat das Link-Taghilfsprogramm das `<meta name="x-stylesheet-fallback-test" content="" class="sr-only" />`-Element und den folgenden JavaScript-Code generiert, mit dem überprüft wird, ob die angeforderte Datei *bootstrap.min.css* im CDN verfügbar ist. In diesem Fall war die CSS-Datei verfügbar, sodass das Taghilfsprogramm das `<link />`-Element mit der CSS-Datei aus dem CDN generiert hat.

## <a name="commonly-used-link-tag-helper-attributes"></a>Häufig verwendete Attribute des Link-Taghilfsprogramms

Sie finden alle Attribute, Eigenschaften und Methoden des Link-Taghilfsprogramms in der Referenz zur [LinkTagHelper-Klasse](xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper).

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
