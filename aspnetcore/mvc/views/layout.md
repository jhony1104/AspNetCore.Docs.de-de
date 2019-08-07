---
title: Layout in ASP.NET Core
author: ardalis
description: Erfahren Sie, wie man gängige Layouts verwendet, Anweisungen von mehreren Ansichten gemeinsam nutzen lässt und Programmcode vor dem Rendern der Ansichten in einer ASP.NET Core-App ausführt.
ms.author: riande
ms.date: 07/30/2019
uid: mvc/views/layout
ms.openlocfilehash: 6bd9dfc65c026ee524277aaaa21333d299c8981e
ms.sourcegitcommit: 7001657c00358b082734ba4273693b9b3ed35d2a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68670007"
---
# <a name="layout-in-aspnet-core"></a>Layout in ASP.NET Core

Von [Steve Smith](https://ardalis.com/) und [Dave Brock](https://twitter.com/daveabrock)

Seiten und Ansichten beinhalten häufig sowohl visuelle als auch programmgesteuerte Elemente. Dieser Artikel demonstriert Folgendes:

* Verwendung von allgemeinen Layouts
* Freigeben von Anweisungen
* Führen Sie den allgemeinen Code aus, bevor Sie Seiten oder Ansichten rendern.

In diesem Dokument werden Layouts für zwei verschiedene Ansätze zu ASP.NET Core MVC erläutert: Razor Pages und Controller mit Ansichten. In diesem Thema sind die Unterschiede minimal:

* Razor-Seiten befinden sich im Ordner *Pages*.
* Controller mit Ansichten verwenden einen Ordner namens *Views* für Ansichten.

## <a name="what-is-a-layout"></a>Was ist ein Layout?

Die meisten Web-Apps haben ein gebräuchliches Layout, das dem Benutzer beim Navigieren auf den Seiten ein konsistentes Verhalten bietet. Das Layout enthält normalerweise allgemeine Benutzeroberflächenelemente wie App-Header, Navigations- oder Menüelemente sowie eine Fußzeile.

![Beispiel für ein Seitenlayout](layout/_static/page-layout.png)

Gängige HTML-Strukturen wie Skripts und Stylesheets werden häufig von vielen Seiten in einer App verwendet. Alle diese gemeinsamen Elemente werden in einer *Layoutdatei* definiert, auf die dann von jeder Ansicht einer App verwiesen werden kann. Layouts minimieren duplizierten Code in Ansichten.

Gemäß Konvention ist *_Layout.cshtml* das Standardlayout für eine ASP.NET Core-App. Für neue ASP.NET Core-Projekte, die mit den Vorlagen erstellt wurden, sind folgende Layoutdateien vorhanden:

* Razor Pages: *Pages/Shared/_Layout.cshtml*

  ![Ordner „Pages“ im Projektmappen-Explorer](layout/_static/rp-web-project-views.png)

* Controller mit Ansichten: *Views/Shared/_Layout.cshtml*

  ![Ordner „Views“ im Projektmappen-Explorer](layout/_static/mvc-web-project-views.png)

Das Layout definiert eine übergeordnete Vorlage für die Ansichten einer App. Apps erfordern nicht zwingend ein Layout. Apps können aber auch mehrere Layouts definieren. Die Ansichten der App können dann unterschiedliche Layouts nutzen.

Der folgende Code zeigt die Layoutdatei für eine Vorlage, die mit dem Projekt mit einem Controller und Ansichten erstellt wurde:

[!code-cshtml[](~/common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=44,72)]

## <a name="specifying-a-layout"></a>Festlegen eines Layouts

Razor-Ansichten verfügen über eine `Layout` -Eigenschaft. Durch Festlegen dieser Eigenschaft wird das Layout der jeweiligen Ansicht bestimmt:

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml?highlight=2)]

