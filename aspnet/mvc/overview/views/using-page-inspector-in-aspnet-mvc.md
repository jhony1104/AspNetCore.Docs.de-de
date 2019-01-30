---
uid: mvc/overview/views/using-page-inspector-in-aspnet-mvc
title: Verwenden der Seitenprüfung in ASP.NET MVC | Microsoft-Dokumentation
author: rick-anderson
description: Der Seitenprüfung in Visual Studio 2012 ist ein Webentwicklungstool mit einem integrierten Browser. Auswählen eines Elements in der integrierten Browser, und klicken Sie auf der Seitenprüfung i...
ms.author: riande
ms.date: 08/15/2012
ms.assetid: c7e4e1ab-4932-4614-9f53-aaf7c706d498
msc.legacyurl: /mvc/overview/views/using-page-inspector-in-aspnet-mvc
msc.type: authoredcontent
ms.openlocfilehash: 0dea8b077878139a3f513cb51447b86a93fe55b8
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236535"
---
<a name="using-page-inspector-in-aspnet-mvc"></a>Verwenden der Seitenprüfung in ASP.NET MVC
====================
von Tim Ammann

> Der Seitenprüfung in Visual Studio 2012 ist ein Webentwicklungstool mit einem integrierten Browser. Auswählen eines Elements in der integrierten Browser und der Seitenprüfung sofort hervorgehoben, Quell- und CSS-des-Elements. Sie können eine MVC-Ansicht navigieren, finden die Quellen der gerenderten Markup, und schnell Browsertools direkt in Visual Studio-Umgebung verwenden.
> 
> [Video ansehen](../../videos/mvc-4/using-page-inspector-in-aspnet-mvc.md)
> 
> Dieses Tutorial veranschaulicht, wie Überprüfungsmodus zu aktivieren und schnell suchen und Bearbeiten von Markup und CSS in Ihrer Webprojekt. Das Tutorial verwendet ein MVC-Projekt, aber Sie können auch die Seitenprüfung für [Web Forms](https://go.microsoft.com/?linkid=9802001) und anderen ASP.NET-Anwendungen.
> 
> Das Tutorial enthält die folgenden Abschnitte:
> 
> - [Erforderliche Komponenten](#_1_prerequisites)
> - [Erstellen einer Webanwendung](#_2_creating_a)
> - [Verwenden der Seitenprüfung, navigieren Sie zu einer Ansicht](#_3_using_page)
> - [Überprüfungsmodus zu aktivieren](#_4_inspection_mode)
> - [Verwenden der Seitenprüfung Markup ändern](#_5_using_page)
> - [Überprüfungsmodus und HTML-Fenster](#_6_inspection_mode)
> - [Vorschau der CSS-Änderungen im Fenster Stile](#_7_previewing_css)
> - [CSS Auto Sync](#css_auto_sync)
> - [Mithilfe der CSS-Farbauswahl](#css_color_picker)
> - [Zuordnen von dynamische Seitenelemente zu JavaScript](#map_dynamic_elements)


<a id="_prerequisites"></a><a id="_1_prerequisites"></a>

## <a name="prerequisites"></a>Vorraussetzungen

- [Visual Studio 2012](https://www.microsoft.com/visualstudio/11) oder [Visual Studio Express 2012 für Web](https://www.microsoft.com/visualstudio/11/downloads#express-web).

> [!NOTE]
> Rufen Sie die neueste Version der Seitenprüfung mit [Webplattform-Installer](https://go.microsoft.com/fwlink/?LinkId=255386) Windows Azure SDK für .NET 2.0 zu installieren.


Die Seitenprüfung ist Microsoft Web Developer Tools enthalten. Die neueste Version ist 1.3. Um die Version haben Sie, Visual Studio ausführen, und wählen Sie **Info zu Microsoft Visual Studio** aus der **Hilfe** Menü.

<a id="_creating_a_web"></a><a id="_2_creating_a"></a>

## <a name="create-a-web-application"></a>Erstellen einer Webanwendung

Erstellen Sie zunächst eine Webanwendung, der Seitenprüfung mit verwendet werden. Wählen Sie in Visual Studio **Datei** &gt; **neues Projekt**. Erweitern Sie auf der linken Seite **Visual C#-** Option **Web**, und wählen Sie dann **ASP.NET MVC 4-Webanwendung**.

![Neue ASP.NET MVC-Anwendung](using-page-inspector-in-aspnet-mvc/_static/image2.png)

Klicken Sie auf **OK**.

In der **neues ASP.NET MVC 4-Projekt** wählen Sie im Dialogfeld **Internetanwendung**. Lassen Sie **Razor** als die standardansichts-Engine.

![Neues ASP.NET MVC-Projekt – Internetanwendung](using-page-inspector-in-aspnet-mvc/_static/image4.png)

Öffnet die Anwendung in **Quelle** anzeigen.

![Neue ASP.NET MVC-Anwendung in der Quellansicht](using-page-inspector-in-aspnet-mvc/_static/image6.png)

Nun, da Sie eine Anwendung gearbeitet haben, können Sie der Seitenprüfung verwenden, überprüfen und ändern Sie sie.

<a id="_starting_page_inspector"></a><a id="_3_using_page"></a>

## <a name="use-page-inspector-to-browse-to-a-view"></a>Verwenden der Seitenprüfung, navigieren Sie zu einer Ansicht

In Visual Studio 2012, Sie können mit der rechten Maustaste einer beliebigen Ansicht in das Projekt, wählen **in Seitenprüfung anzeigen**, Page Inspector wird ermitteln, die Route und die Seite angezeigt.

In **Projektmappen-Explorer**, erweitern Sie die **Ansichten** Ordner und klicken Sie dann die **Startseite** Ordner. Klicken Sie mit der rechten Maustaste auf die Datei "Index.cshtml", und wählen Sie **in Seitenprüfung anzeigen**.

![Ansicht "Index.cshtml" in der Seitenprüfung](using-page-inspector-in-aspnet-mvc/_static/image8.png)

Standardmäßig wird der Seitenprüfung auf der linken Seite der Visual Studio-Umgebung als Fenster angedockt. Falls gewünscht, können Sie an anderer Stelle andocken, oder lösen das Fenster. Weitere Informationen finden Sie unter [How to: Arrange and Dock Windows (Vorgehensweise: Anordnen und Andocken von Fenstern)](https://msdn.microsoft.com/library/z4y0hsax.aspx).

Im obere Bereich des Fensters die Seitenprüfung zeigt die aktuelle Seite in einem Browserfenster angezeigt. Im unteren Bereich zeigt die Seite im HTML-Markup verwendet werden, sowie einige Registerkarten, mit die Sie verschiedene Aspekte der Seite untersuchen können. Der untere Bereich ist ähnlich wie die [F12-Entwicklertools](https://msdn.microsoft.com/ie/aa740478) in Internet Explorer.

![ASP.NET MVC-Anwendung in der Seitenprüfung](using-page-inspector-in-aspnet-mvc/_static/image10.png)

In diesem Tutorial verwenden Sie die **HTML** und **Stile** Registerkarten, navigieren schnell, und nehmen Sie Änderungen an der Anwendung.

<a id="_examining_(&quot;decomposing&quot;)_the"></a><a id="_inspection_mode_and"></a><a id="_4_inspection_mode"></a>

## <a name="enableinspection-mode"></a>EnableInspection-Modus

Um die Seitenprüfung in den Überprüfungsmodus zu versetzen, klicken Sie auf die **prüfen** Schaltfläche. Bei aktiviertem Überprüfungsmodus im Wenn Sie den Mauszeiger über einen beliebigen Teil der gerenderten Seite halten wird das entsprechende Quellmarkup oder der Code hervorgehoben.

![Überprüfungsmodus umschalten](using-page-inspector-in-aspnet-mvc/_static/image12.png)

Jetzt können bewegen Sie die Maus über verschiedene Teile der Seite innerhalb der Seitenprüfung. Wie Sie dies tun, wird der Mauszeiger ändert sich zu einem großen Pluszeichen, und untergeordnete Element wird hervorgehoben:

![Mit dem Mauszeiger auf div.content-Wrappers](using-page-inspector-in-aspnet-mvc/_static/image14.png)

Wenn Sie den Mauszeiger bewegen, werden Visual Studio die entsprechende Razor-Syntax in der Quelldatei hervorgehoben. Wenn das HTML-Element aus einer anderen Quelldatei stammt, wird Visual Studio automatisch die Datei geöffnet.

In der Seitenprüfung die **HTML** Registerkarte zeigt den HTML-Code, der von der Razor-Syntax generiert wurde. Wenn Sie den Mauszeiger bewegen, werden die HTML-Elemente hervorgehoben. Die **Stile** Registerkarte zeigt die CSS-Regeln für das Element.

<a id="_5_using_page"></a>

## <a name="use-page-inspector-to-make-changes-to-markup"></a>Verwenden der Seitenprüfung Markup ändern

Der Seitenprüfung können Sie Markup finden, deren Speicherort möglicherweise nicht offensichtlich. Sie können dann ändern Sie das Markup und finden Sie die Änderungen.

Um dies zu sehen, klicken Sie auf **prüfen** und führen Sie einen Bildlauf zum unteren Rand der Seite im Fenster der Seitenprüfung.

Wenn Sie den Mauszeiger in den Footerbereich verschieben, wird die Seitenprüfung öffnet die \_Layout.cshtml-Datei und hebt Sie im Abschnitt der Layoutseite, die Sie ausgewählt haben. Wie Sie sehen können, sind die Fußzeile in die Layoutdatei und nicht die Ansicht selbst definiert ist.

![Fußzeile](using-page-inspector-in-aspnet-mvc/_static/image16.png)

Verschieben Sie den Mauszeiger jetzt auf die Zeile mit dem Copyright <a id="a"> </a>beachten. In der \_Layout.cshtml-Seite, die entsprechende Zeile wird hervorgehoben.

![Hervorgehobene Zeile der Fußzeile-copyright](using-page-inspector-in-aspnet-mvc/_static/image18.png)

Hinzufügen von Text an das Ende der Zeile in der \_Layout.cshtml-Datei.

&lt;p&gt;&amp;kopieren; @DateTime.Now.Year -Meine ASP.NET MVC-Anwendung um eine gelungene!  &lt; /p&gt;

Jetzt drücken Sie Strg + Alt + Eingabe, oder klicken Sie auf die Update-Leiste zum Anzeigen der Ergebnisse im Fenster Browser der Seitenprüfung.

![Meine ASP.NET Application Rocks!](using-page-inspector-in-aspnet-mvc/_static/image20.png)

Sie können daran gedacht, dass die Fußzeile in die Datei "Index.cshtml" definiert, aber es stellte sich heraus in der \_Layout.cshtml, und die Seitenprüfung, die sie für Sie gefunden.

<a id="_inspection_mode_and_1"></a><a id="_6_inspection_mode"></a>

## <a name="inspection-mode-and-the-html-window"></a>Überprüfungsmodus und HTML-Fenster

Als Nächstes müssen Sie einen kurzen Blick auf die HTML-Fenster, und wie sie Elemente für Sie zugeordnet.

Klicken Sie auf **prüfen** der Seitenprüfung im Überprüfungsmodus zu platzieren.

Klicken Sie auf den oberen Teil der Seite die Fehlermeldung "Ihre Logohere". Untersuchen Sie ein bestimmtes Element im Detail, damit die Anzeige im Browserfenster nicht mehr geändert werden, wie Sie den Mauszeiger bewegen.

Nun verschieben Sie den Mauszeiger an die **HTML** Fenster. Während Sie den Mauszeiger bewegen, wird der Seitenprüfung beschrieben, das Element innerhalb der **HTML** Fenster und das entsprechende Element im Browserfenster hervorgehoben.

![HTML-Fenster](using-page-inspector-in-aspnet-mvc/_static/image22.png)

Wie zuvor die Seitenprüfung öffnet die \_Layout.cshtml-Datei für Sie in eine temporäre Registerkarte. Klicken Sie auf die \_Layout.cshtml temporäre Registerkarte, und das entsprechende Markup markiert die &lt;Header&gt; Abschnitt für Sie:

![Hervorgehobene markup](using-page-inspector-in-aspnet-mvc/_static/image24.png)

<a id="_using_page_inspector"></a><a id="_7_previewing_css"></a>

## <a name="preview-css-changes-in-the-styles-window"></a>Vorschau der CSS-Änderungen im Fenster Stile

Als Nächstes verwenden Sie die Seitenprüfung **Stile** Fenster Vorschau der Änderungen an CSS.

Klicken Sie auf **prüfen** der Seitenprüfung im Überprüfungsmodus zu platzieren.

Verschieben Sie im Fenster Browser der Seitenprüfung den Mauszeiger über den Abschnitt "Startseite", bis die **div.content-Wrappers** Bezeichnung angezeigt wird.

![Mit dem Mauszeiger auf div.content-Wrappers](using-page-inspector-in-aspnet-mvc/_static/image26.png)

Klicken Sie im Abschnitt div.content-Wrappers einmal, und verschieben Sie den Mauszeiger an die **Stile** Fenster. Die **Stile** Fenster zeigt alle CSS-Regeln für dieses Element. Scrollen Sie zur Klassenselektor Suchen der .featured .content-Wrapper. Deaktivieren Sie nun das Kontrollkästchen für die Background-Color-Eigenschaft.

![Clear-Hintergrundfarbe](using-page-inspector-in-aspnet-mvc/_static/image28.png)

Beachten Sie, wie die Änderung sofort im Browser der Seitenprüfung-Fenster zeigt in der Vorschau.

Wählen Sie das Kontrollkästchen erneut aus, doppelklicken Sie auf den Wert der Eigenschaft, und ändern Sie es sich in Rot. Die Änderung wird sofort aus:

![Rote Hintergrundfarbe](using-page-inspector-in-aspnet-mvc/_static/image30.png)

Die **Stile** Fenster macht es einfach, test und Vorschau CSS Änderungen erforderlich, bevor die Änderungen zu, um den Stil übernehmen Stylesheet selbst.

<a id="css_auto_sync"></a>
## <a name="css-auto-sync"></a>Automatische CSS-Synchronisierung

> [!NOTE]
> Dieses Feature erfordert Version 1.3 der Seitenprüfung.


Das Feature für die automatische CSS-Synchronisierung können Sie eine CSS-Datei direkt bearbeiten und die Änderungen sofort im Browser Seitenprüfung.

Klicken Sie auf **prüfen** der Seitenprüfung im Überprüfungsmodus zu platzieren.

Im Browser der Seitenprüfung verschieben Sie den Mauszeiger über den Abschnitt "Startseite", bis die **div.content-Wrappers** Bezeichnung angezeigt wird. Klicken Sie auf einmal, um dieses Element auszuwählen.

Die **Stile** Fenster zeigt alle CSS-Regeln für dieses Element. Scrollen Sie zur Klassenselektor Suchen der .featured .content-Wrapper. Klicken Sie auf ".featured .content-Wrapper". Seitenprüfung öffnet die CSS-Datei, die dieses Format ("Site.CSS") definiert und hebt den entsprechenden CSS-Stil.

![](using-page-inspector-in-aspnet-mvc/_static/image32.png)

Ändern Sie nun den Wert für `background-color` auf "Rot". Die Änderung wird sofort im Browser Seitenprüfung angezeigt.

![](using-page-inspector-in-aspnet-mvc/_static/image34.png)

<a id="css_color_picker"></a>
## <a name="using-the-css-color-picker"></a>Mithilfe der CSS-Farbauswahl

CSS-Editor in Visual Studio 2012 verfügt über eine Farbauswahl angezeigt, die ganz einfach auswählen, und fügen Sie Farben. Die Farbauswahl enthält eine Standardpalette mit Farben, unterstützt standardfarbnamen, Hashcodes, RGB-, RGBA-, HSL- und HSLA-Farben und verwaltet eine Liste der Farben, die Sie zuletzt in das Dokument verwendet haben.

Im vorherigen Abschnitt haben Sie den Wert geändert der `background-color` Eigenschaft. Um die Farbauswahl aufzurufen, platzieren Sie die Einfügemarke nach dem Eigenschaftennamen und den Typ **#** oder **Rgb (**.

![Die CSS-Auswahl Farbleiste](using-page-inspector-in-aspnet-mvc/_static/image36.png)

Klicken Sie auf eine Farbe auszuwählen, oder drücken die nach-unten-Taste, und klicken Sie dann verwenden Sie die linke und Rechte Pfeiltasten, um die Farben zu durchlaufen. Wenn Sie eine Farbe besuchen, wird der entsprechende Hexadezimalwert in der Vorschau angezeigt:

![Background-Color-Wert der Eigenschaft, die in der Vorschau angezeigt](using-page-inspector-in-aspnet-mvc/_static/image38.png)

Wenn der Farbleiste nicht den gewünschten genaue Farbe haben, können Sie der Farbe Auswahl Pop aus. Um es zu öffnen, klicken Sie auf die Chevron-Doppelpfeil am rechten Ende der Farbleiste, oder ein-oder zweimal drücken Sie den Pfeil nach unten auf der Tastatur.

![CSS-Farbe Auswahl Pop-ab](using-page-inspector-in-aspnet-mvc/_static/image40.png)

Klicken Sie auf eine Farbe aus der senkrechte Strich auf der rechten Seite. Dies zeigt einen Farbverlauf für diese Farbe im Hauptfenster. Wählen Sie eine Farbe direkt aus der senkrechte Strich durch Drücken der EINGABETASTE, oder klicken Sie auf einem beliebigen Zeitpunkt im Hauptfenster mit größerer Genauigkeit zu wählen.

Wenn eine Farbe vorliegt, auf dem Bildschirm, die Sie verwenden möchten (es muss keine werden innerhalb der Visual Studio-Benutzeroberfläche), Sie können den Wert erfassen, indem Sie mit dem Tool Pipette in der unteren rechten Ecke.

Sie können auch die Deckkraft einer Farbe ändern, indem Sie den Schieberegler am unteren Rand der Farbauswahl. Auf diese Weise Änderungen Farbe, die Werte in RGBA-Werte, da das RGBA-Format Deckkraft darstellen kann.

Nachdem Sie eine Farbe ausgewählt haben, drücken Sie die EINGABETASTE, und geben Sie ein Semikolon zum Abschließen des Background-Color-Eintrags in der *"Site.CSS"* Datei.

<a id="_the_update_bar"></a>

### <a name="the-page-inspector-update-bar"></a>Die Seite Inspector-Update-Leiste

Die Seitenprüfung sofort erkennt die Änderung an der *"Site.CSS"* Datei und zeigt eine Warnung in eine Update-Leiste.

![Update-Leiste](using-page-inspector-in-aspnet-mvc/_static/image42.png)

Speichern Sie alle Dateien und den Browser der Seitenprüfung zu aktualisieren, drücken Strg + Alt + Eingabe, oder klicken Sie auf die Update-Leiste. Die Änderung in die Hervorhebungsfarbe wird im Browser angezeigt.

<a id="map_dynamic_elements"></a>
## <a name="mapping-dynamic-page-elements-to-javascript"></a>Zuordnen von dynamische Seitenelemente zu JavaScript

Bei modernen Webanwendungen werden die Elemente auf der Seite häufig dynamisch mit JavaScript generiert. Das bedeutet, dass es keine statischen Markup (HTML oder Razor), das die diese Elemente entspricht.

Mit Version 1.3 können die Seitenprüfung Elemente zuordnen, die die Seite wieder an die entsprechenden JavaScript-Code dynamisch hinzugefügt wurden. Um dieses Feature zu demonstrieren, verwenden wir die [Single-Page-Anwendung (SPA) Vorlage](../../../single-page-application/overview/introduction/knockoutjs-template.md).

> [!NOTE]
> Die SPA-Vorlage erfordert die [ASP.NET- und Web Tools 2012.2](https://go.microsoft.com/fwlink/?LinkId=282650) aktualisieren.


Wählen Sie in Visual Studio **Datei** &gt; **neues Projekt**. Erweitern Sie auf der linken Seite **Visual C#-** Option **Web**, und wählen Sie dann **ASP.NET MVC 4-Webanwendung**. Klicken Sie auf **OK**.

In der **neues ASP.NET MVC 4-Projekt** wählen Sie im Dialogfeld **Single Page Application**.

Erweitern Sie im Projektmappen-Explorer die **Ansichten** Ordner und klicken Sie dann die **Startseite** Ordner. Klicken Sie mit der rechten Maustaste auf die Datei "Index.cshtml", und wählen Sie **in Seitenprüfung anzeigen**.

Das erste angezeigte im Browser Seitenprüfung ist eine Anmeldeseite. Klicken Sie auf "Registrieren", und erstellen Sie einen Benutzernamen und Kennwort. Nachdem Sie haben registriert, wird die Anwendung meldet an und erstellt eine to-Do-Liste mit einige Beispiel-Elemente.

Klicken Sie auf **prüfen** der Seitenprüfung im Überprüfungsmodus zu platzieren. Die Browser der Seitenprüfung klicken Sie auf eines der to-do-Elemente. Beachten Sie, dass das Element anstelle von blau hervorgehoben wird, neben dem Elementnamen in orange dargestellt, die mit "JS" hervorgehoben ist. Dies gibt an, dass das Element dynamisch über ein Skript erstellt wurde.

![](using-page-inspector-in-aspnet-mvc/_static/image44.png)

Darüber hinaus eine orangefarbene Unterstreichung wird angezeigt, auf die **Aufrufliste** Registerkarte. Dies bedeutet, dass die **Aufrufliste** Bereich enthält weitere Informationen über das Element.

Klicken Sie auf die **Aufrufliste** Registerkarte. Die **Aufrufliste** Bereich zeigt die Aufrufliste für den JavaScript-Aufruf, der das Element erstellt. Aufrufe von externen Bibliotheken wie jQuery reduziert, damit Sie die Aufrufe an das Anwendungsskript leicht erkennen können.

![](using-page-inspector-in-aspnet-mvc/_static/image46.png)

Um den vollständigen Stapel, einschließlich Aufrufe von externen Bibliotheken finden Sie unter können Sie die Knoten mit der Bezeichnung "Externe Bibliotheken" erweitern:

![](using-page-inspector-in-aspnet-mvc/_static/image48.png)

Wenn Sie ein Element in der Aufrufliste klicken, wird Visual Studio öffnet die Codedatei, und das entsprechende Skript hervorgehoben.

![](using-page-inspector-in-aspnet-mvc/_static/image50.png)

## <a name="see-also"></a>Siehe auch

[Einführung in ASP.NET MVC 4 mit Visual Studio](../older-versions/getting-started-with-aspnet-mvc4/intro-to-aspnet-mvc-4.md) (ASP.NET-Website)

[Einführung in der Seitenprüfung](https://channel9.msdn.com/posts/visual-studio-vnext-introducing-page-inspector/) (Channel 9-Video)

[Fehlermeldungen der Seitenprüfung](https://go.microsoft.com/?linkid=9813062) (MSDN)
