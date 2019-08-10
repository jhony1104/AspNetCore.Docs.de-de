---
title: ASP.net Core blazor-Layouts
author: guardrex
description: Erfahren Sie, wie Sie wiederverwendbare Layoutkomponenten für blazor-Apps erstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 07/02/2019
uid: blazor/layouts
ms.openlocfilehash: 2d652e149381f0a93e3135da978ab5737d47c6f1
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/03/2019
ms.locfileid: "68948220"
---
# <a name="aspnet-core-blazor-layouts"></a>ASP.net Core blazor-Layouts

Von [Rainer stropek](https://www.timecockpit.com)

Einige APP-Elemente, z. b. Menüs, Copyright Meldungen und Firmenlogos, sind in der Regel Teil des gesamten Layouts der APP und werden von jeder Komponente in der APP verwendet. Das Kopieren des Codes dieser Elemente in alle Komponenten einer APP ist kein effizienter Ansatz&mdash;, wenn eines der Elemente ein Update erfordert, muss jede Komponente aktualisiert werden. Eine solche Duplizierung ist schwierig zu verwalten und kann zu inkonsistenten Inhalten im Laufe der Zeit führen. *Layouts* lösen dieses Problem.

Technisch gesehen ist ein Layout nur eine andere Komponente. Ein Layout ist in einer Razor-Vorlage oder im C# Code definiert und kann [Datenbindung](xref:blazor/components#data-binding), [Abhängigkeitsinjektion](xref:blazor/dependency-injection)und andere Komponenten Szenarios verwenden.

Um eine *Komponente* in ein *Layout*umzuwandeln, ist die Komponente:

* Erbt von `LayoutComponentBase`, wodurch eine `Body` Eigenschaft für den gerenderten Inhalt innerhalb des Layouts definiert wird.
* Verwendet die Razor-Syntax `@Body` , um den Speicherort im layoutmarkup anzugeben, an dem der Inhalt gerendert wird.

Das folgende Codebeispiel zeigt die Razor-Vorlage der Layoutkomponente *MainLayout. Razor*. Das Layout erbt `LayoutComponentBase` und legt die `@Body` zwischen der Navigationsleiste und der Fußzeile fest:

[!code-cshtml[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

## <a name="specify-a-layout-in-a-component"></a>Angeben eines Layouts in einer Komponente

Verwenden Sie die Razor `@layout` -Direktive, um ein Layout auf eine Komponente anzuwenden. Der Compiler konvertiert `@layout` in eine `LayoutAttribute`, die auf die Component-Klasse angewendet wird.

Der Inhalt der folgenden Komponente, *Masterlist. Razor*, wird `MainLayout` an der Position von `@Body`in eingefügt:

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

## <a name="centralized-layout-selection"></a>Zentrale Layoutauswahl

Jeder Ordner einer APP kann optional eine Vorlagen Datei mit dem Namen *_Imports. Razor*enthalten. Der Compiler schließt die in der Imports-Datei angegebenen Direktiven in allen Razor-Vorlagen im gleichen Ordner und rekursiv in allen Unterordnern ein. Daher wird durch eine *_Imports. Razor* - `@layout MainLayout` Datei sichergestellt, dass alle Komponenten in einem Ordner `MainLayout`verwenden. Es ist nicht erforderlich, alle `@layout MainLayout` *Razor* -Dateien im Ordner und in den Unterordnern wiederholt hinzuzufügen. `@using`Direktiven werden auch auf die gleiche Weise auf Komponenten angewendet.

Die folgenden *_Imports. Razor* -Datei wird importiert:

* `MainLayout`.
* Alle Razor-Komponenten im selben Ordner und in allen Unterordnern.
* Der `BlazorApp1.Data` -Namespace.
 
[!code-cshtml[](layouts/sample_snapshot/3.x/_Imports.razor)]

Die Datei *_Imports. Razor* ähnelt der [Datei _ViewImports. cshtml für Razor-Ansichten und-Seiten](xref:mvc/views/layout#importing-shared-directives) , die jedoch speziell auf Razor-Komponenten Dateien angewendet werden.

In den blazor-Vorlagen werden *_Imports. Razor* -Dateien für die Layoutauswahl verwendet. Eine APP, die über eine blazor-Vorlage erstellt wurde, enthält die Datei *_Imports. Razor* im Stammverzeichnis des Projekts und im Ordner *pages* .

## <a name="nested-layouts"></a>In-Netz-Layouts

Apps können aus den in einem Netz gefügten Layouts bestehen. Eine Komponente kann auf ein Layout verweisen, das wiederum auf ein anderes Layout verweist. Schachtelungs Layouts werden z. b. verwendet, um eine Menüstruktur mit mehreren Ebenen zu erstellen.

Im folgenden Beispiel wird gezeigt, wie Sie die Verwendung von Netz Layouts durchführen. Die Datei " *episodescomponent. Razor* " ist die Komponente, die angezeigt werden soll. Die Komponente verweist auf `MasterListLayout`das:

[!code-cshtml[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

Die Datei *masterlistlayout. Razor* stellt den `MasterListLayout`bereit. Das Layout verweist auf ein anderes `MasterLayout`Layout,, in dem es gerendert wird. `EpisodesComponent`wird gerendert, wo `@Body` angezeigt wird

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

Schließlich enthält in Masterlayout. Razor die Layoutelemente der obersten Ebene, z. b. Header, Hauptmenü und Fußzeile. `MasterLayout` `MasterListLayout`mit `EpisodesComponent` werden gerendert, wo `@Body` angezeigt wird:

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:mvc/views/layout>
