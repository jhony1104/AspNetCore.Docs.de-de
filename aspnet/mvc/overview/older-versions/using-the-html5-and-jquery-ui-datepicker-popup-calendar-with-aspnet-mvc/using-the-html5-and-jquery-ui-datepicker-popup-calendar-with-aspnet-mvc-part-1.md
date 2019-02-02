---
uid: mvc/overview/older-versions/using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc/using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1
title: Verwenden des HTML5 und jQuery UI Datepicker-Popupkalenders mit ASP.NET MVC – Teil 1 | Microsoft-Dokumentation
author: Rick-Anderson
description: In diesem Tutorial lernen Sie die Grundlagen der Arbeit mit Editor-Vorlagen, Anzeigevorlagen und dem jQuery UI Datepicker-Popupkalenders in einer ASP.NET MV...
ms.author: riande
ms.date: 08/29/2011
ms.assetid: c23d27f7-b0cf-44f2-8445-fb69e045c674
msc.legacyurl: /mvc/overview/older-versions/using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc/using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1
msc.type: authoredcontent
ms.openlocfilehash: 3e700d2db4f86fe6734e2f08b01c9f8a8a69b6c3
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667790"
---
<a name="using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc---part-1"></a>Verwenden des HTML5 und jQuery UI Datepicker-Popupkalenders mit ASP.NET MVC – Teil 1
====================
durch [Rick Anderson]((https://twitter.com/RickAndMSFT))

> In diesem Tutorial lernen Sie die Grundlagen der Arbeit mit Editor-Vorlagen, Anzeigevorlagen und dem jQuery UI Datepicker-Popupkalenders in einer ASP.NET MVC-Webanwendung.


Dieses Tutorial vermittelt Ihnen die Grundlagen der Arbeit mit Editor-Vorlagen, Anzeigevorlagen und dem jQuery [UI Datepicker-Popupkalenders](http://plugins.jquery.com/project/datepicker) in einer ASP.NET MVC-Webanwendung. In diesem Tutorial können Sie Microsoft Visual Web Developer 2010 Express Service Pack 1 (&quot;Visual Web Developer&quot;), dies ist eine kostenlose Version von Microsoft Visual Studio oder Sie können Visual Studio 2010 SP1 verwenden, wenn bereits vorhanden.

Bevor Sie beginnen, stellen Sie sicher, dass Sie die unten aufgeführten erforderlichen Komponenten installiert haben. Sie können alle installieren, indem Sie auf den folgenden Link: [Webplattform-Installer](https://www.microsoft.com/web/gallery/install.aspx?appid=VWD2010SP1Pack). Alternativ können Sie einzeln die erforderliche Software, die über die folgenden Links installieren:

- [Visual Studio Web Developer Express SP1-Voraussetzungen](https://www.microsoft.com/web/gallery/install.aspx?appid=VWD2010SP1Pack)
- [ASP.NET MVC 3 Toolsupdate](https://www.microsoft.com/web/gallery/install.aspx?appsxml=&amp;appid=MVC3)
- [SQL Server Compact 4.0](https://www.microsoft.com/web/gallery/install.aspx?appid=SQLCE;SQLCEVSTools_4_0)(Common Language Runtime und Tools unterstützen)

Wenn Sie Visual Studio 2010 anstelle von Visual Web Developer verwenden, werden installieren Sie die erforderlichen Komponenten, indem Sie auf den folgenden Link: [Visual Studio 2010-Voraussetzungen](https://www.microsoft.com/web/gallery/install.aspx?appsxml=&amp;appid=VS2010SP1Pack).

In diesem Tutorial wird vorausgesetzt, Sie haben die [erste Schritte mit MVC 3](../getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md) Lernprogramm oder, dass Sie mit der ASP.NET MVC-Entwicklung vertraut sind. Dieses Tutorial beginnt mit das abgeschlossene Projekt aus der [erste Schritte mit MVC 3](../getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md) Tutorial.

Dieses Tutorial zeigt den Code in C# geschrieben. Allerdings die [Startprojekt](https://archive.msdn.microsoft.com/Project/Download/FileDownload.aspx?ProjectName=aspnetmvcsamples&amp;DownloadId=15800) und abgeschlossene Projekt sind auch in Visual Basic verfügbar.

Visual Studio-Projekt mit C# und Visual Basic-Quellcode ist zur Ergänzung dieses Themas verfügbar: [Herunterladen](https://archive.msdn.microsoft.com/Project/Download/FileDownload.aspx?ProjectName=aspnetmvcsamples&amp;DownloadId=15800).

### <a name="what-youll-build"></a>Sie lernen Folgendes

Fügen Sie Vorlagen (insbesondere bearbeiten und Anzeigen von Vorlagen) auf der einfachen Film-Auflistung-Anwendung, die erstellt wurde die [erste Schritte mit MVC 3](../getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md) Tutorial. Außerdem fügen Sie eine [jQuery UI Datepicker](http://jqueryui.com/demos/datepicker/) Popupkalenders zum Vereinfachen der Eingabe von Datumsangaben. Der folgende Screenshot zeigt die geänderte Anwendung mit dem jQuery UI Datepicker-Popupkalenders angezeigt.

![fertige jQuery](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1/_static/image1.png)

### <a name="skills-youll-learn"></a>Fähigkeiten, mit denen, die Sie lernen Folgendes

Hier ist Sie lernen Folgendes:

- Gewusst wie: Verwenden von Attributen aus dem ["DataAnnotations"](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx) Namespace, um das Format der Daten steuern, wenn er angezeigt wird und wenn sie befindet sich im Bearbeitungsmodus befindet.
- Erstellen von Vorlagen (bearbeiten und Anzeigen von Vorlagen) zum Steuern der Formatierung der Daten.
- Gewusst wie: Hinzufügen der [jQuery UI Datepicker](http://jqueryui.com/demos/datepicker/) als eine Möglichkeit zur Eingabe von Feldern.

### <a name="getting-started"></a>Erste Schritte

Wenn Sie bereits über die Auflistung von Film-Anwendung aus dem Startprojekt haben, laden Sie sie: 

* [Herunterladen](https://code.msdn.microsoft.com/Introduction-to-MVC-3-10d1b098).
* Windows-Explorer mit der Maustaste der *MvcMovie.zip* und wählen Sie **Eigenschaften**. 
* In der **MvcMovie.zip Eigenschaften** wählen Sie im Dialogfeld **Unblock**. (Aufheben der Sperre wird verhindert, dass eine sicherheitswarnung angezeigt, das auftritt, wenn Sie versuchen, eine *ZIP* -Datei, die Sie aus dem Web heruntergeladen haben.)

![](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1/_static/image2.png)

Mit der rechten Maustaste die *MvcMovie.zip* und wählen Sie **alle extrahieren** auf die Datei zu entpacken. Öffnen Sie in Visual Studio 2010 oder Visual Web Developer die *MvcMovieCS\_TU.sln* Datei.

In **Projektmappen-Explorer**, doppelklicken Sie auf die *Views\Shared\\"_Layout.cshtml"* um ihn zu öffnen. Ändern der `H1` Header **MVC-Filmapp** zu **Film jQuery**. Drücken Sie STRG + F5, um die Anwendung auszuführen, und klicken Sie auf die **Startseite** Registerkarte, die Sie verwendet die `Index` -Methode der Movie-Controller. Um die Anwendung testen, wählen Sie die **bearbeiten** Link und die **Details** Link für einen der Filme. Beachten Sie, dass in den Ansichten "Index", "Bearbeiten, und" Details ", das Veröffentlichungsdatum und Preis ordentlich formatiert sind:

![](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1/_static/image3.png)

Die Formatierung für das Datum und der Preis ist das Ergebnis der Verwendung der [DisplayFormat](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.displayformatattribute.aspx) Attribut für die Eigenschaften der `Movie` Klasse.

Öffnen der *Movie.cs* Datei, und kommentieren Sie die `DisplayFormat` -Attribut für die `ReleaseDate` und `Price` Eigenschaften. Die resultierende `Movie` Klasse sieht wie folgt aus:

[!code-csharp[Main](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1/samples/sample1.cs)]

Drücken Sie STRG + F5, um die Anwendung auszuführen, und wählen Sie die **Startseite** Registerkarte, um die Filmliste anzuzeigen. Dieses Mal das Datum der Veröffentlichung zeigt das Datum und die Uhrzeit und das Preisfeld zeigt nicht mehr das Währungssymbol. Die Änderung in der `Movie` Klasse wurde rückgängig gemacht, das sofort übersichtlicher formatiert, das Sie zuvor gesehen haben, aber Sie korrigieren dies in Kürze.

![](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1/_static/image4.png)

### <a name="using-the-dataannotations-datatype-attribute-to-specify-the-data-type"></a>Verwenden der "DataAnnotations" DataType-Attributs, um den Datentyp angeben

Ersetzen Sie die auskommentierten `DisplayFormat` -Attribut für die `ReleaseDate` Eigenschaft mit dem die [DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx) -Attributs unter Verwendung der `Date` Enumeration. Ersetzen der `DisplayFormat` -Attribut für die `Price` Eigenschaft mit dem die [DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx) Attribut erneut, dieses Mal mit der `Currency` Enumeration. Dies ist der fertige Code aussieht:

[!code-csharp[Main](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1/samples/sample2.cs)]

Führen Sie die Anwendung aus. Jetzt werden das Veröffentlichungsdatum und Preis Eigenschaften ordnungsgemäß formatiert (die entsprechenden Formate für Datum und Währung verwendet). Die [DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx) Attribut Typmetadaten für den integrierten ASP.NET MVC stellt Vorlagen bereit, damit die Felder im richtigen Format gerendert. Mithilfe der `DataType` Attribut empfiehlt sich, mit der `DisplayFormat` -Attribut, das ursprünglich in den Code, da war die `DataType` Attribut macht das Modell reiner und flexibler für Zwecke wie Internationalisierung.

Im nächsten Abschnitt sehen Sie, wie Sie benutzerdefinierte Vorlagen, die Datumsfelder angezeigt werden.

> [!div class="step-by-step"]
> [Nächste](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-2.md)
