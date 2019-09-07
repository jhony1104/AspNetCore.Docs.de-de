---
title: ASP.net Core blazor-Layouts
author: guardrex
description: Erfahren Sie, wie Sie wiederverwendbare Layoutkomponenten für blazor-Apps erstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/06/2019
uid: blazor/layouts
ms.openlocfilehash: 05a38c10e18407d50422192ab1ddf3ff4b0f3a5b
ms.sourcegitcommit: 43c6335b5859282f64d66a7696c5935a2bcdf966
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2019
ms.locfileid: "70800360"
---
# <a name="aspnet-core-blazor-layouts"></a>ASP.net Core blazor-Layouts

Von [Rainer stropek](https://www.timecockpit.com) und [Luke Latham](https://github.com/guardrex)

Einige APP-Elemente, z. b. Menüs, Copyright Meldungen und Firmenlogos, sind in der Regel Teil des gesamten Layouts der APP und werden von jeder Komponente in der APP verwendet. Das Kopieren des Codes dieser Elemente in alle Komponenten einer APP ist kein effizienter Ansatz&mdash;, wenn eines der Elemente ein Update erfordert, muss jede Komponente aktualisiert werden. Eine solche Duplizierung ist schwierig zu verwalten und kann zu inkonsistenten Inhalten im Laufe der Zeit führen. *Layouts* lösen dieses Problem.

Technisch gesehen ist ein Layout nur eine andere Komponente. Ein Layout ist in einer Razor-Vorlage oder im C# Code definiert und kann [Datenbindung](xref:blazor/components#data-binding), [Abhängigkeitsinjektion](xref:blazor/dependency-injection)und andere Komponenten Szenarios verwenden.

Um eine *Komponente* in ein *Layout*umzuwandeln, ist die Komponente:

* Erbt von `LayoutComponentBase`, wodurch eine `Body` Eigenschaft für den gerenderten Inhalt innerhalb des Layouts definiert wird.
* Verwendet die Razor-Syntax `@Body` , um den Speicherort im layoutmarkup anzugeben, an dem der Inhalt gerendert wird.

Das folgende Codebeispiel zeigt die Razor-Vorlage der Layoutkomponente *MainLayout. Razor*. Das Layout erbt `LayoutComponentBase` und legt die `@Body` zwischen der Navigationsleiste und der Fußzeile fest:

[!code-cshtml[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

In einer APP, die auf einer der Vorlagen der blazor-APP `MainLayout` basiert, befindet sich die Komponente (*MainLayout. Razor*) im frei *gegebenen* Ordner der app.

## <a name="default-layout"></a>Standardlayout

Geben Sie das standardmäßige APP- `Router` Layout in der-Komponente in der *app. Razor* -Datei der APP an. Mit der `Router` folgenden Komponente, die von den Standardvorlagen für blazor bereitgestellt wird, wird das Standard `MainLayout` Layout auf die Komponente festgelegt:

[!code-cshtml[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

Wenn Sie ein Standardlayout für `NotFound` den Inhalt angeben möchten `LayoutView` , `NotFound` geben Sie einen für Inhalt an:

[!code-cshtml[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

Weitere Informationen zur `Router` -Komponente finden <xref:blazor/routing>Sie unter.

## <a name="specify-a-layout-in-a-component"></a>Angeben eines Layouts in einer Komponente

Verwenden Sie die Razor `@layout` -Direktive, um ein Layout auf eine Komponente anzuwenden. Der Compiler konvertiert `@layout` in eine `LayoutAttribute`, die auf die Component-Klasse angewendet wird.

Der Inhalt der folgenden `MasterList` Komponente wird `MasterLayout` an der Position von `@Body`in eingefügt:

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

## <a name="centralized-layout-selection"></a>Zentrale Layoutauswahl

Jeder Ordner einer APP kann optional eine Vorlagen Datei mit dem Namen *_Imports. Razor*enthalten. Der Compiler schließt die in der Imports-Datei angegebenen Direktiven in allen Razor-Vorlagen im gleichen Ordner und rekursiv in allen Unterordnern ein. Daher wird durch eine *_Imports. Razor* - `@layout MyCoolLayout` Datei sichergestellt, dass alle Komponenten in einem Ordner `MyCoolLayout`verwenden. Es ist nicht erforderlich, alle `@layout MyCoolLayout` *Razor* -Dateien im Ordner und in den Unterordnern wiederholt hinzuzufügen. `@using`Direktiven werden auch auf die gleiche Weise auf Komponenten angewendet.

Die folgenden *_Imports. Razor* -Datei wird importiert:

* `MyCoolLayout`
* Alle Razor-Komponenten im selben Ordner und in allen Unterordnern.
* Der `BlazorApp1.Data` -Namespace.
 
[!code-cshtml[](layouts/sample_snapshot/3.x/_Imports.razor)]

Die Datei *_Imports. Razor* ähnelt der [Datei _ViewImports. cshtml für Razor-Ansichten und-Seiten](xref:mvc/views/layout#importing-shared-directives) , die jedoch speziell auf Razor-Komponenten Dateien angewendet werden.

## <a name="nested-layouts"></a>In-Netz-Layouts

Apps können aus den in einem Netz gefügten Layouts bestehen. Eine Komponente kann auf ein Layout verweisen, das wiederum auf ein anderes Layout verweist. Schachtelungs Layouts werden z. b. verwendet, um eine Menüstruktur mit mehreren Ebenen zu erstellen.

Im folgenden Beispiel wird gezeigt, wie Sie die Verwendung von Netz Layouts durchführen. Die Datei " *episodescomponent. Razor* " ist die Komponente, die angezeigt werden soll. Die Komponente verweist auf `MasterListLayout`das:

[!code-cshtml[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

Die Datei *masterlistlayout. Razor* stellt den `MasterListLayout`bereit. Das Layout verweist auf ein anderes `MasterLayout`Layout,, in dem es gerendert wird. `EpisodesComponent`wird gerendert, wo `@Body` angezeigt wird

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

Schließlich enthält in Masterlayout. Razor die Layoutelemente der obersten Ebene, z. b. Header, Hauptmenü und Fußzeile. `MasterLayout` `MasterListLayout`mit wird gerendert `@Body` , wenn Folgendes angezeigt wird: `EpisodesComponent`

[!code-cshtml[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:mvc/views/layout>
