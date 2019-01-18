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
<a name="display-data-items-and-details"></a><span data-ttu-id="0a81c-103">Anzeigen von Datenelementen und details</span><span class="sxs-lookup"><span data-stu-id="0a81c-103">Display data items and details</span></span>
====================
<span data-ttu-id="0a81c-104">by [Erik Reitan](https://github.com/Erikre)</span><span class="sxs-lookup"><span data-stu-id="0a81c-104">by [Erik Reitan](https://github.com/Erikre)</span></span>

> <span data-ttu-id="0a81c-105">Dieser tutorialreihe erfahren Sie, die Grundlagen zum Erstellen einer ASP.NET Web Forms-Anwendung mit ASP.NET 4.7 und Microsoft Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="0a81c-105">This tutorial series teaches you the basics of building an ASP.NET Web Forms application with ASP.NET 4.7 and Microsoft Visual Studio 2017.</span></span>

<span data-ttu-id="0a81c-106">In diesem Tutorial erfahren Sie, wie Sie zum Anzeigen von Datenelementen und Details zur mit ASP.NET Web Forms und Entity Framework Code First-Element.</span><span class="sxs-lookup"><span data-stu-id="0a81c-106">In this tutorial, you'll learn how to display data items and data item details with ASP.NET Web Forms and Entity Framework Code First.</span></span> <span data-ttu-id="0a81c-107">Dieses Tutorial baut auf dem vorherigen Lernprogramm für "Und Navigation in der Benutzeroberfläche" als Teil der tutorialreihe Wingtip Toys-Store.</span><span class="sxs-lookup"><span data-stu-id="0a81c-107">This tutorial builds on the previous "UI and Navigation" tutorial as part of the Wingtip Toy Store tutorial series.</span></span> <span data-ttu-id="0a81c-108">Nach Abschluss dieses Lernprogramms, sehen Sie Produkte auf die *ProductsList.aspx* Seite und die Details eines Produkts die *ProductDetails.aspx* Seite.</span><span class="sxs-lookup"><span data-stu-id="0a81c-108">After completing this tutorial, you'll see products on the *ProductsList.aspx* page and a product's details on the *ProductDetails.aspx* page.</span></span>

## <a name="youll-learn-how-to"></a><span data-ttu-id="0a81c-109">Sie lernen, die folgende Aufgaben auszuführen:</span><span class="sxs-lookup"><span data-stu-id="0a81c-109">You'll learn how to:</span></span>

- <span data-ttu-id="0a81c-110">Fügen Sie ein Steuerelement zum Anzeigen von Produkten aus der Datenbank hinzu.</span><span class="sxs-lookup"><span data-stu-id="0a81c-110">Add a data control to display products from the database</span></span>
- <span data-ttu-id="0a81c-111">Verbinden Sie ein Steuerelement in den ausgewählten Datentyp</span><span class="sxs-lookup"><span data-stu-id="0a81c-111">Connect a data control to the selected data</span></span>
- <span data-ttu-id="0a81c-112">Fügen Sie ein Steuerelement zum Anzeigen von Produktdetails aus der Datenbank hinzu.</span><span class="sxs-lookup"><span data-stu-id="0a81c-112">Add a data control to display product details from the database</span></span>
- <span data-ttu-id="0a81c-113">Abrufen eines Werts aus der Abfragezeichenfolge, und verwenden Sie diesen Wert, um die Daten einzuschränken, die aus der Datenbank abgerufen werden</span><span class="sxs-lookup"><span data-stu-id="0a81c-113">Retrieve a value from the query string and use that value to limit the data that's retrieved from the database</span></span>

### <a name="features-introduced-in-this-tutorial"></a><span data-ttu-id="0a81c-114">Funktionen, die in diesem Lernprogramm eingeführt:</span><span class="sxs-lookup"><span data-stu-id="0a81c-114">Features introduced in this tutorial:</span></span>

- <span data-ttu-id="0a81c-115">Modellbindung</span><span class="sxs-lookup"><span data-stu-id="0a81c-115">Model binding</span></span>
- <span data-ttu-id="0a81c-116">Wertanbieter</span><span class="sxs-lookup"><span data-stu-id="0a81c-116">Value providers</span></span>

## <a name="add-a-data-control"></a><span data-ttu-id="0a81c-117">Fügen Sie ein Steuerelement hinzu</span><span class="sxs-lookup"><span data-stu-id="0a81c-117">Add a data control</span></span>

<span data-ttu-id="0a81c-118">Sie können eine Reihe von Optionen verwenden, Daten an ein Steuerelement gebunden.</span><span class="sxs-lookup"><span data-stu-id="0a81c-118">You can use a few different options to bind data to a server control.</span></span> <span data-ttu-id="0a81c-119">Die am häufigsten verwendeten gehören:</span><span class="sxs-lookup"><span data-stu-id="0a81c-119">The most common include:</span></span>

 * <span data-ttu-id="0a81c-120">Hinzufügen von Datenquellen-Steuerelement</span><span class="sxs-lookup"><span data-stu-id="0a81c-120">Adding a data source control</span></span>
 * <span data-ttu-id="0a81c-121">Hinzufügen von Code von hand</span><span class="sxs-lookup"><span data-stu-id="0a81c-121">Adding code by hand</span></span>
 * <span data-ttu-id="0a81c-122">Mit der modellbindung</span><span class="sxs-lookup"><span data-stu-id="0a81c-122">Using model binding</span></span>

### <a name="use-a-data-source-control-to-bind-data"></a><span data-ttu-id="0a81c-123">Verwenden Sie ein Datenquellen-Steuerelement zum Binden von Daten</span><span class="sxs-lookup"><span data-stu-id="0a81c-123">Use a data source control to bind data</span></span>

<span data-ttu-id="0a81c-124">Ein Datenquellen-Steuerelement hinzufügen, können Sie die Datenquellen-Steuerelement mit dem Steuerelement verknüpfen, das Daten anzeigt.</span><span class="sxs-lookup"><span data-stu-id="0a81c-124">Adding a data source control allows you to link the data source control to the control that displays the data.</span></span> <span data-ttu-id="0a81c-125">Bei diesem Ansatz können Sie deklarativ, anstatt programmgesteuert serverseitige Steuerelemente an Datenquellen herstellen.</span><span class="sxs-lookup"><span data-stu-id="0a81c-125">With this approach, you can declaratively,  rather than programmatically, connect server-side controls to data sources.</span></span>

### <a name="code-by-hand-to-bind-data"></a><span data-ttu-id="0a81c-126">Code von hand zum Binden von Daten</span><span class="sxs-lookup"><span data-stu-id="0a81c-126">Code by hand to bind data</span></span>

<span data-ttu-id="0a81c-127">Codieren von hand umfasst:</span><span class="sxs-lookup"><span data-stu-id="0a81c-127">Coding by hand involves:</span></span>

1. <span data-ttu-id="0a81c-128">Lesen eines Werts</span><span class="sxs-lookup"><span data-stu-id="0a81c-128">Reading a value</span></span>
2. <span data-ttu-id="0a81c-129">Überprüft, ob es null ist.</span><span class="sxs-lookup"><span data-stu-id="0a81c-129">Checking if it's null</span></span>
3. <span data-ttu-id="0a81c-130">In einen entsprechenden Typ konvertiert wird</span><span class="sxs-lookup"><span data-stu-id="0a81c-130">Converting it to an appropriate type</span></span>
4. <span data-ttu-id="0a81c-131">Überprüfen die Konvertierung Erfolg</span><span class="sxs-lookup"><span data-stu-id="0a81c-131">Checking conversion success</span></span>
5. <span data-ttu-id="0a81c-132">Den Wert verwenden in der Abfrage.</span><span class="sxs-lookup"><span data-stu-id="0a81c-132">Using the value in the query</span></span> 

<span data-ttu-id="0a81c-133">Dadurch können Sie die vollständige Kontrolle über Ihre Datenzugriffslogik haben.</span><span class="sxs-lookup"><span data-stu-id="0a81c-133">This approach lets you have full control over your data-access logic.</span></span>

### <a name="use-model-binding-to-bind-data"></a><span data-ttu-id="0a81c-134">Verwenden von modellbindung zum Binden von Daten</span><span class="sxs-lookup"><span data-stu-id="0a81c-134">Use model binding to bind data</span></span>

<span data-ttu-id="0a81c-135">Modellbindung können Sie die Ergebnisse mit sehr viel weniger Code binden und Ihnen die Möglichkeit, um die Funktionalität in der gesamten Anwendung wiederverwenden.</span><span class="sxs-lookup"><span data-stu-id="0a81c-135">Model binding lets you bind results with far less code and gives you the ability to reuse the functionality throughout your application.</span></span> <span data-ttu-id="0a81c-136">Arbeiten mit Code fokussierter Datenzugriffslogik vereinfacht, dabei aber nicht auf ein Framework für umfangreiche und Datenbindung.</span><span class="sxs-lookup"><span data-stu-id="0a81c-136">It simplifies working with code-focused data-access logic while still providing a rich, data-binding framework.</span></span>

## <a name="display-products"></a><span data-ttu-id="0a81c-137">Produkte anzeigen</span><span class="sxs-lookup"><span data-stu-id="0a81c-137">Display products</span></span>

<span data-ttu-id="0a81c-138">In diesem Tutorial verwenden Sie modellbindung zum Binden von Daten.</span><span class="sxs-lookup"><span data-stu-id="0a81c-138">In this tutorial, you'll use model binding to bind data.</span></span> <span data-ttu-id="0a81c-139">Zum Konfigurieren eines Datensteuerelements, um die modellbindung zu verwenden, um Daten auszuwählen, die Sie festlegen des Steuerelements `SelectMethod` Eigenschaft, um den Namen einer Methode im Code der Seite.</span><span class="sxs-lookup"><span data-stu-id="0a81c-139">To configure a data control to use model binding to select data, you set the control's `SelectMethod` property to a method name in the page's code.</span></span> <span data-ttu-id="0a81c-140">Das Steuerelement ruft die Methode zum geeigneten Zeitpunkt im Lebenszyklus Seite und die zurückgegebenen Daten automatisch gebunden.</span><span class="sxs-lookup"><span data-stu-id="0a81c-140">The data control calls the method at the appropriate time in the page life cycle and automatically binds the returned data.</span></span> <span data-ttu-id="0a81c-141">Es ist nicht erforderlich, explizit aufrufen, die `DataBind` Methode.</span><span class="sxs-lookup"><span data-stu-id="0a81c-141">There's no need to explicitly call the `DataBind` method.</span></span>

1. <span data-ttu-id="0a81c-142">In **Projektmappen-Explorer**öffnen *"ProductList.aspx"*.</span><span class="sxs-lookup"><span data-stu-id="0a81c-142">In **Solution Explorer**, open *ProductList.aspx*.</span></span>
2. <span data-ttu-id="0a81c-143">Ersetzen Sie das vorhandene Markup durch dieses Markup an:</span><span class="sxs-lookup"><span data-stu-id="0a81c-143">Replace the existing markup with this markup:</span></span>   

    [!code-aspx-csharp[Main](display_data_items_and_details/samples/sample1.aspx)]

<span data-ttu-id="0a81c-144">Dieser Code verwendet ein **ListView** Steuerelement mit dem Namen `productList` zum Anzeigen von Produkten.</span><span class="sxs-lookup"><span data-stu-id="0a81c-144">This code uses a **ListView** control named `productList` to display products.</span></span>

[!code-aspx-csharp[Main](display_data_items_and_details/samples/sample2.aspx)]

<span data-ttu-id="0a81c-145">Mit Vorlagen und Stile, Sie definieren, wie die **ListView** Steuerelement Daten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="0a81c-145">With templates and styles, you define how the **ListView** control displays data.</span></span> <span data-ttu-id="0a81c-146">Es ist nützlich für Daten in einer sich wiederholenden Struktur.</span><span class="sxs-lookup"><span data-stu-id="0a81c-146">It's useful for data in any repeating structure.</span></span> <span data-ttu-id="0a81c-147">Obwohl dies **ListView** Beispiel einfach Daten anzeigt, können Sie auch, ohne Code, Benutzer zu bearbeiten, einfügen und Löschen von Daten, und zu sortieren und Paging Daten.</span><span class="sxs-lookup"><span data-stu-id="0a81c-147">Though this **ListView** example simply displays database data, you can also, without code, enable users to edit, insert, and delete data, and to sort and page data.</span></span>

<span data-ttu-id="0a81c-148">Durch Festlegen der `ItemType` -Eigenschaft in der **ListView** zu steuern, die XPath-Datenbindungsausdruck `Item` verfügbar ist und das Steuerelement wird stark typisiert.</span><span class="sxs-lookup"><span data-stu-id="0a81c-148">By setting the `ItemType` property in the **ListView** control, the data-binding expression `Item` is available and the control becomes strongly typed.</span></span> <span data-ttu-id="0a81c-149">Wie im vorherigen Tutorial erwähnt, können Sie Details zum Element-Objekt mit IntelliSense, auswählen, z. B. Angeben der `ProductName`:</span><span class="sxs-lookup"><span data-stu-id="0a81c-149">As mentioned in the previous tutorial, you can select Item object details with IntelliSense, such as specifying the `ProductName`:</span></span>

![Anzeigen von Daten Elementen und Details - IntelliSense](display_data_items_and_details/_static/image1.png)

<span data-ttu-id="0a81c-151">Sie können auch mit der modellbindung an eine `SelectMethod` Wert.</span><span class="sxs-lookup"><span data-stu-id="0a81c-151">You're also using model binding to specify a `SelectMethod` value.</span></span> <span data-ttu-id="0a81c-152">Dieser Wert (`GetProducts`) der Methode Sie dem Code fügen entspricht Verzug für Produkte im nächsten Schritt.</span><span class="sxs-lookup"><span data-stu-id="0a81c-152">This value (`GetProducts`) corresponds to the method you'll add to the code behind to display products in the next step.</span></span>

### <a name="add-code-to-display-products"></a><span data-ttu-id="0a81c-153">Fügen Sie Code zum Anzeigen von Produkten</span><span class="sxs-lookup"><span data-stu-id="0a81c-153">Add code to display products</span></span>

<span data-ttu-id="0a81c-154">In diesem Schritt fügen Sie Code zum Auffüllen der **ListView** -Steuerelement mit Produktdaten aus der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="0a81c-154">In this step, you'll add code to populate the **ListView** control with product data from the database.</span></span> <span data-ttu-id="0a81c-155">Der Code unterstützt alle Produkte und Produkte einzelne Kategorie anzeigt.</span><span class="sxs-lookup"><span data-stu-id="0a81c-155">The code supports showing all products and  individual category products.</span></span>

1. <span data-ttu-id="0a81c-156">In **Projektmappen-Explorer**, mit der rechten Maustaste *"ProductList.aspx"* und wählen Sie dann **Ansichtscode**.</span><span class="sxs-lookup"><span data-stu-id="0a81c-156">In **Solution Explorer**, right-click *ProductList.aspx* and then select **View Code**.</span></span>
2. <span data-ttu-id="0a81c-157">Ersetzen Sie den vorhandenen Code in die *ProductList.aspx.cs* Datei mit diesem:</span><span class="sxs-lookup"><span data-stu-id="0a81c-157">Replace the existing code in the *ProductList.aspx.cs* file with this:</span></span>   

    [!code-csharp[Main](display_data_items_and_details/samples/sample3.cs)]

<span data-ttu-id="0a81c-158">Dieser Code zeigt die `GetProducts` Methode, die die **ListView** des Steuerelements `ItemType` Eigenschaft verweist, der *"ProductList.aspx"* Seite.</span><span class="sxs-lookup"><span data-stu-id="0a81c-158">This code shows the `GetProducts` method that the **ListView** control's `ItemType` property references in the *ProductList.aspx* page.</span></span> <span data-ttu-id="0a81c-159">Um die Ergebnisse an eine bestimmte Datenbankkategorie beschränken möchten, im Code wird die `categoryId` Wert aus der Abfragezeichenfolgenwert übergeben, um die *"ProductList.aspx"* Seite, wenn die *"ProductList.aspx"* Seite ist navigiert.</span><span class="sxs-lookup"><span data-stu-id="0a81c-159">To limit the results to a specific database category, the code sets the `categoryId` value from the query string value passed to the *ProductList.aspx* page when the *ProductList.aspx* page is navigated to.</span></span> <span data-ttu-id="0a81c-160">Die `QueryStringAttribute` -Klasse in der `System.Web.ModelBinding` Namespace wird verwendet, um den Wert des Abfragezeichenfolgen-Variablen abzurufen `id`.</span><span class="sxs-lookup"><span data-stu-id="0a81c-160">The `QueryStringAttribute` class in the `System.Web.ModelBinding` namespace is used to retrieve the value of the query string variable `id`.</span></span> <span data-ttu-id="0a81c-161">Dies weist die modellbindung, um zu versuchen, einen Wert aus der Abfragezeichenfolge zum Binden der `categoryId` Parameter zur Laufzeit.</span><span class="sxs-lookup"><span data-stu-id="0a81c-161">This instructs model binding to try to bind a value from the query string to the `categoryId` parameter at run time.</span></span>

<span data-ttu-id="0a81c-162">Wenn Sie eine gültige Kategorie an der Seite "als Abfragezeichenfolge übergeben wird, sind die Ergebnisse der Abfrage auf diese Produkte in der Datenbank, die mit übereinstimmen beschränkt die `categoryId` Wert.</span><span class="sxs-lookup"><span data-stu-id="0a81c-162">When a valid category is passed as a query string to the page, the results of the query are limited to those products in the database that match the `categoryId` value.</span></span> <span data-ttu-id="0a81c-163">Beispielsweise, wenn die *ProductsList.aspx* Seiten-URL ist dies:</span><span class="sxs-lookup"><span data-stu-id="0a81c-163">For instance, if the *ProductsList.aspx* page URL is this:</span></span>


[!code-console[Main](display_data_items_and_details/samples/sample4.cmd)]

<span data-ttu-id="0a81c-164">Die Seite zeigt nur die Produkte, in denen die `categoryId` gleich `1`.</span><span class="sxs-lookup"><span data-stu-id="0a81c-164">The page displays only the products where the `categoryId` equals `1`.</span></span>

<span data-ttu-id="0a81c-165">Alle Produkte werden angezeigt, wenn keine Abfragezeichenfolge enthalten, wenn ist die *"ProductList.aspx"* Seite aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="0a81c-165">All products are displayed if no query string is included when the *ProductList.aspx* page is called.</span></span>

<span data-ttu-id="0a81c-166">Die Quellen der Werte für diese Methoden werden als bezeichnet *Wert Anbieter* (z. B. *QueryString*), und die Parameterattribute, die angeben, welcher Wertanbieter verwendet, werden als bezeichnet*Anbieter Wertattribute* (z. B. `id`).</span><span class="sxs-lookup"><span data-stu-id="0a81c-166">The sources of values for these methods are referred to as *value providers* (such as *QueryString*), and the parameter attributes that indicate which value provider to use are referred to as *value provider attributes* (such as `id`).</span></span> <span data-ttu-id="0a81c-167">ASP.NET umfasst Wertanbieter und die zugehörigen Attribute für alle typischen Quellen von Benutzereingaben in einer Web Forms-Anwendung, z. B. der Abfragezeichenfolgen, Cookies, Formularwerte, Steuerelemente, Ansichtszustand, Sitzungsstatus und Profileigenschaften.</span><span class="sxs-lookup"><span data-stu-id="0a81c-167">ASP.NET includes value providers and corresponding attributes for all of the typical sources of user input in a Web Forms application such as the query string, cookies, form values, controls, view state, session state, and profile properties.</span></span> <span data-ttu-id="0a81c-168">Sie können auch benutzerdefinierte Wertanbieter erstellen.</span><span class="sxs-lookup"><span data-stu-id="0a81c-168">You can also write custom value providers.</span></span>

### <a name="run-the-application"></a><span data-ttu-id="0a81c-169">Ausführen der Anwendung</span><span class="sxs-lookup"><span data-stu-id="0a81c-169">Run the application</span></span>

<span data-ttu-id="0a81c-170">Führen Sie die Anwendung jetzt alle Produkte oder eine Produktkategorie angezeigt.</span><span class="sxs-lookup"><span data-stu-id="0a81c-170">Run the application now to view all products or a category's products.</span></span>

1. <span data-ttu-id="0a81c-171">Drücken Sie **F5** während Sie sich in Visual Studio, um die Anwendung auszuführen.</span><span class="sxs-lookup"><span data-stu-id="0a81c-171">Press **F5** while in Visual Studio to run the application.</span></span>  
   <span data-ttu-id="0a81c-172">Der Browser wird geöffnet und zeigt die *"default.aspx"* Seite.</span><span class="sxs-lookup"><span data-stu-id="0a81c-172">The browser opens and shows the *Default.aspx* page.</span></span>

2. <span data-ttu-id="0a81c-173">Wählen Sie **Autos** im Navigationsmenü Product Category.</span><span class="sxs-lookup"><span data-stu-id="0a81c-173">Select **Cars** from the product category navigation menu.</span></span>  
   <span data-ttu-id="0a81c-174">Die *"ProductList.aspx"* Seite zeigt zeigt nur **Autos** Kategorieprodukte.</span><span class="sxs-lookup"><span data-stu-id="0a81c-174">The *ProductList.aspx* page displays showing only **Cars** category products.</span></span> <span data-ttu-id="0a81c-175">Weiter unten in diesem Tutorial werden Sie Produktdetails anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="0a81c-175">Later in this tutorial, you'll display product details.</span></span>  

    ![Anzeigen von Daten Elementen und Details - Autos](display_data_items_and_details/_static/image2.png)

3. <span data-ttu-id="0a81c-177">Wählen Sie **Produkte** oben im Navigationsmenü aus.</span><span class="sxs-lookup"><span data-stu-id="0a81c-177">Select **Products** from the navigation menu at the top.</span></span>  
   <span data-ttu-id="0a81c-178">In diesem Fall die *"ProductList.aspx"* Seite wird angezeigt, aber dieses Mal es die gesamte Liste der Produkte zeigt.</span><span class="sxs-lookup"><span data-stu-id="0a81c-178">Again, the *ProductList.aspx* page is displayed, however this time it shows the entire list of products.</span></span>   

    ![Anzeigen von Daten Elementen und Details - Produkte](display_data_items_and_details/_static/image3.png)

4. <span data-ttu-id="0a81c-180">Schließen Sie den Browser, und kehren Sie zu Visual Studio zurück.</span><span class="sxs-lookup"><span data-stu-id="0a81c-180">Close the browser and return to Visual Studio.</span></span>

### <a name="add-a-data-control-to-display-product-details"></a><span data-ttu-id="0a81c-181">Fügen Sie ein Steuerelement zum Anzeigen von Produktdetails hinzu.</span><span class="sxs-lookup"><span data-stu-id="0a81c-181">Add a data control to display product details</span></span>

<span data-ttu-id="0a81c-182">Als Nächstes ändern Sie das Markup in der *ProductDetails.aspx* Seite, die Sie im vorherigen Tutorial zur Anzeige von Informationen von bestimmten Produkt hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="0a81c-182">Next, you'll modify the markup in the *ProductDetails.aspx* page that you added in the previous tutorial to display specific product information.</span></span>

1. <span data-ttu-id="0a81c-183">In **Projektmappen-Explorer**öffnen *ProductDetails.aspx*.</span><span class="sxs-lookup"><span data-stu-id="0a81c-183">In **Solution Explorer**, open *ProductDetails.aspx*.</span></span>

2. <span data-ttu-id="0a81c-184">Ersetzen Sie das vorhandene Markup durch dieses Markup an:</span><span class="sxs-lookup"><span data-stu-id="0a81c-184">Replace the existing markup with this markup:</span></span>

    [!code-aspx-csharp[Main](display_data_items_and_details/samples/sample5.aspx)] 

    <span data-ttu-id="0a81c-185">Dieser Code verwendet ein **FormView** -Steuerelement zum Anzeigen von Details für bestimmte Produkte.</span><span class="sxs-lookup"><span data-stu-id="0a81c-185">This code uses a **FormView** control to display specific product details.</span></span> <span data-ttu-id="0a81c-186">Dieses Markup wird mit Methoden wie die Methoden zum Anzeigen von Daten in die *"ProductList.aspx"* Seite.</span><span class="sxs-lookup"><span data-stu-id="0a81c-186">This markup uses methods like the methods used to display data in the *ProductList.aspx* page.</span></span> <span data-ttu-id="0a81c-187">Die **FormView** Steuerelement wird verwendet, um ein einzelner Datensatz aus einer Datenquelle zu einem Zeitpunkt angezeigt.</span><span class="sxs-lookup"><span data-stu-id="0a81c-187">The **FormView** control is used to display a single record at a time from a data source.</span></span> <span data-ttu-id="0a81c-188">Bei Verwendung der **FormView** -Steuerelement, erstellen Sie Vorlagen, um das Anzeigen und Bearbeiten von datengebundenen Werten.</span><span class="sxs-lookup"><span data-stu-id="0a81c-188">When you use the **FormView** control, you create templates to display and edit data-bound values.</span></span> <span data-ttu-id="0a81c-189">Diese Vorlagen enthalten Steuerelemente binden von Ausdrücken, und formatieren, die definieren, Design und die Funktionen des Formulars.</span><span class="sxs-lookup"><span data-stu-id="0a81c-189">These templates contain controls, binding expressions, and formatting that define the form's look and functionality.</span></span>

<span data-ttu-id="0a81c-190">Verbinden den bisherigen Markupcode mit der Datenbank ist zusätzlichen Code erforderlich.</span><span class="sxs-lookup"><span data-stu-id="0a81c-190">Connecting the previous markup to the database requires additional code.</span></span>

1. <span data-ttu-id="0a81c-191">In **Projektmappen-Explorer**, mit der rechten Maustaste *ProductDetails.aspx* , und klicken Sie dann auf **Ansichtscode**.</span><span class="sxs-lookup"><span data-stu-id="0a81c-191">In **Solution Explorer**, right-click *ProductDetails.aspx* and then click **View Code**.</span></span>  
   <span data-ttu-id="0a81c-192">Die *ProductDetails.aspx.cs* Datei wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="0a81c-192">The *ProductDetails.aspx.cs* file is displayed.</span></span>

2. <span data-ttu-id="0a81c-193">Ersetzen Sie den vorhandenen Code durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="0a81c-193">Replace the existing code with this code:</span></span>   

    [!code-csharp[Main](display_data_items_and_details/samples/sample6.cs)]

<span data-ttu-id="0a81c-194">Dieser Code überprüft wird, für eine "`productID`" Query-String-Wert.</span><span class="sxs-lookup"><span data-stu-id="0a81c-194">This code checks for a "`productID`" query-string value.</span></span> <span data-ttu-id="0a81c-195">Wenn Sie ein gültigen Abfragezeichenfolgen-Wert gefunden wird, wird das entsprechende Produkt angezeigt.</span><span class="sxs-lookup"><span data-stu-id="0a81c-195">If a valid query-string value is found, the matching product is displayed.</span></span> <span data-ttu-id="0a81c-196">Wenn die Abfragezeichenfolge nicht gefunden oder der Wert ungültig ist, wird kein Produkt angezeigt.</span><span class="sxs-lookup"><span data-stu-id="0a81c-196">If the query-string isn't found, or its value isn't valid, no product is displayed.</span></span>

### <a name="run-the-application"></a><span data-ttu-id="0a81c-197">Ausführen der Anwendung</span><span class="sxs-lookup"><span data-stu-id="0a81c-197">Run the application</span></span>

<span data-ttu-id="0a81c-198">Jetzt können Sie die Anwendung aus, um ein einzelnes Produkt angezeigt sehen ausführen basierend auf der Produkt-ID</span><span class="sxs-lookup"><span data-stu-id="0a81c-198">Now you can run the application to see an individual product displayed based on product ID.</span></span>

1. <span data-ttu-id="0a81c-199">Drücken Sie **F5** während Sie sich in Visual Studio, um die Anwendung auszuführen.</span><span class="sxs-lookup"><span data-stu-id="0a81c-199">Press **F5** while in Visual Studio to run the application.</span></span>  
   <span data-ttu-id="0a81c-200">Der Browser wird geöffnet und zeigt die *"default.aspx"* Seite.</span><span class="sxs-lookup"><span data-stu-id="0a81c-200">The browser opens and shows the *Default.aspx* page.</span></span>

2. <span data-ttu-id="0a81c-201">Wählen Sie **Boote** im Navigationsmenü der Kategorie.</span><span class="sxs-lookup"><span data-stu-id="0a81c-201">Select **Boats** from the category navigation menu.</span></span>  
   <span data-ttu-id="0a81c-202">Die *"ProductList.aspx"* angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="0a81c-202">The *ProductList.aspx* page is displayed.</span></span>

3. <span data-ttu-id="0a81c-203">Wählen Sie **Papier Schiff** aus der Liste der Produkte.</span><span class="sxs-lookup"><span data-stu-id="0a81c-203">Select **Paper Boat** from the product list.</span></span>
   <span data-ttu-id="0a81c-204">Die *ProductDetails.aspx* angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="0a81c-204">The *ProductDetails.aspx* page is displayed.</span></span>

    ![Anzeigen von Daten Elementen und Details - Produkte](display_data_items_and_details/_static/image4.png)
    
4. <span data-ttu-id="0a81c-206">Schließen Sie den Browser.</span><span class="sxs-lookup"><span data-stu-id="0a81c-206">Close the browser.</span></span>


## <a name="additional-resources"></a><span data-ttu-id="0a81c-207">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="0a81c-207">Additional resources</span></span>

[<span data-ttu-id="0a81c-208">Abrufen und Anzeigen von Daten mit modellbindung und Web forms</span><span class="sxs-lookup"><span data-stu-id="0a81c-208">Retrieving and displaying data with model binding and web forms</span></span>](../../presenting-and-managing-data/model-binding/retrieving-data.md)

## <a name="next-steps"></a><span data-ttu-id="0a81c-209">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="0a81c-209">Next steps</span></span>

<span data-ttu-id="0a81c-210">In diesem Tutorial haben Sie Markup und Code zum Anzeigen von Produkten und Produktdetails hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="0a81c-210">In this tutorial, you added markup and code to display products and product details.</span></span> <span data-ttu-id="0a81c-211">Sie haben über die stark typisierte Datensteuerelemente, modellbindung und Wertanbieter.</span><span class="sxs-lookup"><span data-stu-id="0a81c-211">You learned about strongly typed data controls, model binding, and value providers.</span></span> <span data-ttu-id="0a81c-212">Im nächsten Tutorial fügen Sie einen Einkaufswagen gelegt hat der Wingtip Toys-beispielanwendung.</span><span class="sxs-lookup"><span data-stu-id="0a81c-212">In the next tutorial, you'll add a shopping cart to the Wingtip Toys sample application.</span></span> 

> [!div class="step-by-step"]
> <span data-ttu-id="0a81c-213">[Zurück](ui_and_navigation.md)
> [Weiter](shopping-cart.md)</span><span class="sxs-lookup"><span data-stu-id="0a81c-213">[Previous](ui_and_navigation.md)
[Next](shopping-cart.md)</span></span>
