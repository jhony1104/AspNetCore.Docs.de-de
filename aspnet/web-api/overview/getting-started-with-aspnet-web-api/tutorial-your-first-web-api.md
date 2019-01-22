---
uid: web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api
title: Erste Schritte mit ASP.NET-Web-API 2 (c#)
author: MikeWasson
description: HTTP ist nicht nur für Webseiten bereitstellt. Es ist auch eine leistungsstarke Plattform zum Erstellen von APIs, Dienste und Daten verfügbar machen. HTTP ist einfach, flexibel und Ubiq...
ms.author: riande
ms.date: 11/28/2017
msc.legacyurl: /web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api
msc.type: authoredcontent
ms.openlocfilehash: 7bec95af4532535f0d620bfe6862958907466874
ms.sourcegitcommit: 728f4e47be91e1c87bb7c0041734191b5f5c6da3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54444258"
---
<a name="get-started-with-aspnet-web-api-2-c"></a>Erste Schritte mit ASP.NET-Web-API 2 (c#)
====================
durch [Mike Wasson](https://github.com/MikeWasson)

[Abgeschlossenes Projekt herunterladen](https://code.msdn.microsoft.com/Sample-code-of-Getting-c56ccb28)

HTTP ist nicht nur für Webseiten bereitstellt. HTTP ist auch eine leistungsstarke Plattform zum Erstellen von APIs, Dienste und Daten verfügbar machen. HTTP ist einfach, flexibel und allgegenwärtig. Nahezu jede Plattform, der Sie sich vorstellen können hat eine HTTP-Bibliothek, damit die HTTP-Dienste eine breit gefächerte Palette von Clients, einschließlich Browsern, mobilen Geräten und herkömmliche desktopanwendungen können.

ASP.NET Web-API ist ein Framework zum Erstellen von Web-APIs auf .NET Framework. In diesem Tutorial verwenden Sie die ASP.NET Web-API, um eine Web-API zu erstellen, die eine Liste der Produkte zurückgibt.

## <a name="software-versions-used-in-the-tutorial"></a>Softwareversionen, die in diesem Tutorial verwendet werden.

- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)
- Web-API 2

Finden Sie unter [erstellen Sie eine Web-API mit ASP.NET Core und Visual Studio für Windows](https://docs.microsoft.com/aspnet/core/tutorials/first-web-api) für eine neuere Version dieses Tutorials.

## <a name="create-a-web-api-project"></a>Erstellen Sie ein Web-API-Projekt

In diesem Tutorial verwenden Sie die ASP.NET Web-API, um eine Web-API zu erstellen, die eine Liste der Produkte zurückgibt. Die Front-End-Webseite verwendet jQuery, um die Ergebnisse anzuzeigen.

![](tutorial-your-first-web-api/_static/image1.png)

Starten Sie Visual Studio, und wählen Sie **neues Projekt** aus der **starten** Seite. Alternativ wählen Sie in der **Datei** , wählen Sie im Menü **neu** und dann **Projekt**.

In der **Vorlagen** wählen Sie im Bereich **installierte Vorlagen** und erweitern Sie die **Visual C#-** Knoten. Klicken Sie unter **Visual C#-** Option **Web**. Wählen Sie in der Liste der Projektvorlagen das Projekt **ASP.NET-Webanwendung**. Nennen Sie das Projekt "ProductsApp", und klicken Sie auf **OK**.

![](tutorial-your-first-web-api/_static/image2.png)

In der **neues ASP.NET-Projekt** wählen Sie im Dialogfeld die **leere** Vorlage. Klicken Sie unter &quot;fügen Sie Ordner und kernreferenzen für&quot;, überprüfen Sie **Web-API-**. Klicken Sie auf **OK**.

![](tutorial-your-first-web-api/_static/image3.png)

> [!NOTE]
> Sie können auch erstellen, ein Web-API-Projekt mit der &quot;Web-API-&quot; Vorlage. Die Web-API-Vorlage verwendet ASP.NET MVC-API-Hilfeseiten bereitstellen. Ich verwende die leere Vorlage für dieses Tutorial verwenden, da ohne MVC-Web-API angezeigt werden soll. Im Allgemeinen müssen Sie nicht wissen, ASP.NET MVC, Web-API verwenden.


## <a name="adding-a-model"></a>Hinzufügen eines Modells

Ein *Modell* ist ein Objekt, das die Daten in Ihrer Anwendung darstellt. ASP.NET Web-API können automatisch Ihr Modell JSON, XML oder ein anderes Format zu serialisieren, und klicken Sie dann die serialisierten Daten in den Hauptteil der HTTP-Antwortnachricht geschrieben. Solange ein Client das Serialisierungsformat lesen kann, können sie das Objekt deserialisieren. Die meisten Clients können XML oder JSON analysieren. Darüber hinaus kann der Client welches Format angeben, durch Festlegen des Accept-Headers in der HTTP-Anforderungsnachricht werden sollen.

Wir erstellen zunächst ein einfaches Modell, das ein Produkt darstellt.

Wenn der Projektmappen-Explorer nicht angezeigt wird, klicken Sie auf die **Ansicht** Menü **Projektmappen-Explorer**. Klicken Sie im Projektmappen-Explorer den Ordner "Models". Wählen Sie im Kontextmenü des **hinzufügen** wählen Sie dann **Klasse**.

![](tutorial-your-first-web-api/_static/image4.png)

Nennen Sie die Klasse &quot;Produkt&quot;. Fügen Sie die folgenden Eigenschaften auf der `Product` Klasse.

[!code-csharp[Main](tutorial-your-first-web-api/samples/sample1.cs)]

## <a name="adding-a-controller"></a>Hinzufügen eines Controllers

In der Web-API eine *Controller* ist ein Objekt, das HTTP-Anforderungen verarbeitet. Wir fügen einen Controller hinzu, der entweder eine Liste mit Produkten oder ein einzelnes Produkt, das durch ID angegebene zurückgeben können

> [!NOTE]
> Wenn Sie ASP.NET MVC verwendet haben, sind Sie bereits vertraut mit Controllern. Web-API-Controller ähneln MVC-Controller, aber erbt die **ApiController** -Klasse anstelle der **Controller** Klasse.

In **Projektmappen-Explorer**, mit der rechten Maustaste in den Ordner "Controllers". Wählen Sie **hinzufügen** und wählen Sie dann **Controller**.

![](tutorial-your-first-web-api/_static/image5.png)

In der **Gerüst hinzufügen** wählen Sie im Dialogfeld **Web-API-Controller – leer**. Klicken Sie auf **Hinzufügen**.

![](tutorial-your-first-web-api/_static/image6.png)

In der **Controller hinzufügen** Dialogfeld benennen Sie den Controller &quot;ProductsController&quot;. Klicken Sie auf **Hinzufügen**.

![](tutorial-your-first-web-api/_static/image7.png)

Erstellt eine Datei namens ProductsController.cs im Ordner "Controllers".

![](tutorial-your-first-web-api/_static/image8.png)

> [!NOTE]
> Sie müssen nicht die Controller in den Ordner Controller zu platzieren. Der Ordnername ist lediglich eine bequeme Möglichkeit, Ihre Quelldateien zu organisieren.


Wenn diese Datei noch nicht geöffnet ist, doppelklicken Sie auf die Datei, um ihn zu öffnen. Ersetzen Sie den Code in dieser Datei durch Folgendes ein:

[!code-csharp[Main](tutorial-your-first-web-api/samples/sample2.cs)]

Um das Beispiel einfach zu halten, werden Produkte in einem Array fester Größe innerhalb der Controller-Klasse gespeichert. In einer echten Anwendung würden Sie natürlich Abfragen einer Datenbank oder einige andere externe Datenquelle verwenden.

Der Controller definiert zwei Methoden, die Produkte zurückzugeben:

- Die `GetAllProducts` Methode gibt zurück, die gesamte Liste der Produkte als ein **"IEnumerable"&lt;Produkt&gt;**  Typ.
- Die `GetProduct` Methode sucht ein einzelnes Produkt nach seiner ID.

Das ist alles! Sie verfügen über eine funktionierende-Web-API. Jede Methode im Controller entspricht eine oder mehrere URIs:

| Controller-Methode | URI |
| --- | --- |
| GetAllProducts | /api/products |
| GetProduct | /api/products/*id* |

Für die `GetProduct` -Methode, die *Id* im URI ist ein Platzhalter. Um das Produkt mit der ID 5 zu erhalten, ist der URI beispielsweise `api/products/5`.

Weitere Informationen dazu, wie Web-API-HTTP-Anforderungen an Controllermethoden weiterleitet, finden Sie unter [Routing in ASP.NET Web-API](../web-api-routing-and-actions/routing-in-aspnet-web-api.md).

## <a name="calling-the-web-api-with-javascript-and-jquery"></a>Aufrufen der Web-API mit Javascript und jQuery

In diesem Abschnitt fügen wir eine HTML-Seite, die von AJAX verwendet wird, um die Web-API aufzurufen. Wir verwenden jQuery für die AJAX-Aufrufe und auch auf die Seite mit den Ergebnissen zu aktualisieren.

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste in des Projekts, und wählen **hinzufügen**, und wählen Sie dann **neues Element**.

![](tutorial-your-first-web-api/_static/image9.png)

In der **neues Element hinzufügen** wählen Sie im Dialogfeld die **Web** Knoten unter **Visual C#-**, und wählen Sie dann die **HTML-Seite** Element. Nennen Sie die Seite &quot;"Index.HTML"&quot;.

![](tutorial-your-first-web-api/_static/image10.png)

Ersetzen Sie alles, was in dieser Datei durch Folgendes:

[!code-html[Main](tutorial-your-first-web-api/samples/sample3.html)]

Es gibt mehrere Möglichkeiten, um jQuery herunterzuladen. In diesem Beispiel habe ich verwendet die [Microsoft Ajax CDN](../../../ajax/cdn/overview.md). Sie können auch von [ http://jquery.com/ ](http://jquery.com/), und das ASP.NET "Web-API"-Projektvorlage enthält auch jQuery.

### <a name="getting-a-list-of-products"></a>Abrufen einer Liste von Produkten

Um eine Liste der Produkte zu erhalten, senden Sie eine HTTP GET-Anforderung an &quot;/api/Produkte&quot;.

Die jQuery [GetJSON](http://api.jquery.com/jQuery.getJSON/) -Funktion sendet eine AJAX-Anforderung. Für die Antwort Array von JSON-Objekte enthält. Die `done` Funktion gibt einen Rückruf an, die aufgerufen wird, wenn die Anforderung erfolgreich ist. Im Rückruf aktualisieren wir das DOM mit den Produktinformationen.

[!code-html[Main](tutorial-your-first-web-api/samples/sample4.html)]

### <a name="getting-a-product-by-id"></a>Ein Produkt nach ID

Um ein Produkt nach ID zu erhalten, senden Sie eine HTTP GET-Anforderung an &quot;/API/Produkte/*Id*&quot;, wobei *Id* ist die Produkt-ID

[!code-javascript[Main](tutorial-your-first-web-api/samples/sample5.js)]

Wir rufen Sie immer noch `getJSON` die AJAX-Anforderung, aber dieses Mal senden wir verwenden Sie die ID im Anforderungs-URI. Die Antwort auf diese Anforderung ist eine JSON-Darstellung eines einzelnen Produkts.

## <a name="running-the-application"></a>Ausführen der Anwendung

Drücken Sie F5 zum Starten des Debuggings der Anwendungs. Die Webseite sollte wie folgt aussehen:

![](tutorial-your-first-web-api/_static/image11.png)

Um ein Produkt nach ID zu erhalten, geben Sie die ID, und klicken Sie auf Suchen:

![](tutorial-your-first-web-api/_static/image12.png)

Wenn Sie eine ungültige ID eingeben, gibt der Server einen HTTP-Fehler zurück:

![](tutorial-your-first-web-api/_static/image13.png)

## <a name="using-f12-to-view-the-http-request-and-response"></a>Verwendung F12 zum Anzeigen des HTTP-Anforderung und Antwort

Wenn Sie mit einem HTTP-Dienst arbeiten, kann es sein hilfreich sein, die HTTP-Anforderung anzeigen und anfordern können Nachrichten. Dazu können Sie die Verwendung F12-Entwicklertools in Internet Explorer 9. Drücken Sie in Internet Explorer 9 **F12** zu den Tools zu öffnen. Klicken Sie auf die **Netzwerk** Registerkarte, und drücken Sie **erfassen starten**. Wechseln Sie nun an die Webseite, und drücken Sie **F5** , die Webseite neu zu laden. Internet Explorer wird den HTTP-Datenverkehr zwischen dem Browser und dem Webserver erfassen. Ansicht "Zusammenfassung" zeigt den Netzwerkdatenverkehr für eine Seite an:

![](tutorial-your-first-web-api/_static/image14.png)

Suchen Sie den Eintrag für den relativen URI "api/Produkte /". Wählen Sie diesen Eintrag, und klicken Sie auf **wechseln Sie zur detaillierten Ansicht**. In der Detailansicht sind Registerkarten, um die Anforderung und Antwort-Header und Nachrichtentexte anzuzeigen. Angenommen, Sie klicken Sie auf die **Anforderungsheader** Registerkarte können Sie sehen, dass der Client angefordert &quot;Application/Json&quot; im Accept-Header.

![](tutorial-your-first-web-api/_static/image15.png)

Wenn Sie auf der Registerkarte "Antwort-Text" klicken, sehen Sie, wie die Liste der Produkte in JSON serialisiert wurde. Andere Browser aufweisen ähnliche Funktionen. Ist ein nützliches Tool [Fiddler](http://www.fiddler2.com/fiddler2/), ein Web debugging Proxy. Sie können mithilfe von Fiddler an Ihre HTTP-Datenverkehr und zum Erstellen von HTTP-Anforderungen, wodurch Sie die vollständige Kontrolle über die HTTP-Header in der Anforderung.

## <a name="see-this-app-running-on-azure"></a>Finden Sie unter dieser App in Azure ausführen

Möchten Sie die fertige Website als live-Web-app ausgeführt wird, finden Sie unter? Sie können eine vollständige Version der app beim Azure-Konto bereitstellen, indem Sie einfach die folgende Schaltfläche.

[![](https://azuredeploy.net/deploybutton.png)](https://deploy.azure.com/?WT.mc_id=deploy_azure_aspnet&repository=https://github.com/tfitzmac/WebAPI-ProductsApp#/form/setup)

Sie benötigen ein Azure-Konto zum Bereitstellen dieser Lösung in Azure. Wenn Sie nicht bereits über ein Konto verfügen, müssen Sie die folgenden Optionen aus:

- [Öffnen Sie ein Azure-Konto kostenlos](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A443DD604) – Sie erhalten ein Guthaben zum Ausprobieren Zahlungspflichtiger Azure-Dienste nutzen können, und auch nach dem sie verwendet werden, bis können Sie das Konto behalten und kostenlose Azure-Dienste.
- [MSDN-abonnentenvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A443DD604) -Ihr MSDN-Abonnement ein monatliches Guthaben, die Sie für zahlungspflichtige Azure-Dienste verwenden können.

## <a name="next-steps"></a>Nächste Schritte

- Ein vollständigeres Beispiel, der ein HTTP-Dienst, POST, PUT und DELETE-Aktionen unterstützt und in eine Datenbank geschrieben, werden soll, finden Sie unter [mithilfe von Web-API 2 mit Entity Framework 6](../data/using-web-api-with-entity-framework/part-1.md).
- Weitere Informationen zum Erstellen von fließende und reaktionsfreudige Webanwendungen über einen HTTP-Dienst, finden Sie unter [einseitigen ASP.NET-Anwendung](../../../single-page-application/index.md).
- Informationen dazu, wie Sie ein Visual Studio-Webprojekt in Azure App Service bereitstellen, finden Sie unter [ASP.NET Web-app in Azure App Service erstellen](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/).
