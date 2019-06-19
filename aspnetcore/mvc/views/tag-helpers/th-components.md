---
title: Taghilfsprogrammkomponenten in ASP.NET Core
author: scottaddie
description: Informationen zu Taghilfsprogrammkomponenten und deren Verwendung in ASP.NET Core.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.date: 06/12/2019
uid: mvc/views/tag-helpers/th-components
ms.openlocfilehash: b5b3abea6492cfaa7d6acd0e54073a8db12eb2a5
ms.sourcegitcommit: 335a88c1b6e7f0caa8a3a27db57c56664d676d34
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/12/2019
ms.locfileid: "67034753"
---
# <a name="tag-helper-components-in-aspnet-core"></a>Taghilfsprogrammkomponenten in ASP.NET Core

Von [Scott Addie](https://twitter.com/Scott_Addie) und [Fiyaz Bin Hasan](https://github.com/fiyazbinhasan)

Eine Taghilfsprogrammkomponente ist ein Taghilfsprogramm, mit dem Sie HTML-Elemente aus serverseitigem Code bedingt ändern oder hinzufügen können. Dieses Feature ist in ASP.NET Core 2.0 oder höher verfügbar.

ASP.NET Core enthält zwei integrierte Taghilfsprogrammkomponenten: `head` und `body`. Sie befinden sich im Namespace <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> und können in MVC und Razor Pages verwendet werden. Taghilfsprogrammkomponenten benötigen keine Registrierung bei der App in *_ViewImports.cshtml*.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="use-cases"></a>Anwendungsfälle

Zwei gängige Anwendungsfälle für Taghilfsprogrammkomponenten sind:

1. [Einfügen eines `<link>` in `<head>`.](#inject-into-html-head-element)
1. [Einfügen eines `<script>` in `<body>`.](#inject-into-html-body-element)

In den folgenden Abschnitten werden diese Anwendungsfälle beschrieben.

### <a name="inject-into-html-head-element"></a>Einfügen in HTML-head-Element

Im HTML-`<head>`-Element werden CSS-Dateien häufig mit dem HTML-`<link>`-Element importiert. Der folgende Code fügt ein `<link>`-Element in das `<head>`-Element mit der `head`-Taghilfsprogrammkomponente ein:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressStyleTagHelperComponent.cs)]

Für den Code oben gilt:

* `AddressStyleTagHelperComponent` implementiert <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>. Die Abstraktion:
  * Ermöglicht die Initialisierung der Klasse mit einem <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.
  * Ermöglicht die Verwendung von Taghilfsprogrammkomponenten zum Hinzufügen oder Ändern von HTML-Elementen.
* Die <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*>-Eigenschaft definiert die Reihenfolge, in der die Komponenten gerendert werden. `Order` ist erforderlich, wenn mehrere Verwendungsmöglichkeiten für Taghilfsprogrammkomponenten in einer App vorhanden sind.
* <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> vergleicht den <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*>-Eigenschaftswert des Ausführungskontexts mit `head`. Wenn der Vergleich zu TRUE ausgewertet wird, wird der Inhalt des `_style`-Felds in das HTML`<head>`-Element eingefügt.

### <a name="inject-into-html-body-element"></a>Einfügen in HTML-body-Element

Die `body`-Taghilfsprogrammkomponente kann ein `<script>`-Element in das `<body>`-Element einfügen. Im folgenden Code wird diese Technik veranschaulicht:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressScriptTagHelperComponent.cs)]

Eine separate HTML-Datei wird zum Speichern des `<script>`-Elements verwendet. Durch die HTML-Datei wird der Code übersichtlicher und besser verwaltbar. Der vorangehende Code liest den Inhalt von *TagHelpers/Templates/AddressToolTipScript.html* und fügt diesen mit der Ausgabe des Taghilfsprogramms an. Die Datei *AddressToolTipScript.html* enthält das folgende Markup:

[!code-html[](th-components/samples/RazorPagesSample/TagHelpers/Templates/AddressToolTipScript.html)]