Das angegebene Layout kann einen vollständigen Pfad (z.B. */Pages/Shared/_Layout.cshtml* oder */Views/Shared/_Layout.cshtml*) oder einen Teil des Namens (Beispiel: `_Layout`) aufweisen. Wird ein Teil des Namens angegeben, dann sucht die Razor-Ansichts-Engine die Layoutdatei unter Verwendung des standardmäßigen Ermittlungsprozesses. Der Ordner, in dem sich die die Handlermethode (oder der Controller) befindet, wird zuerst durchsucht und danach der Ordner *Shared*. Dieser Ermittlungsprozess ist identisch mit dem Prozess zum Auffinden von [Teilansichten](xref:mvc/views/partial#partial-view-discovery).

Standardmäßig muss jedes Layout `RenderBody` aufrufen. Wo immer der Aufruf von `RenderBody` platziert ist, wird der Inhalt der Ansicht gerendert.

<a name="layout-sections-label"></a>

### <a name="sections"></a>Abschnitte

Optional kann ein Layout auf mindestens einen *Abschnitt* verweisen, indem es `RenderSection` aufruft. Abschnitte geben an, wo bestimmte Seitenelemente platziert werden sollen. Jeder Aufruf von `RenderSection` kann angeben, ob dieser Abschnitt erforderlich ist:

```html
@section Scripts {
    @RenderSection("Scripts", required: false)
}
```

Wenn ein erforderlicher Bereich nicht gefunden werden kann, wird eine Ausnahme ausgelöst. Einzelne Ansichten verwenden die Razor-Syntax `@section`, um den in einem Abschnitt zu rendernden Inhalt anzugeben. Wenn eine Seite oder eine Ansicht einen Abschnitt definiert, muss dieser auch gerendert werden. Andernfalls tritt ein Fehler auf.

Eine Beispieldefinition von `@section` in einer Razor Pages-Ansicht:

```html
@section Scripts {
     <script type="text/javascript" src="/scripts/main.js"></script>
}
```

Im vorangehenden Code wird *scripts/main.js* zum Abschnitt `scripts` auf einer Seite oder in einer Ansicht hinzugefügt. Andere Seiten oder Ansichten in der gleichen App benötigen dieses Skript möglicherweise nicht und definieren einen Abschnitt zu Skripts.

In der folgenden Markupdatei wird die Datei *_ValidationScriptsPartial.cshtml* mit dem [Partial-Taghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) gerendert:

```html
@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

Die vorhergehende Markupdatei wurde durch [Gerüstidentitäten](xref:security/authentication/scaffold-identity) generiert.

Die Abschnitte, die auf einer Seite oder in einer Ansicht definiert wurden, stehen nur auf deren Layoutseite zur Verfügung. Teilansichten, Ansichtskomponenten und andere Teile eines Ansichtssystems können nicht auf sie verweisen.

### <a name="ignoring-sections"></a>Ignorieren von Abschnitten

Standardmäßig müssen der Text und die Abschnitte einer Inhaltsseite alle von der Layoutseite gerendert werden. Die Razor-Ansichtsengine erzwingt dies, indem sie erfasst, ob der Text und jeder Abschnitt gerendert wurde.

Rufen Sie die Methoden `IgnoreBody` und `IgnoreSection` auf, um die Ansichtsengine anzuweisen, den Text oder die Abschnitte zu ignorieren.

Der Text und jeder Abschnitt einer Razor Page müssen entweder gerendert oder ignoriert werden.

<a name="viewimports"></a>

## <a name="importing-shared-directives"></a>Importieren gemeinsam verwendeter Anweisungen

Seiten und Ansichten können Razor-Anweisungen zum Importieren von Namespaces und die [Abhängigkeitsinjektion](dependency-injection.md) verwenden. Anweisungen, die von mehreren Ansichten gemeinsam verwendet werden, können in einer gemeinsam verwendeten Datei namens *_ViewImports.cshtml* angegeben werden. Die `_ViewImports`-Datei unterstützt die folgenden Anweisungen:

* `@addTagHelper`
* `@removeTagHelper`
* `@tagHelperPrefix`
* `@using`
* `@model`
* `@inherits`
* `@inject`

Die Datei unterstützt keine anderen Razor-Features wie Funktionen und Abschnittdefinitionen.

Eine `_ViewImports.cshtml`-Beispieldatei:

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewImports.cshtml)]

Die Datei *_ViewImports.cshtml* für eine ASP.NET Core MVC-App befindet sich normalerweise im Ordner *Pages* (oder *Views*). Eine Datei *_ViewImports.cshtml* kann auch in einen anderen Ordner verschoben werden. In diesem Fall wird sie nur auf die Seiten oder Ansichten in diesem Ordner und in dessen Unterordnern angewendet. `_ViewImports`-Dateien werden beginnend ab der Stammebene verarbeitet und dann einzeln für jeden Ordner bis zum Speicherort der Seite oder der Ansicht selbst. Die `_ViewImports`-Einstellungen auf der Stammebene werden möglicherweise auf Ordnerebene außer Kraft gesetzt.

Nehmen Sie beispielsweise Folgendes an:

* Die Datei *_ViewImports.cshtml* auf der Stammebene enthält `@model MyModel1` und `@addTagHelper *, MyTagHelper1`.
* Die Datei *_ViewImports.cshtml* in einem Unterordner enthält `@model MyModel2` und `@addTagHelper *, MyTagHelper2`.

Seiten und Ansichten im Unterordner haben Zugriff auf beide Taghilfsprogramme und das Modell `MyModel2`.

Wenn sich mehrere Dateien namens *_ViewImports.cshtml* in der Hierarchie befinden, umfasst das kombinierte Verhalten der Anweisungen Folgendes:

* `@addTagHelper`, `@removeTagHelper`: werden nach der Reihe ausgeführt
* `@tagHelperPrefix`: dasjenige, das der Ansicht am Nächsten ist, setzt die anderen außer Kraft
* `@model`: dasjenige, das der Ansicht am Nächsten ist, setzt die anderen außer Kraft
* `@inherits`: dasjenige, das der Ansicht am Nächsten ist, setzt die anderen außer Kraft
* `@using`: alle einbezogen; Duplikate werden ignoriert
* `@inject`: dasjenige, das der Ansicht am Nächsten ist, setzt für jede Eigenschaft alle anderen mit dem gleichen Namen außer Kraft

<a name="viewstart"></a>

## <a name="running-code-before-each-view"></a>Ausführen von Code vor jeder Ansicht

Code, der ausgeführt werden muss, bevor die einzelnen Ansichten oder Seiten in die Datei *_ViewStart.cshtml* platziert werden. Gemäß der Konvention befindet sich die Datei *_ViewStart.cshtml* im Ordner *Seiten* (oder *Ansichten*). Die in *_ViewStart.cshtml* aufgelisteten Anweisungen werden vor jeder vollständigen Ansicht (also keine Layouts und keine Teilansichten) ausgeführt. *_ViewStart.cshtml* ist genauso wie [ViewImports.cshtml](xref:mvc/views/layout#viewimports) hierarchisch. Wenn eine Datei namens *_ViewStart.cshtml* im Ordner „Ansichten“ oder „Seiten“, der mit einem Controller verknüpft ist, definiert wird, wird sie nach derjenigen ausgeführt, die im Stamm des Ordners *Seiten* (oder *Ansichten*) definiert wurde (falls vorhanden).

Ein Beispiel für die Datei *_ViewStart.cshtml*:

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml)]

Die oben stehende Datei gibt an, dass alle Ansichten das Layout *_Layout.cshtml* verwenden.

*_ViewStart.cshtml* und *_ViewImports.cshtml* werden in der Regel **nicht** in den Ordner */Pages/Shared* (oder  */Views/Shared*) platziert. Die Versionen dieser Dateien auf Anwendungsebene sollten direkt in den Ordner */Pages* (oder */Views*) platziert werden.
