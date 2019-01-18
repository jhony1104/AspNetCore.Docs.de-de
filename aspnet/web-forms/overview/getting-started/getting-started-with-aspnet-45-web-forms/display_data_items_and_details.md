---
uid: web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/display_data_items_and_details
title: Anzeigen von Datenelementen und Details | Microsoft-Dokumentation
author: Erikre
description: Diese tutorialreihe zeigt Ihnen, die Grundlagen zum Erstellen einer ASP.NET Web Forms-Anwendung mit ASP.NET 4.7 und Microsoft Visual Studio 2017
ms.author: riande
ms.date: 1/04/2019
ms.assetid: 64a491a8-0ed6-4c2f-9c1c-412962eb6006
msc.legacyurl: /web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/display_data_items_and_details
msc.type: authoredcontent
ms.openlocfilehash: acc2f8e78375ef0455d467e2af750ecbee623224
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396224"
---
<a name="display-data-items-and-details"></a>Anzeigen von Datenelementen und details
====================
by [Erik Reitan](https://github.com/Erikre)

> Dieser tutorialreihe erfahren Sie, die Grundlagen zum Erstellen einer ASP.NET Web Forms-Anwendung mit ASP.NET 4.7 und Microsoft Visual Studio 2017.

In diesem Tutorial erfahren Sie, wie Sie zum Anzeigen von Datenelementen und Details zur mit ASP.NET Web Forms und Entity Framework Code First-Element. Dieses Tutorial baut auf dem vorherigen Lernprogramm für "Und Navigation in der Benutzeroberfläche" als Teil der tutorialreihe Wingtip Toys-Store. Nach Abschluss dieses Lernprogramms, sehen Sie Produkte auf die *ProductsList.aspx* Seite und die Details eines Produkts die *ProductDetails.aspx* Seite.

## <a name="youll-learn-how-to"></a>Sie lernen, die folgende Aufgaben auszuführen:

- Fügen Sie ein Steuerelement zum Anzeigen von Produkten aus der Datenbank hinzu.
- Verbinden Sie ein Steuerelement in den ausgewählten Datentyp
- Fügen Sie ein Steuerelement zum Anzeigen von Produktdetails aus der Datenbank hinzu.
- Abrufen eines Werts aus der Abfragezeichenfolge, und verwenden Sie diesen Wert, um die Daten einzuschränken, die aus der Datenbank abgerufen werden

### <a name="features-introduced-in-this-tutorial"></a>Funktionen, die in diesem Lernprogramm eingeführt:

- Modellbindung
- Wertanbieter

## <a name="add-a-data-control"></a>Fügen Sie ein Steuerelement hinzu

Sie können eine Reihe von Optionen verwenden, Daten an ein Steuerelement gebunden. Die am häufigsten verwendeten gehören:

 * Hinzufügen von Datenquellen-Steuerelement
 * Hinzufügen von Code von hand
 * Mit der modellbindung

### <a name="use-a-data-source-control-to-bind-data"></a>Verwenden Sie ein Datenquellen-Steuerelement zum Binden von Daten

Ein Datenquellen-Steuerelement hinzufügen, können Sie die Datenquellen-Steuerelement mit dem Steuerelement verknüpfen, das Daten anzeigt. Bei diesem Ansatz können Sie deklarativ, anstatt programmgesteuert serverseitige Steuerelemente an Datenquellen herstellen.

### <a name="code-by-hand-to-bind-data"></a>Code von hand zum Binden von Daten

Codieren von hand umfasst:

1. Lesen eines Werts
2. Überprüft, ob es null ist.
3. In einen entsprechenden Typ konvertiert wird
4. Überprüfen die Konvertierung Erfolg
5. Den Wert verwenden in der Abfrage. 

Dadurch können Sie die vollständige Kontrolle über Ihre Datenzugriffslogik haben.

### <a name="use-model-binding-to-bind-data"></a>Verwenden von modellbindung zum Binden von Daten

Modellbindung können Sie die Ergebnisse mit sehr viel weniger Code binden und Ihnen die Möglichkeit, um die Funktionalität in der gesamten Anwendung wiederverwenden. Arbeiten mit Code fokussierter Datenzugriffslogik vereinfacht, dabei aber nicht auf ein Framework für umfangreiche und Datenbindung.

## <a name="display-products"></a>Produkte anzeigen

In diesem Tutorial verwenden Sie modellbindung zum Binden von Daten. Zum Konfigurieren eines Datensteuerelements, um die modellbindung zu verwenden, um Daten auszuwählen, die Sie festlegen des Steuerelements `SelectMethod` Eigenschaft, um den Namen einer Methode im Code der Seite. Das Steuerelement ruft die Methode zum geeigneten Zeitpunkt im Lebenszyklus Seite und die zurückgegebenen Daten automatisch gebunden. Es ist nicht erforderlich, explizit aufrufen, die `DataBind` Methode.

1. In **Projektmappen-Explorer**öffnen *"ProductList.aspx"*.
2. Ersetzen Sie das vorhandene Markup durch dieses Markup an:   

    [!code-aspx-csharp[Main](display_data_items_and_details/samples/sample1.aspx)]

Dieser Code verwendet ein **ListView** Steuerelement mit dem Namen `productList` zum Anzeigen von Produkten.

[!code-aspx-csharp[Main](display_data_items_and_details/samples/sample2.aspx)]

Mit Vorlagen und Stile, Sie definieren, wie die **ListView** Steuerelement Daten angezeigt. Es ist nützlich für Daten in einer sich wiederholenden Struktur. Obwohl dies **ListView** Beispiel einfach Daten anzeigt, können Sie auch, ohne Code, Benutzer zu bearbeiten, einfügen und Löschen von Daten, und zu sortieren und Paging Daten.

Durch Festlegen der `ItemType` -Eigenschaft in der **ListView** zu steuern, die XPath-Datenbindungsausdruck `Item` verfügbar ist und das Steuerelement wird stark typisiert. Wie im vorherigen Tutorial erwähnt, können Sie Details zum Element-Objekt mit IntelliSense, auswählen, z. B. Angeben der `ProductName`:

![Anzeigen von Daten Elementen und Details - IntelliSense](display_data_items_and_details/_static/image1.png)

Sie können auch mit der modellbindung an eine `SelectMethod` Wert. Dieser Wert (`GetProducts`) der Methode Sie dem Code fügen entspricht Verzug für Produkte im nächsten Schritt.

### <a name="add-code-to-display-products"></a>Fügen Sie Code zum Anzeigen von Produkten

In diesem Schritt fügen Sie Code zum Auffüllen der **ListView** -Steuerelement mit Produktdaten aus der Datenbank. Der Code unterstützt alle Produkte und Produkte einzelne Kategorie anzeigt.

1. In **Projektmappen-Explorer**, mit der rechten Maustaste *"ProductList.aspx"* und wählen Sie dann **Ansichtscode**.
2. Ersetzen Sie den vorhandenen Code in die *ProductList.aspx.cs* Datei mit diesem:   

    [!code-csharp[Main](display_data_items_and_details/samples/sample3.cs)]

Dieser Code zeigt die `GetProducts` Methode, die die **ListView** des Steuerelements `ItemType` Eigenschaft verweist, der *"ProductList.aspx"* Seite. Um die Ergebnisse an eine bestimmte Datenbankkategorie beschränken möchten, im Code wird die `categoryId` Wert aus der Abfragezeichenfolgenwert übergeben, um die *"ProductList.aspx"* Seite, wenn die *"ProductList.aspx"* Seite ist navigiert. Die `QueryStringAttribute` -Klasse in der `System.Web.ModelBinding` Namespace wird verwendet, um den Wert des Abfragezeichenfolgen-Variablen abzurufen `id`. Dies weist die modellbindung, um zu versuchen, einen Wert aus der Abfragezeichenfolge zum Binden der `categoryId` Parameter zur Laufzeit.

Wenn Sie eine gültige Kategorie an der Seite "als Abfragezeichenfolge übergeben wird, sind die Ergebnisse der Abfrage auf diese Produkte in der Datenbank, die mit übereinstimmen beschränkt die `categoryId` Wert. Beispielsweise, wenn die *ProductsList.aspx* Seiten-URL ist dies:


[!code-console[Main](display_data_items_and_details/samples/sample4.cmd)]

Die Seite zeigt nur die Produkte, in denen die `categoryId` gleich `1`.

Alle Produkte werden angezeigt, wenn keine Abfragezeichenfolge enthalten, wenn ist die *"ProductList.aspx"* Seite aufgerufen wird.

Die Quellen der Werte für diese Methoden werden als bezeichnet *Wert Anbieter* (z. B. *QueryString*), und die Parameterattribute, die angeben, welcher Wertanbieter verwendet, werden als bezeichnet*Anbieter Wertattribute* (z. B. `id`). ASP.NET umfasst Wertanbieter und die zugehörigen Attribute für alle typischen Quellen von Benutzereingaben in einer Web Forms-Anwendung, z. B. der Abfragezeichenfolgen, Cookies, Formularwerte, Steuerelemente, Ansichtszustand, Sitzungsstatus und Profileigenschaften. Sie können auch benutzerdefinierte Wertanbieter erstellen.

### <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die Anwendung jetzt alle Produkte oder eine Produktkategorie angezeigt.

1. Drücken Sie **F5** während Sie sich in Visual Studio, um die Anwendung auszuführen.  
   Der Browser wird geöffnet und zeigt die *"default.aspx"* Seite.

2. Wählen Sie **Autos** im Navigationsmenü Product Category.  
   Die *"ProductList.aspx"* Seite zeigt zeigt nur **Autos** Kategorieprodukte. Weiter unten in diesem Tutorial werden Sie Produktdetails anzuzeigen.  

    ![Anzeigen von Daten Elementen und Details - Autos](display_data_items_and_details/_static/image2.png)

3. Wählen Sie **Produkte** oben im Navigationsmenü aus.  
   In diesem Fall die *"ProductList.aspx"* Seite wird angezeigt, aber dieses Mal es die gesamte Liste der Produkte zeigt.   

    ![Anzeigen von Daten Elementen und Details - Produkte](display_data_items_and_details/_static/image3.png)

4. Schließen Sie den Browser, und kehren Sie zu Visual Studio zurück.

### <a name="add-a-data-control-to-display-product-details"></a>Fügen Sie ein Steuerelement zum Anzeigen von Produktdetails hinzu.

Als Nächstes ändern Sie das Markup in der *ProductDetails.aspx* Seite, die Sie im vorherigen Tutorial zur Anzeige von Informationen von bestimmten Produkt hinzugefügt.

1. In **Projektmappen-Explorer**öffnen *ProductDetails.aspx*.

2. Ersetzen Sie das vorhandene Markup durch dieses Markup an:

    [!code-aspx-csharp[Main](display_data_items_and_details/samples/sample5.aspx)] 

    Dieser Code verwendet ein **FormView** -Steuerelement zum Anzeigen von Details für bestimmte Produkte. Dieses Markup wird mit Methoden wie die Methoden zum Anzeigen von Daten in die *"ProductList.aspx"* Seite. Die **FormView** Steuerelement wird verwendet, um ein einzelner Datensatz aus einer Datenquelle zu einem Zeitpunkt angezeigt. Bei Verwendung der **FormView** -Steuerelement, erstellen Sie Vorlagen, um das Anzeigen und Bearbeiten von datengebundenen Werten. Diese Vorlagen enthalten Steuerelemente binden von Ausdrücken, und formatieren, die definieren, Design und die Funktionen des Formulars.

Verbinden den bisherigen Markupcode mit der Datenbank ist zusätzlichen Code erforderlich.

1. In **Projektmappen-Explorer**, mit der rechten Maustaste *ProductDetails.aspx* , und klicken Sie dann auf **Ansichtscode**.  
   Die *ProductDetails.aspx.cs* Datei wird angezeigt.

2. Ersetzen Sie den vorhandenen Code durch den folgenden Code:   

    [!code-csharp[Main](display_data_items_and_details/samples/sample6.cs)]

Dieser Code überprüft wird, für eine "`productID`" Query-String-Wert. Wenn Sie ein gültigen Abfragezeichenfolgen-Wert gefunden wird, wird das entsprechende Produkt angezeigt. Wenn die Abfragezeichenfolge nicht gefunden oder der Wert ungültig ist, wird kein Produkt angezeigt.

### <a name="run-the-application"></a>Ausführen der Anwendung

Jetzt können Sie die Anwendung aus, um ein einzelnes Produkt angezeigt sehen ausführen basierend auf der Produkt-ID

1. Drücken Sie **F5** während Sie sich in Visual Studio, um die Anwendung auszuführen.  
   Der Browser wird geöffnet und zeigt die *"default.aspx"* Seite.

2. Wählen Sie **Boote** im Navigationsmenü der Kategorie.  
   Die *"ProductList.aspx"* angezeigt wird.

3. Wählen Sie **Papier Schiff** aus der Liste der Produkte.
   Die *ProductDetails.aspx* angezeigt wird.

    ![Anzeigen von Daten Elementen und Details - Produkte](display_data_items_and_details/_static/image4.png)
    
4. Schließen Sie den Browser.


## <a name="additional-resources"></a>Zusätzliche Ressourcen

[Abrufen und Anzeigen von Daten mit modellbindung und Web forms](../../presenting-and-managing-data/model-binding/retrieving-data.md)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Markup und Code zum Anzeigen von Produkten und Produktdetails hinzugefügt. Sie haben über die stark typisierte Datensteuerelemente, modellbindung und Wertanbieter. Im nächsten Tutorial fügen Sie einen Einkaufswagen gelegt hat der Wingtip Toys-beispielanwendung. 

> [!div class="step-by-step"]
> [Zurück](ui_and_navigation.md)
> [Weiter](shopping-cart.md)
