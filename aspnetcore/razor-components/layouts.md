---
title: Layouts für Razor-Komponenten
author: guardrex
description: Erfahren Sie, wie Komponenten von wiederverwendbares Layout für Razor-Komponenten-apps zu erstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: razor-components/layouts
ms.openlocfilehash: 31ed940ce40e3ae6e3744418cf241d396308f4fe
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59515525"
---
# <a name="razor-components-layouts"></a>Layouts für Razor-Komponenten

Durch [Rainer Stropek](https://www.timecockpit.com)

Apps werden in der Regel mehr als eine Komponente enthalten. Layoutelemente, z. B. Menüs, copyright Nachrichten und Logos, müssen auf alle Komponenten vorhanden sein. Kopieren den Code für diese Layoutelemente in alle Komponenten einer App ist ein effizienter Ansatz nicht. Solche Duplizierung ist schwer zu verwalten und wahrscheinlich führt zu einer inkonsistenten Inhalt im Laufe der Zeit. *Layouts* dieses Problem zu lösen.

Technisch gesehen ist ein Layout nur eine andere Komponente. Ein Layout wird definiert, in einer Razor-Vorlage oder in C# code und dürfen [Datenbindung](xref:razor-components/components#data-binding), [Abhängigkeitsinjektion](xref:razor-components/dependency-injection), und andere gewöhnlichen Funktionen von Komponenten.

Aktivieren Sie zwei zusätzliche Aspekte einer *Komponente* in einer *Layout*

* Die Layoutkomponente muss vom erben `LayoutComponentBase`. `LayoutComponentBase` definiert eine `Body` -Eigenschaft, die den Inhalt enthält, in das Layout gerendert werden.
* Die Layoutkomponente verwendet die `Body` Eigenschaft, um anzugeben, wo der Inhalt gerendert wird, mit der Razor-Syntax `@Body`. Während des Renderns `@Body` durch den Inhalt des Layouts ersetzt wird.

Das folgende Codebeispiel zeigt die Razor-Vorlage für eine Layoutkomponente. Beachten Sie die Verwendung von `LayoutComponentBase` und `@Body`:

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterLayout.cshtml)]

## <a name="use-a-layout-in-a-component"></a>Verwenden Sie ein Layout in einer Komponente

Verwenden Sie die Razor-Direktive `@layout` ein Layout auf eine Komponente anwenden. Der Compiler konvertiert diese Richtlinie in eine `LayoutAttribute`, das auf die Component-Klasse angewendet wird.

Im folgenden Codebeispiel wird das Konzept veranschaulicht. Der Inhalt dieser Komponente eingefügt, in der *MasterLayout* an der Position des `@Body`:

```cshtml
@layout MasterLayout
@page "/master-list"

<h2>Master Episode List</h2>
```

## <a name="centralized-layout-selection"></a>Zentralisierte Layoutauswahl.

Jeder Ordner, der eine app kann optional eine Vorlagendatei, die mit dem Namen enthalten *_ViewImports.cshtml*. Der Compiler schließt die Anweisungen in der Ansicht Imports-Datei in der Razor-Vorlagen im selben Ordner und rekursiv in allen Unterordnern angegeben. Aus diesem Grund eine *_ViewImports.cshtml* Datei mit `@layout MainLayout` wird sichergestellt, dass alle Komponenten in einem Ordner die *MainLayout* Layout. Besteht keine Notwendigkeit wiederholt hinzufügen `@layout` aller der *.razor* Dateien.

Beachten Sie, die die Standardvorlage verwendet die *_ViewImports.cshtml* Mechanismus zur Layoutauswahl. Eine neu erstellte app enthält die *_ViewImports.cshtml* Datei die *Komponenten/Seiten* Ordner.

## <a name="nested-layouts"></a>Geschachtelte layouts

Apps können auf geschachtelte Layouts bestehen. Eine Komponente kann es sich um ein Layout verweisen, das wiederum ein anderes Layout verweist. Beispielsweise können schachteln Layouts verwendet werden, mit mehreren Ebenen mit eine Menüstruktur entsprechend.

Die folgenden Codebeispiele zeigen, wie geschachtelte Layouts verwendet wird. Die *EpisodesComponent.cshtml* Datei ist die Komponente angezeigt. Beachten Sie, dass die Komponente verweist, das Layout auf `MasterListLayout`.

*EpisodesComponent.cshtml*:

```cshtml
@layout MasterListLayout
@page "/master-list/episodes"

<h1>Episodes</h1>
```

Die *MasterListLayout.cshtml* Datei enthält die `MasterListLayout`. Das Layout verweist auf ein anderes Layout `MasterLayout`, wohin diese fließen, die eingebettet werden.

*MasterListLayout.cshtml*:

```cshtml
@layout MasterLayout
@inherits LayoutComponentBase

<nav>
    <!-- Menu structure of master list -->
    ...
</nav>

@Body
```

Zum Schluss `MasterLayout` enthält die Layoutelemente der obersten Ebene, z. B. die Header, Footer und im Hauptmenü.

*MasterLayout.cshtml*:

```cshtml
@inherits LayoutComponentBase

<header>...</header>
<nav>...</nav>

@Body
```
