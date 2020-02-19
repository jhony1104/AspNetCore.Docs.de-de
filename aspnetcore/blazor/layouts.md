---
title: ASP.net Core Blazor Layouts
author: guardrex
description: Erfahren Sie, wie Sie wiederverwendbare Layoutkomponenten für Blazor Apps erstellen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/layouts
ms.openlocfilehash: 5b6e1c7ceb4a6e41230e31bbe379bde1bb0a8286
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447138"
---
# <a name="aspnet-core-opno-locblazor-layouts"></a>ASP.net Core Blazor Layouts

Von [Rainer stropek](https://www.timecockpit.com) und [Luke Latham](https://github.com/guardrex)

Einige APP-Elemente, z. b. Menüs, Copyright Meldungen und Firmenlogos, sind in der Regel Teil des gesamten Layouts der APP und werden von jeder Komponente in der APP verwendet. Das Kopieren des Codes dieser Elemente in alle Komponenten einer APP ist keine effiziente Vorgehensweise&mdash;jedes Mal, wenn eines der Elemente ein Update erfordert, muss jede Komponente aktualisiert werden. Eine solche Duplizierung ist schwierig zu verwalten und kann zu inkonsistenten Inhalten im Laufe der Zeit führen. *Layouts* lösen dieses Problem.

Technisch gesehen ist ein Layout nur eine andere Komponente. Ein Layout ist in einer Razor-Vorlage oder im C# Code definiert und kann [Datenbindung](xref:blazor/data-binding), [Abhängigkeitsinjektion](xref:blazor/dependency-injection)und andere Komponenten Szenarios verwenden.

Um eine *Komponente* in ein *Layout*umzuwandeln, ist die Komponente:

* Erbt von `LayoutComponentBase`, das eine `Body` Eigenschaft für den gerenderten Inhalt innerhalb des Layouts definiert.
* Verwendet die Razor-Syntax `@Body`, um den Speicherort im layoutmarkup anzugeben, an dem der Inhalt gerendert wird.

Das folgende Codebeispiel zeigt die Razor-Vorlage der Layoutkomponente *MainLayout. Razor*. Das Layout erbt `LayoutComponentBase` und legt die `@Body` zwischen der Navigationsleiste und der Fußzeile fest:

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

In einer APP, die auf einer der Blazor App-Vorlagen basiert, befindet sich die `MainLayout` Komponente (*MainLayout. Razor*) im frei *gegebenen* Ordner der app.

## <a name="default-layout"></a>Standardlayout

Geben Sie das standardmäßige App-Layout in der `Router`-Komponente in der *app. Razor* -Datei der APP an. Mit der folgenden `Router`-Komponente, die von den standardmäßigen Blazor Vorlagen bereitgestellt wird, wird das Standardlayout auf die `MainLayout`-Komponente festgelegt:

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

Um ein Standardlayout für `NotFound` Inhalt anzugeben, geben Sie eine `LayoutView` für `NotFound` Inhalt an:

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

Weitere Informationen zur `Router` Komponente finden Sie unter <xref:blazor/routing>.

Die Angabe des Layouts als Standardlayout im Router ist eine sinnvolle Vorgehensweise, da Sie für einzelne Komponenten oder Ordner pro Ordner überschrieben werden kann. Verwenden Sie den Router, um das Standardlayout der App festzulegen, da es sich hierbei um die allgemeinste Methode handelt.

## <a name="specify-a-layout-in-a-component"></a>Angeben eines Layouts in einer Komponente

Verwenden Sie die Razor-Direktive `@layout`, um ein Layout auf eine Komponente anzuwenden. Der Compiler konvertiert `@layout` in eine `LayoutAttribute`, die auf die Komponenten Klasse angewendet wird.

Der Inhalt der folgenden `MasterList` Komponente wird in die `MasterLayout` an der Position `@Body`eingefügt:

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

Wenn Sie das Layout direkt in einer Komponente angeben, wird ein *Standardlayout* im Router oder eine `@layout` Direktive, die aus *_Imports. Razor*importiert wurde, überschrieben.

## <a name="centralized-layout-selection"></a>Zentrale Layoutauswahl

Jeder Ordner einer APP kann optional eine Vorlagen Datei mit dem Namen *_Imports. Razor*enthalten. Der Compiler schließt die in der Imports-Datei angegebenen Direktiven in allen Razor-Vorlagen im gleichen Ordner und rekursiv in allen Unterordnern ein. Daher stellt eine *_Imports Razor* -Datei, die `@layout MyCoolLayout` enthält, sicher, dass alle Komponenten in einem Ordner `MyCoolLayout`verwenden. Es ist nicht mehr erforderlich, allen *Razor* -Dateien im Ordner und in den Unterordnern `@layout MyCoolLayout` wiederholt hinzuzufügen. `@using` Direktiven werden auch auf die gleiche Weise auf Komponenten angewendet.

Die folgenden *_Imports. Razor* -Datei Importe:

* [https://login.microsoftonline.com/consumers/](`MyCoolLayout`).
* Alle Razor-Komponenten im selben Ordner und in allen Unterordnern.
* Der `BlazorApp1.Data`-Namespace.
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

Die Datei " *_Imports. Razor* " ähnelt der Datei " [_ViewImports. cshtml" für Razor-Ansichten und-Seiten](xref:mvc/views/layout#importing-shared-directives) , die jedoch speziell auf Razor-Komponenten Dateien angewendet werden.

Durch das Angeben eines Layouts in *_Imports. Razor* wird ein als *Standardlayout*des Routers angegebenes Layout überschrieben.

## <a name="nested-layouts"></a>In-Netz-Layouts

Apps können aus den in einem Netz gefügten Layouts bestehen. Eine Komponente kann auf ein Layout verweisen, das wiederum auf ein anderes Layout verweist. Schachtelungs Layouts werden z. b. verwendet, um eine Menüstruktur mit mehreren Ebenen zu erstellen.

Im folgenden Beispiel wird gezeigt, wie Sie die Verwendung von Netz Layouts durchführen. Die Datei " *episodescomponent. Razor* " ist die Komponente, die angezeigt werden soll. Die Komponente verweist auf die `MasterListLayout`:

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

Die Datei *masterlistlayout. Razor* stellt die `MasterListLayout`bereit. Das Layout verweist auf ein anderes Layout, `MasterLayout`, wo es gerendert wird. `EpisodesComponent` wird gerendert, wenn `@Body` angezeigt wird:

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

Zum Schluss enthält `MasterLayout` in *Masterlayout. Razor* die Layoutelemente der obersten Ebene, z. b. Header, Hauptmenü und Fußzeile. `MasterListLayout` mit dem `EpisodesComponent` wird gerendert, wenn `@Body` angezeigt wird:

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-razor-pages-layout-with-integrated-components"></a>Freigeben eines Razor Pages Layouts mit integrierten Komponenten

Wenn Routing fähige Komponenten in eine Razor Pages-app integriert sind, kann das freigegebene Layout der APP mit den-Komponenten verwendet werden. Weitere Informationen finden Sie unter <xref:blazor/integrate-components>.

## <a name="additional-resources"></a>Weitere Ressourcen

* <xref:mvc/views/layout>
