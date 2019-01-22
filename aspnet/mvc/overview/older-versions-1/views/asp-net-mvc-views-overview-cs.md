---
uid: mvc/overview/older-versions-1/views/asp-net-mvc-views-overview-cs
title: ASP.NET MVC-Ansichten – Übersicht (c#) | Microsoft-Dokumentation
author: StephenWalther
description: Was ist eine ASP.NET MVC-Ansicht, und wie unterscheidet es sich von einem HTML-Seite? In diesem Tutorial Stephen Walther bietet eine Einführung zu Ansichten und veranschaulicht, wie Sie t...
ms.author: riande
ms.date: 02/16/2008
ms.assetid: 152ab1e5-aec2-4ea7-b8cc-27a24dd9acb8
msc.legacyurl: /mvc/overview/older-versions-1/views/asp-net-mvc-views-overview-cs
msc.type: authoredcontent
ms.openlocfilehash: a8e64a99549584f150d64d909ac97210257b1147
ms.sourcegitcommit: 728f4e47be91e1c87bb7c0041734191b5f5c6da3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54444128"
---
<a name="aspnet-mvc-views-overview-c"></a>ASP.NET MVC-Ansichten – Übersicht (c#)
====================
durch [Stephen Walther](https://github.com/StephenWalther)

> Was ist eine ASP.NET MVC-Ansicht, und wie unterscheidet es sich von einem HTML-Seite? In diesem Tutorial Stephen Walther bietet eine Einführung zu Ansichten und veranschaulicht, wie Sie Daten anzeigen und HTML-Hilfsprogramme in einer Ansicht nutzen können.


Der Zweck dieses Lernprogramms ist eine kurze Einführung in ASP.NET MVC-Ansichten, Anzeigen von Daten und HTML-Hilfsprogramme bereit. Am Ende dieses Tutorials sollten Sie verstehen, wie Sie neue Ansichten erstellen, Daten von einem Controller an eine Ansicht übergeben und HTML-Hilfsprogramme verwenden, um Inhalte in einer Ansicht zu generieren.

## <a name="understanding-views"></a>Grundlegendes zu Sichten

Für ASP.NET oder Active Server Pages enthält ASP.NET MVC nicht alles, der direkt auf eine Seite entspricht. In einer ASP.NET MVC-Anwendung besteht keine Seite auf dem Datenträger, der den Pfad in der URL entspricht, die Sie in die Adressleiste des Browsers eingeben. Am ehesten mit einer Seite in einer ASP.NET MVC-Anwendung ist etwas bezeichnet ein *Ansicht*.

In einer ASP.NET MVC-Anwendung werden die eingehenden Browseranforderungen zu Controlleraktionen zugeordnet. Eine Controlleraktion kann es sich um eine Ansicht zurückgeben. Eine Controlleraktion kann jedoch eine andere Art von Aktion, z. B. umleiten Sie zu einer anderen Controlleraktion durchführen.

Codebeispiel 1 enthält einen einfachen Controller, mit dem Namen HomeController. HomeController macht zwei Controller-Aktionen, die mit dem Namen Index() und Details() verfügbar.

**Codebeispiel 1 - Datei "HomeController.cs"**

[!code-csharp[Main](asp-net-mvc-views-overview-cs/samples/sample1.cs)]

Sie können die erste Aktion, die Aktion Index() aufrufen, indem Sie die folgende URL in die Adressleiste Ihres Browsers eingeben:

/ Home/Index

Sie können die zweite Aktion, die Aktion Details() aufrufen, indem Sie diese Adresse in Ihren Browser eingeben:

/ Home/Details

Die Index()-Aktion gibt eine Ansicht zurück. Die meisten Aktionen, die Sie erstellen werden Sichten zurückgegeben. Sie können jedoch eine Aktion andere Arten von Aktionsergebnissen zurückgeben. Beispielsweise gibt die Details()-Aktion ein, die eingehende Anforderung an die Aktion Index() leitet RedirectToActionResult zurück.

Die Aktion Index() enthält die folgende Codezeile:

View();

Diese Codezeile gibt es sich um eine Ansicht, die sich unter folgendem Pfad auf dem Webserver sein müssen:

\Views\Home\Index.aspx

Der Pfad für die Ansicht wird von den Namen des Controllers und der Name der Controlleraktion abgeleitet.

Falls gewünscht, können Sie über die Ansicht explizite sein. Die folgende Codezeile gibt eine Ansicht namens Fred zurück:

Ansicht (Fred);

Wenn diese Codezeile ausgeführt wird, wird eine Ansicht aus dem folgenden Pfad zurückgegeben:

\Views\Home\Fred.aspx

> [!NOTE] 
> 
> Wenn Sie planen, Erstellen von Komponententests für Ihre ASP.NET MVC-Anwendung ist es eine gute Idee, die explizit über den Sichtnamen sein. Auf diese Weise können Sie einen Komponententest, um sicherzustellen, dass die erwarteten Ansicht durch eine Controlleraktion zurückgegeben wurde erstellen.


## <a name="adding-content-to-a-view"></a>Hinzufügen von Inhalten zu einer Ansicht

Eine Sicht ist ein Standard (HTML-Dokument, das Skripts enthalten kann X). Sie können Skripts verwenden, um dynamischen Inhalt an eine Ansicht hinzuzufügen.

Beispielsweise zeigt die Ansicht im Codebeispiel 2 an das aktuelle Datum und die Uhrzeit.

**Codebeispiel 2 - \Views\Home\Index.aspx**

[!code-aspx[Main](asp-net-mvc-views-overview-cs/samples/sample2.aspx)]

Beachten Sie, dass der Text der HTML-Seite in Liste 2 das folgende Skript enthält:

&lt;"% Response.Write(DateTime.Now);"&gt;

Verwenden Sie die Skript-Trennzeichen &lt;% "und"&gt; Anfang und Ende eines Skripts zu markieren. Dieses Skript ist in c# geschrieben. Es zeigt das aktuelle Datum und die Uhrzeit durch Aufrufen der Response.Write()-Methode, um Inhalt an den Browser zu rendern. Die Skript-Trennzeichen &lt;% "und"&gt; können verwendet werden, um eine oder mehrere Anweisungen ausführen.

Da Sie so oft Response.Write() aufrufen, bietet Microsoft eine Verknüpfung zum Aufrufen der Response.Write()-Methode. Die Ansicht in Programmausdruck 3 verwendet die Trennzeichen &lt;% = "und"&gt; als Abkürzung für Response.Write() aufrufen.

**Codebeispiel 3 - Views\Home\Index2.aspx**

[!code-aspx[Main](asp-net-mvc-views-overview-cs/samples/sample3.aspx)]

Sie können jeder verwenden, um dynamischen Inhalt in einer Ansicht zu generieren. Normalerweise verwenden Sie entweder Visual Basic .NET oder C# , Controllern und Ansichten zu schreiben.

## <a name="using-html-helpers-to-generate-view-content"></a>Verwenden von HTML-Hilfsmethoden zum Generieren von Inhalt anzeigen

Zum Hinzufügen von Inhalt an eine Ansicht zu vereinfachen, können Sie nutzen der so genannte ein *HTML-Hilfsobjekt*. Ein HTML-Hilfsobjekt, ist in der Regel eine Methode, die eine Zeichenfolge generiert. Sie können HTML-Hilfsprogramme verwenden, um standard-HTML-Elemente wie z. B. Textfelder, Links, Dropdownlisten und Listenfelder zu generieren.

Bilden beispielsweise die Ansicht in Listing 4 nutzt drei HTML-Hilfsprogrammen – Hilfsprogramme BeginForm(), TextBox() und Password() – um eine Anmeldung zu generieren (siehe Abbildung 1).

**Programmausdruck 4 – \Views\Home\Login.aspx**

[!code-aspx[Main](asp-net-mvc-views-overview-cs/samples/sample4.aspx)]


[![Das Dialogfeld "Neues Projekt"](asp-net-mvc-views-overview-cs/_static/image1.jpg)](asp-net-mvc-views-overview-cs/_static/image1.png)

**Abbildung 01**: Ein standard Anmeldeformular ([klicken Sie, um das Bild in voller Größe anzeigen](asp-net-mvc-views-overview-cs/_static/image2.png))


Alle HTML-Hilfsprogramme Methoden werden in der Eigenschaft Html der Ansicht aufgerufen. Rendern Sie z. B. ein Textfeld durch Aufrufen der Html.TextBox()-Methode.

Beachten Sie, dass Sie die Skript-Trennzeichen verwenden &lt;% = "und"&gt; beim Aufrufen von Html.TextBox() sowohl Html.Password() Hilfsprogramme. Diese Hilfsprogramme werden einfach eine Zeichenfolge zurückgeben. Sie müssen Response.Write() aufrufen, um die Zeichenfolge an den Browser zu rendern.

Verwendung von HTML-Hilfsmethoden ist optional. Sie erleichtern sich das Leben durch Verringern der Menge von HTML und Skripts, die Sie schreiben müssen. Die Ansicht in Listing 5 wird genaue dasselbe Format wie die Ansicht in Listing 4 ohne Verwendung von HTML-Hilfsprogramme gerendert.

**Programmausdruck 5 – \Views\Home\Login.aspx**

[!code-aspx[Main](asp-net-mvc-views-overview-cs/samples/sample5.aspx)]

Sie können auch das Erstellen eigener HTML-Hilfsprogrammen. Beispielsweise können Sie eine Hilfsmethode GridView() erstellen, die einen Satz von Datenbank-Datensätzen in einer HTML-Tabelle automatisch anzeigt. Erläutert, in diesem Thema im Lernprogramm **Erstellen von benutzerdefinierten HTML-Hilfsprogrammen**.

## <a name="using-view-data-to-pass-data-to-a-view"></a>Anzeigen von Daten verwenden, um Daten an eine Ansicht übergeben

Sie verwenden die Anzeigedaten, Daten von einem Controller an eine Ansicht zu übergeben. Stellen Sie Anzeigedaten, wie ein Paket, die Sie über die e-Mail zu senden. Alle Daten, die von einem Controller an eine Ansicht übergeben müssen mit diesem Paket gesendet werden. Der Controller im Codebeispiel 6 fügt beispielsweise eine Nachricht zum Anzeigen von Daten.

**Codebeispiel 6: ProductController.cs**

[!code-csharp[Main](asp-net-mvc-views-overview-cs/samples/sample6.cs)]

Der Controller die ViewData-Eigenschaft stellt eine Auflistung von Name-Wert-Paaren dar. Auflisten von 6 wurde der Erfassung von Ansicht namens "Message" mit dem Hello World-Wert die Index()-Methode ein Element hinzugefügt. Wenn die Ansicht von der Index()-Methode zurückgegeben wird, werden die Anzeigen von Daten automatisch an die Ansicht übergeben.

Die Ansicht im Codebeispiel 7 Ruft die Nachricht von den Daten ab und rendert die Nachricht an den Browser.

**Codebeispiel 7: \Views\Product\Index.aspx**

[!code-aspx[Main](asp-net-mvc-views-overview-cs/samples/sample7.aspx)]

Beachten Sie, dass die Ansicht die Html.Encode() HTML-Hilfsmethode beim Rendern der Nachricht nutzt. Das HTML-Hilfsobjekt Html.Encode() codiert Sonderzeichen wie z. B. &lt; und &gt; in Zeichen, die sicher auf einer Webseite angezeigt werden. Wenn Sie Inhalt, die ein Benutzer auf einer Website sendet rendern, sollten Sie den Inhalt, der JavaScript-Injection-Angriffe zu verhindern, dass codieren.

(Da wir die Nachricht selbst im ProductController erstellt haben, müssen wir unbedingt auf die Meldung zu codieren. Es ist jedoch ein sollte zur guten Gewohnheit, immer die Html.Encode()-Methode aufgerufen wird, bei der Anzeige von Inhalten aus der sichtdaten in einer Sicht abgerufen.)

Auflisten von 7 haben wir nutzen Anzeigedaten, eine einfache Zeichenfolge-Nachricht anhand eines Controllers zu einer Ansicht zu übergeben. Sie können auch anzeigen von Daten verwenden, um andere Arten von Daten, z. B. eine Auflistung von Datenbank-Datensätzen von einem Controller an eine Ansicht zu übergeben. Beispielsweise sollten Sie den Inhalt der Datenbank Products-Tabelle in einer Sicht, zeigen Sie die Auflistung der Datenbank übergeben würde Datensätze in der Ansicht Daten.

Sie können auch die stark typisierte Ansichtsdaten anhand eines Controllers zu einer Ansicht zu übergeben. Erläutert, in diesem Thema im Lernprogramm **Grundlegendes zu stark typisierten Daten anzeigen und Ansichten**.

## <a name="summary"></a>Zusammenfassung

In diesem Tutorial bereitgestellt, eine kurze Einführung in ASP.NET MVC-Ansichten, Anzeigen von Daten und HTML-Hilfsprogramme. Im ersten Abschnitt haben Sie gelernt, wie neue Ansichten zu Ihrem Projekt hinzufügen. Sie haben gelernt, dass Sie eine Ansicht und den richtigen Ordner hinzufügen, um über einen bestimmten Controller aufrufen. Als Nächstes erläutert das Thema des HTML-Hilfsprogramme. Sie erfahren, wie HTML-Hilfsprogramme standard-HTML-Inhalt einfach erstellen können. Schließlich haben Sie Gewusst wie: Anzeigen von Daten zur Datenübergabe von einem Controller zu einer Ansicht nutzen.

> [!div class="step-by-step"]
> [Nächste](creating-custom-html-helpers-cs.md)