Der vorangehende Code bindet ein [Bootstrap-QuickInfo-Widget](https://getbootstrap.com/docs/3.3/javascript/#tooltips) an ein beliebiges `<address>`-Element, das ein `printable`-Attribut enthält. Der Effekt wird sichtbar, wenn ein Mauszeiger über das Element bewegt wird.

## <a name="register-a-component"></a>Registrieren einer Komponente

Eine Taghilfsprogrammkomponente muss der Sammlung „Taghilfsprogrammkomponenten“ der App hinzugefügt werden. Es gibt drei Möglichkeiten, der Sammlung Taghilfsprogrammkomponenten hinzuzufügen:

* [Taghilfsprogrammkomponenten in ASP.NET Core](#tag-helper-components-in-aspnet-core)
  * [Anwendungsfälle](#use-cases)
    * [Einfügen in HTML-head-Element](#inject-into-html-head-element)
    * [Einfügen in HTML-body-Element](#inject-into-html-body-element)
  * [Registrieren einer Komponente](#register-a-component)
    * [Registrierung über Dienstcontainer](#registration-via-services-container)
    * [Registrierung über Razor-Datei](#registration-via-razor-file)
    * [Registrierung über Seitenmodell oder Controller](#registration-via-page-model-or-controller)
  * [Erstellen einer Komponente](#create-a-component)
  * [Zusätzliche Ressourcen](#additional-resources)

### <a name="registration-via-services-container"></a>Registrierung über Dienstcontainer

Wenn die Klasse der Taghilfsprogrammkomponente nicht mit <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager> verwaltet wird, muss sie mit dem [Abhängigkeitsinjektionssystem (Dependency Injection, DI)](xref:fundamentals/dependency-injection) registriert werden. Der folgende `Startup.ConfigureServices`-Code registriert die `AddressStyleTagHelperComponent`- und `AddressScriptTagHelperComponent`- -Klassen mit einer [vorübergehenden Lebensdauer](xref:fundamentals/dependency-injection#lifetime-and-registration-options):

[!code-csharp[](th-components/samples/RazorPagesSample/Startup.cs?name=snippet_ConfigureServices&highlight=12-15)]

### <a name="registration-via-razor-file"></a>Registrierung über Razor-Datei

Wenn die Taghilfsprogrammkomponente nicht mit Abhängigkeitsinjektion registriert wird, kann sie über eine Razor Pages-Seite oder eine MVC-Ansicht registriert werden. Diese Technik wird verwendet, um das injizierte Markup und die Reihenfolge der Komponentenausführung aus einer Razor-Datei zu steuern.

`ITagHelperComponentManager` dient zum Hinzufügen von Taghilfsprogrammkomponenten oder zum Entfernen aus der App. Im folgenden Code wird diese Technik mit `AddressTagHelperComponent` veranschaulicht:

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_ITagHelperComponentManager)]

Für den Code oben gilt:

* Die `@inject`-Direktive stellt eine Instanz von `ITagHelperComponentManager` zur Verfügung. Die Instanz wird einer Variablen mit dem Namen `manager` für den Downstreamzugriff in der Razor-Datei zugewiesen.
* Eine Instanz von `AddressTagHelperComponent` wird der Sammlung „Taghilfsprogrammkomponenten“ der App hinzugefügt.

`AddressTagHelperComponent` wird geändert, um einen Konstruktor zu berücksichtigen, der die `markup`- und `order`-Parameter annimmt:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_Constructor)]

Der bereitgestellte `markup`-Parameter wird in `ProcessAsync` wie folgt verwendet:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_ProcessAsync&highlight=10-11)]

### <a name="registration-via-page-model-or-controller"></a>Registrierung über Seitenmodell oder Controller

Wenn die Taghilfsprogrammkomponente nicht mit Abhängigkeitsinjektion registriert wird, kann sie über ein Razor Pages-Seitenmodell oder einen MVC-Controller registriert werden. Diese Methode eignet sich zum Trennen von C#-Logik von Razor-Dateien.

Die Konstruktorinjektion wird verwendet, um auf eine Instanz von `ITagHelperComponentManager` zuzugreifen. Die Taghilfsprogrammkomponente wird der Sammlung „Taghilfsprogrammkomponenten“ der Instanz hinzugefügt. Das folgende Razor Pages-Seitenmodell veranschaulicht diese Technik mit `AddressTagHelperComponent`:

[!code-csharp[](th-components/samples/RazorPagesSample/Pages/Index.cshtml.cs?name=snippet_IndexModelClass)]

Für den Code oben gilt:

* Die Konstruktorinjektion wird verwendet, um auf eine Instanz von `ITagHelperComponentManager` zuzugreifen.
* Eine Instanz von `AddressTagHelperComponent` wird der Sammlung „Taghilfsprogrammkomponenten“ der App hinzugefügt.

## <a name="create-a-component"></a>Erstellen einer Komponente

So erstellen Sie eine benutzerdefinierte Taghilfsprogrammkomponente:

* Erstellen Sie eine öffentliche Klasse, die von <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper> ableitet ist.
* Wenden Sie ein [[HtmlTargetElement]](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute)-Attribut auf die Klasse an. Geben Sie den Namen des HTML-Zielelements an.
* *Optional:* Wenden Sie ein [[EditorBrowsable(EditorBrowsableState.Never)]](xref:System.ComponentModel.EditorBrowsableAttribute)-Attribut auf die Klasse an, um die Anzeige des Typs in IntelliSense zu unterdrücken.

Der folgende Code erstellt eine benutzerdefinierte Taghilfsprogrammkomponente, das als Ziel das `<address>`-HTML-Element aufweist:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponentTagHelper.cs)]

Verwenden Sie die benutzerdefinierte `address`-Taghilfsprogrammkomponente zum Einfügen von HTML-Markup wie hier gezeigt:

```csharp
public class AddressTagHelperComponent : TagHelperComponent
{
    private readonly string _printableButton =
        "<button type='button' class='btn btn-info' onclick=\"window.open("
        "'https://binged.it/2AXRRYw')\">" +
        "<span class='glyphicon glyphicon-road' aria-hidden='true'></span>" +
        "</button>";

    public override int Order => 3;

    public override async Task ProcessAsync(TagHelperContext context,
                                            TagHelperOutput output)
    {
        if (string.Equals(context.TagName, "address",
                StringComparison.OrdinalIgnoreCase) &&
            output.Attributes.ContainsName("printable"))
        {
            var content = await output.GetChildContentAsync();
            output.Content.SetHtmlContent(
                $"<div>{content.GetContent()}</div>{_printableButton}");
        }
    }
}
```

Die obige `ProcessAsync`-Methode injiziert den für <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> bereitgestellten HTML-Code in das entsprechende `<address>`-Element. Die Einfügung tritt auf, wenn:

* Der `TagName`-Eigenschaftswert des Ausführungskontexts `address` entspricht.
* Das entsprechende `<address>`-Element über ein `printable`-Attribut verfügt.

Beispielsweise wird die `if`-Anweisung bei der Verarbeitung des folgenden `<address>`-Elements zu TRUE ausgewertet:

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_AddressPrintable)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
* <xref:mvc/views/tag-helpers/builtin-th/Index>
