---
uid: web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/display_data_items_and_details
title: Anzeigen von Datenelementen und Details | Microsoft-Dokumentation
author: Erikre
description: Dieser tutorialreihe erfahren Sie, die Grundlagen zum Erstellen einer ASP.NET Web Forms-Anwendung mit ASP.NET 4.7 und Microsoft Visual Studio Community 2017 für Web
ms.author: riande
ms.date: 1/09/2019
ms.assetid: 64a491a8-0ed6-4c2f-9c1c-412962eb6006
msc.legacyurl: /web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/display_data_items_and_details
msc.type: authoredcontent
ms.openlocfilehash: 73ae1660f5d6e3e28c1c155e745a62936e3502df
ms.sourcegitcommit: cec77d5ad8a0cedb1ecbec32834111492afd0cd2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207433"
---
<a name="display-data-items-and-details"></a>Anzeigen von Datenelementen und details
====================
durch [Erik Reitan](https://github.com/Erikre)

> Dieser tutorialreihe erfahren Sie, die Grundlagen zum Erstellen einer ASP.NET Web Forms-Anwendung mit ASP.NET 4.7 und Microsoft Visual Studio Community 2017 für das Web.

In diesem Tutorial erfahren Sie, wie zum Anzeigen von Datenelementen und Details zur mit ASP.NET Web Forms und Entity Framework Code First-Element. Dieses Tutorial baut auf dem vorherigen Lernprogramm für "Und Navigation in der Benutzeroberfläche" als Teil der tutorialreihe Wingtip Toys-Store. In diesem Tutorial abgeschlossene Produkte auf die *ProductsList.aspx* Seite und die Details eines Produkts die *ProductDetails.aspx* Seite angezeigt werden.

## <a name="what-you-learn"></a>Ihre Lernziele

- Fügen Sie ein Steuerelement zum Anzeigen von Datenbankprodukten hinzu.
- Verbinden Sie ein Steuerelement, mit der ausgewählten Daten.
- Fügen Sie ein Steuerelement zum Anzeigen von Produktdetails hinzu.
- Analysieren Sie Wert einer Abfragezeichenfolge zu, und verwenden Sie, um abgerufenen Daten zu filtern.

Funktionen, die in diesem Tutorial enthalten die modellbindung und die Wertanbieter.

## <a name="add-a-data-control-to-display-products"></a>Fügen Sie ein Steuerelement zum Anzeigen von Produkten
 
Sie haben einige Optionen für die Daten an einem Steuerelement zu binden. Die am häufigsten verwendeten gehören:

 * Hinzufügen von Datenquellen-Steuerelement
 * Hinzufügen von Code von hand
 * Implementieren der modellbindung

### <a name="use-a-data-source-control-to-bind-data"></a>Verwenden Sie ein Datenquellen-Steuerelement zum Binden von Daten

Hinzufügen von Datenquellen-Steuerelement verknüpft das Datenquellen-Steuerelement auf das Steuerelement, das Daten anzeigt. Bei diesem Ansatz können Sie deklarativ, anstatt programmgesteuert serverseitige Steuerelemente an Datenquellen herstellen.

### <a name="code-by-hand-to-bind-data"></a>Code von hand zum Binden von Daten

Codieren von hand umfasst:

1. Lesen eines Werts
2. Überprüft, ob es null ist.
3. In einen entsprechenden Typ konvertiert wird
4. Überprüfen die Konvertierung Erfolg
5. Eine Abfrage mit dem konvertierten Wert 

Bei diesem Ansatz müssen Sie die vollständige Kontrolle über Ihre Datenzugriffslogik.

### <a name="use-model-binding-to-bind-data"></a>Verwenden von modellbindung zum Binden von Daten

Sie binden die Ergebnisse mit sehr viel weniger Code, und es bietet Ihnen die Möglichkeit, die die Funktionalität in der gesamten Anwendung wiederverwenden, mit der modellbindung. Arbeiten mit Code fokussierter Datenzugriffslogik vereinfacht, dabei aber nicht auf ein Framework für umfangreiche und Datenbindung.

## <a name="display-products"></a>Produkte anzeigen

In diesem Tutorial verwenden Sie die modellbindung zum Binden von Daten. Zum Konfigurieren eines Datensteuerelements, um die modellbindung zu verwenden, um Daten auszuwählen, die Sie festlegen des Steuerelements `SelectMethod` Eigenschaft für eine Methode im Code der Seite. Das Steuerelement ruft die Methode zum geeigneten Zeitpunkt im Lebenszyklus Seite und die zurückgegebenen Daten automatisch gebunden. Es ist nicht erforderlich, explizit aufrufen, die `DataBind` Methode.

Arbeiten die folgenden Schritte aus, die Sie ändern *"ProductList.aspx"* Markup zum Anzeigen von Produkten.

1. In **Projektmappen-Explorer**öffnen *"ProductList.aspx"*.

2. Ersetzen Sie das vorhandene Markup durch das folgende Markup: 

    [!code-aspx-csharp[Main](display_data_items_and_details/samples/sample1.aspx)]

Das vorhergehende Markup verwendet eine **ListView** Steuerelement mit dem Namen `productList` zum Anzeigen von Produkten.

[!code-aspx-csharp[Main](display_data_items_and_details/samples/sample2.aspx)]

Mit Vorlagen und Stile, Sie definieren, wie die **ListView** Steuerelement Daten angezeigt. Es ist nützlich für Daten in einer sich wiederholenden Struktur. Obwohl dies **ListView** Beispiel einfach Daten anzeigt, können Sie auch, ohne Code, Benutzer zu bearbeiten, einfügen und Löschen von Daten, und zu sortieren und Paging Daten.

Beim Festlegen der `ItemType` -Eigenschaft in der **ListView** zu steuern, die XPath-Datenbindungsausdruck `Item` verfügbar ist und das Steuerelement wird stark typisiert. Wie im vorherigen Tutorial erwähnt, können Sie Details zum Element-Objekt mit IntelliSense, auswählen, z. B. Angeben der `ProductName`:

![Anzeigen von Daten Elementen und Details - IntelliSense](display_data_items_and_details/_static/image1.png)

Geben Sie mit der modellbindung eine `SelectMethod` Wert (`GetProducts`). Dies ist die Methode, die Sie, um den Code hinzufügen Verzug für Produkte im nächsten Schritt.

### <a name="add-code-to-display-products"></a>Fügen Sie Code zum Anzeigen von Produkten

In diesem Schritt fügen Sie Code zum Auffüllen der **ListView** -Steuerelement mit Produktdaten für die Datenbank. Der Code unterstützt alle Produkte und Produkte einzelne Kategorie anzeigt.

1. In **Projektmappen-Explorer**, mit der rechten Maustaste *"ProductList.aspx"* und wählen Sie dann **Ansichtscode**.
2. Ersetzen Sie den vorhandenen Code in die *ProductList.aspx.cs* Datei mit diesem:   

    [!code-csharp[Main](display_data_items_and_details/samples/sample3.cs)]

Dieser Code zeigt die `GetProducts` Methode, die die **ListView** des Steuerelements `ItemType` -Eigenschaft verweist auf *"ProductList.aspx"*. Um die Ergebnisse an eine bestimmte Datenbankkategorie beschränken möchten, im Code wird die `categoryId` Wert aus der Abfragezeichenfolge übergeben *"ProductList.aspx"*. Die `QueryStringAttribute` -Klasse in der `System.Web.ModelBinding` Namespace wird verwendet, um das Abrufen von Abfragezeichenfolgen-Variablen `id`Werts. Dies weist die modellbindung, zur Laufzeit der Wert einer Abfragezeichenfolge zum Binden der `categoryId` Parameter.

Wenn eine gültige Kategorie (`categoryId`) wird übergeben, die Ergebnisse sind nur für diese Kategorie in der Datenbank waren. Beispielsweise, wenn die *ProductsList.aspx* Seiten-URL ist dies:

[!code-console[Main](display_data_items_and_details/samples/sample4.cmd)]

Die Seite zeigt nur die Produkte, in denen die `categoryId` gleich `1`.

Wenn keine Abfragezeichenfolge übergeben wird, werden alle Produkte angezeigt.

Die Quellen für diese Methoden werden aufgerufen *Wert Anbieter* (wie z. B. `QueryString`), und die Parameterattribute, die angeben, welcher Wertanbieter verwendet heißen *Wertattribute Anbieter* ( wie z. B. `id`). ASP.NET umfasst Wertanbieter und die Attribute für alle typischen Web Forms-Anwendung Benutzer-Eingabequellen. Dazu gehören die Abfragezeichenfolgen, Cookies, Formularwerte, Steuerelemente, Ansichtszustand, Sitzungsstatus und Profileigenschaften. Sie können auch benutzerdefinierte Wertanbieter erstellen.

### <a name="run-the-application"></a>Ausführen der Anwendung

Führen Sie die Anwendung jetzt alle Produkte oder eine Produktkategorie angezeigt.

1. Drücken Sie in Visual Studio **F5** zum Ausführen der Anwendung.
 Der Browser wird geöffnet und zeigt die *"default.aspx"* Seite.

2. Wählen Sie im Menü "Product Category" **Autos**.

   Die *"ProductList.aspx"* Seite angezeigt wird, nur die Produkte aus der **Autos** Kategorie. Weiter unten in diesem Tutorial zeigen Sie die Produktdetails.

    ![Anzeigen von Daten Elementen und Details - Autos](display_data_items_and_details/_static/image2.png)

3. Wählen Sie **Produkte** im oberen Menü aus.
 Die *"ProductList.aspx"* Seite zeigt jetzt alle Produkte. 

    ![Anzeigen von Daten Elementen und Details - Produkte](display_data_items_and_details/_static/image3.png)

4. Schließen Sie den Browser, und kehren Sie zu Visual Studio zurück.

### <a name="add-a-data-control-to-display-product-details"></a>Fügen Sie ein Steuerelement zum Anzeigen von Produktdetails hinzu.

Ändern der *ProductDetails.aspx* Markup, die Sie im vorherigen Tutorial zur Anzeige von Informationen von bestimmten Produkt hinzugefügt:

1. In **Projektmappen-Explorer**öffnen *ProductDetails.aspx*.

2. Ersetzen Sie das vorhandene Markup durch dieses Markup an:

    [!code-aspx-csharp[Main](display_data_items_and_details/samples/sample5.aspx)] 

Dieses Markup wird ein **FormView** -Steuerelement zum Anzeigen von Details für bestimmte Produkte. Verwendet Methoden wie zum Anzeigen von Daten, in denen *"ProductList.aspx"*. Die **FormView** Steuerelement wird verwendet, um ein einzelner Datensatz aus einer Datenquelle zu einem Zeitpunkt angezeigt. Bei Verwendung der **FormView** -Steuerelement, erstellen Sie Vorlagen, um das Anzeigen und Bearbeiten von datengebundenen Werten. Diese Vorlagen enthalten Steuerelemente binden von Ausdrücken, und formatieren, die definieren, Design und die Funktionen des Formulars.

Verbinden den bisherigen Markupcode mit der Datenbank ist zusätzlichen Code erforderlich.

1. In **Projektmappen-Explorer**, mit der rechten Maustaste *ProductDetails.aspx* und wählen Sie dann **Ansichtscode**.  
   Die *ProductDetails.aspx.cs* Datei wird angezeigt.

2. Ersetzen Sie dabei den vorhandenen Code:   

    [!code-csharp[Main](display_data_items_and_details/samples/sample6.cs)]

Dieser Code überprüft wird, für eine "`productID`" Wert der Abfragezeichenfolge. Wenn Sie ein gültiger Wert gefunden wird, wird das entsprechende Produkt angezeigt. Wenn die Abfragezeichenfolge nicht gefunden oder der Wert ungültig ist, wird kein Produkt angezeigt.

### <a name="run-the-application"></a>Ausführen der Anwendung

Jetzt können Sie die Anwendung bestimmte Produktdetails basierend auf der Produkt-ID finden Sie unter ausführen.

1. Drücken Sie in Visual Studio **F5** zum Ausführen der Anwendung.  
 Der Browser öffnet *"default.aspx"*.

2. Wählen Sie im Menü Kategorie **Boote**.  
 Die *"ProductList.aspx"* angezeigt wird.

3. Wählen Sie **Artikel also**.  
 Die *ProductDetails.aspx* angezeigt wird.   

    ![Anzeigen von Daten Elementen und Details - Produkte](display_data_items_and_details/_static/image4.png)

Im nächsten Tutorial fügen Sie einen Einkaufswagen gelegt hat, an die Anwendung Wingtip Toys.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

[Abrufen und Anzeigen von Daten mit modellbindung und Web forms](../../presenting-and-managing-data/model-binding/retrieving-data.md)

> [!div class="step-by-step"]
> [Zurück](ui_and_navigation.md)
> [Weiter](shopping-cart.md)
