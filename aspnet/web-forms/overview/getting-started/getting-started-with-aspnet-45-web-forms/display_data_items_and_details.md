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
<a name="display-data-items-and-details"></a><span data-ttu-id="a74da-103">Anzeigen von Datenelementen und details</span><span class="sxs-lookup"><span data-stu-id="a74da-103">Display data items and details</span></span>
====================
<span data-ttu-id="a74da-104">durch [Erik Reitan](https://github.com/Erikre)</span><span class="sxs-lookup"><span data-stu-id="a74da-104">by [Erik Reitan](https://github.com/Erikre)</span></span>

> <span data-ttu-id="a74da-105">Dieser tutorialreihe erfahren Sie, die Grundlagen zum Erstellen einer ASP.NET Web Forms-Anwendung mit ASP.NET 4.7 und Microsoft Visual Studio Community 2017 für das Web.</span><span class="sxs-lookup"><span data-stu-id="a74da-105">This tutorial series teaches you the basics of building an ASP.NET Web Forms application with ASP.NET 4.7 and Microsoft Visual Studio Community 2017 for the Web.</span></span>

<span data-ttu-id="a74da-106">In diesem Tutorial erfahren Sie, wie zum Anzeigen von Datenelementen und Details zur mit ASP.NET Web Forms und Entity Framework Code First-Element.</span><span class="sxs-lookup"><span data-stu-id="a74da-106">In this tutorial, you learn how to display data items and data item details with ASP.NET Web Forms and Entity Framework Code First.</span></span> <span data-ttu-id="a74da-107">Dieses Tutorial baut auf dem vorherigen Lernprogramm für "Und Navigation in der Benutzeroberfläche" als Teil der tutorialreihe Wingtip Toys-Store.</span><span class="sxs-lookup"><span data-stu-id="a74da-107">This tutorial builds on the previous "UI and Navigation" tutorial as part of the Wingtip Toy Store tutorial series.</span></span> <span data-ttu-id="a74da-108">In diesem Tutorial abgeschlossene Produkte auf die *ProductsList.aspx* Seite und die Details eines Produkts die *ProductDetails.aspx* Seite angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="a74da-108">In the completed tutorial, products on the *ProductsList.aspx* page and a product's details on the *ProductDetails.aspx* page are displayed.</span></span>

## <a name="what-you-learn"></a><span data-ttu-id="a74da-109">Ihre Lernziele</span><span class="sxs-lookup"><span data-stu-id="a74da-109">What you learn</span></span>

- <span data-ttu-id="a74da-110">Fügen Sie ein Steuerelement zum Anzeigen von Datenbankprodukten hinzu.</span><span class="sxs-lookup"><span data-stu-id="a74da-110">Add a data control to display database products.</span></span>
- <span data-ttu-id="a74da-111">Verbinden Sie ein Steuerelement, mit der ausgewählten Daten.</span><span class="sxs-lookup"><span data-stu-id="a74da-111">Connect a data control to selected data.</span></span>
- <span data-ttu-id="a74da-112">Fügen Sie ein Steuerelement zum Anzeigen von Produktdetails hinzu.</span><span class="sxs-lookup"><span data-stu-id="a74da-112">Add a data control to display product details.</span></span>
- <span data-ttu-id="a74da-113">Analysieren Sie Wert einer Abfragezeichenfolge zu, und verwenden Sie, um abgerufenen Daten zu filtern.</span><span class="sxs-lookup"><span data-stu-id="a74da-113">Parse a query string value and use it to filter retrieved database data.</span></span>

<span data-ttu-id="a74da-114">Funktionen, die in diesem Tutorial enthalten die modellbindung und die Wertanbieter.</span><span class="sxs-lookup"><span data-stu-id="a74da-114">Features introduced in this tutorial include model binding and value providers.</span></span>

## <a name="add-a-data-control-to-display-products"></a><span data-ttu-id="a74da-115">Fügen Sie ein Steuerelement zum Anzeigen von Produkten</span><span class="sxs-lookup"><span data-stu-id="a74da-115">Add a data control to display products</span></span>
 
<span data-ttu-id="a74da-116">Sie haben einige Optionen für die Daten an einem Steuerelement zu binden.</span><span class="sxs-lookup"><span data-stu-id="a74da-116">You have a few options to bind data to a server control.</span></span> <span data-ttu-id="a74da-117">Die am häufigsten verwendeten gehören:</span><span class="sxs-lookup"><span data-stu-id="a74da-117">The most common include:</span></span>

 * <span data-ttu-id="a74da-118">Hinzufügen von Datenquellen-Steuerelement</span><span class="sxs-lookup"><span data-stu-id="a74da-118">Adding a data source control</span></span>
 * <span data-ttu-id="a74da-119">Hinzufügen von Code von hand</span><span class="sxs-lookup"><span data-stu-id="a74da-119">Adding code by hand</span></span>
 * <span data-ttu-id="a74da-120">Implementieren der modellbindung</span><span class="sxs-lookup"><span data-stu-id="a74da-120">Implementing model binding</span></span>

### <a name="use-a-data-source-control-to-bind-data"></a><span data-ttu-id="a74da-121">Verwenden Sie ein Datenquellen-Steuerelement zum Binden von Daten</span><span class="sxs-lookup"><span data-stu-id="a74da-121">Use a data source control to bind data</span></span>

<span data-ttu-id="a74da-122">Hinzufügen von Datenquellen-Steuerelement verknüpft das Datenquellen-Steuerelement auf das Steuerelement, das Daten anzeigt.</span><span class="sxs-lookup"><span data-stu-id="a74da-122">Adding a data source control links the data source control to the control that displays the data.</span></span> <span data-ttu-id="a74da-123">Bei diesem Ansatz können Sie deklarativ, anstatt programmgesteuert serverseitige Steuerelemente an Datenquellen herstellen.</span><span class="sxs-lookup"><span data-stu-id="a74da-123">With this approach, you can declaratively, rather than programmatically, connect server-side controls to data sources.</span></span>

### <a name="code-by-hand-to-bind-data"></a><span data-ttu-id="a74da-124">Code von hand zum Binden von Daten</span><span class="sxs-lookup"><span data-stu-id="a74da-124">Code by hand to bind data</span></span>

<span data-ttu-id="a74da-125">Codieren von hand umfasst:</span><span class="sxs-lookup"><span data-stu-id="a74da-125">Coding by hand involves:</span></span>

1. <span data-ttu-id="a74da-126">Lesen eines Werts</span><span class="sxs-lookup"><span data-stu-id="a74da-126">Reading a value</span></span>
2. <span data-ttu-id="a74da-127">Überprüft, ob es null ist.</span><span class="sxs-lookup"><span data-stu-id="a74da-127">Checking if it's null</span></span>
3. <span data-ttu-id="a74da-128">In einen entsprechenden Typ konvertiert wird</span><span class="sxs-lookup"><span data-stu-id="a74da-128">Converting it to an appropriate type</span></span>
4. <span data-ttu-id="a74da-129">Überprüfen die Konvertierung Erfolg</span><span class="sxs-lookup"><span data-stu-id="a74da-129">Checking conversion success</span></span>
5. <span data-ttu-id="a74da-130">Eine Abfrage mit dem konvertierten Wert</span><span class="sxs-lookup"><span data-stu-id="a74da-130">Making a query with the converted value</span></span> 

<span data-ttu-id="a74da-131">Bei diesem Ansatz müssen Sie die vollständige Kontrolle über Ihre Datenzugriffslogik.</span><span class="sxs-lookup"><span data-stu-id="a74da-131">With this approach, you have full control over your data-access logic.</span></span>

### <a name="use-model-binding-to-bind-data"></a><span data-ttu-id="a74da-132">Verwenden von modellbindung zum Binden von Daten</span><span class="sxs-lookup"><span data-stu-id="a74da-132">Use model binding to bind data</span></span>

<span data-ttu-id="a74da-133">Sie binden die Ergebnisse mit sehr viel weniger Code, und es bietet Ihnen die Möglichkeit, die die Funktionalität in der gesamten Anwendung wiederverwenden, mit der modellbindung.</span><span class="sxs-lookup"><span data-stu-id="a74da-133">With model binding, you bind results with far less code and it gives you the ability to reuse the functionality throughout your application.</span></span> <span data-ttu-id="a74da-134">Arbeiten mit Code fokussierter Datenzugriffslogik vereinfacht, dabei aber nicht auf ein Framework für umfangreiche und Datenbindung.</span><span class="sxs-lookup"><span data-stu-id="a74da-134">It simplifies working with code-focused data-access logic while still providing a rich, data-binding framework.</span></span>

## <a name="display-products"></a><span data-ttu-id="a74da-135">Produkte anzeigen</span><span class="sxs-lookup"><span data-stu-id="a74da-135">Display products</span></span>

<span data-ttu-id="a74da-136">In diesem Tutorial verwenden Sie die modellbindung zum Binden von Daten.</span><span class="sxs-lookup"><span data-stu-id="a74da-136">In this tutorial, you use model binding to bind data.</span></span> <span data-ttu-id="a74da-137">Zum Konfigurieren eines Datensteuerelements, um die modellbindung zu verwenden, um Daten auszuwählen, die Sie festlegen des Steuerelements `SelectMethod` Eigenschaft für eine Methode im Code der Seite.</span><span class="sxs-lookup"><span data-stu-id="a74da-137">To configure a data control to use model binding to select data, you set the control's `SelectMethod` property to a method in the page's code.</span></span> <span data-ttu-id="a74da-138">Das Steuerelement ruft die Methode zum geeigneten Zeitpunkt im Lebenszyklus Seite und die zurückgegebenen Daten automatisch gebunden.</span><span class="sxs-lookup"><span data-stu-id="a74da-138">The data control calls the method at the appropriate time in the page life cycle and automatically binds the returned data.</span></span> <span data-ttu-id="a74da-139">Es ist nicht erforderlich, explizit aufrufen, die `DataBind` Methode.</span><span class="sxs-lookup"><span data-stu-id="a74da-139">There's no need to explicitly call the `DataBind` method.</span></span>

<span data-ttu-id="a74da-140">Arbeiten die folgenden Schritte aus, die Sie ändern *"ProductList.aspx"* Markup zum Anzeigen von Produkten.</span><span class="sxs-lookup"><span data-stu-id="a74da-140">Working through the following steps, you modify *ProductList.aspx* markup to display products.</span></span>

1. <span data-ttu-id="a74da-141">In **Projektmappen-Explorer**öffnen *"ProductList.aspx"*.</span><span class="sxs-lookup"><span data-stu-id="a74da-141">In **Solution Explorer**, open *ProductList.aspx*.</span></span>

2. <span data-ttu-id="a74da-142">Ersetzen Sie das vorhandene Markup durch das folgende Markup:</span><span class="sxs-lookup"><span data-stu-id="a74da-142">Replace the existing markup with the following markup:</span></span> 

    [!code-aspx-csharp[Main](display_data_items_and_details/samples/sample1.aspx)]

<span data-ttu-id="a74da-143">Das vorhergehende Markup verwendet eine **ListView** Steuerelement mit dem Namen `productList` zum Anzeigen von Produkten.</span><span class="sxs-lookup"><span data-stu-id="a74da-143">The preceding markup uses a **ListView** control named `productList` to display products.</span></span>

[!code-aspx-csharp[Main](display_data_items_and_details/samples/sample2.aspx)]

<span data-ttu-id="a74da-144">Mit Vorlagen und Stile, Sie definieren, wie die **ListView** Steuerelement Daten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a74da-144">With templates and styles, you define how the **ListView** control displays data.</span></span> <span data-ttu-id="a74da-145">Es ist nützlich für Daten in einer sich wiederholenden Struktur.</span><span class="sxs-lookup"><span data-stu-id="a74da-145">It's useful for data in any repeating structure.</span></span> <span data-ttu-id="a74da-146">Obwohl dies **ListView** Beispiel einfach Daten anzeigt, können Sie auch, ohne Code, Benutzer zu bearbeiten, einfügen und Löschen von Daten, und zu sortieren und Paging Daten.</span><span class="sxs-lookup"><span data-stu-id="a74da-146">Though this **ListView** example simply displays database data, you can also, without code, enable users to edit, insert, and delete data, and to sort and page data.</span></span>

<span data-ttu-id="a74da-147">Beim Festlegen der `ItemType` -Eigenschaft in der **ListView** zu steuern, die XPath-Datenbindungsausdruck `Item` verfügbar ist und das Steuerelement wird stark typisiert.</span><span class="sxs-lookup"><span data-stu-id="a74da-147">When you set the `ItemType` property in the **ListView** control, the data-binding expression `Item` is available and the control becomes strongly typed.</span></span> <span data-ttu-id="a74da-148">Wie im vorherigen Tutorial erwähnt, können Sie Details zum Element-Objekt mit IntelliSense, auswählen, z. B. Angeben der `ProductName`:</span><span class="sxs-lookup"><span data-stu-id="a74da-148">As mentioned in the previous tutorial, you can select Item object details with IntelliSense, such as specifying the `ProductName`:</span></span>

![Anzeigen von Daten Elementen und Details - IntelliSense](display_data_items_and_details/_static/image1.png)

<span data-ttu-id="a74da-150">Geben Sie mit der modellbindung eine `SelectMethod` Wert (`GetProducts`).</span><span class="sxs-lookup"><span data-stu-id="a74da-150">With model binding, you're specifying a `SelectMethod` value (`GetProducts`).</span></span> <span data-ttu-id="a74da-151">Dies ist die Methode, die Sie, um den Code hinzufügen Verzug für Produkte im nächsten Schritt.</span><span class="sxs-lookup"><span data-stu-id="a74da-151">This is the method you add to the code behind to display products in the next step.</span></span>

### <a name="add-code-to-display-products"></a><span data-ttu-id="a74da-152">Fügen Sie Code zum Anzeigen von Produkten</span><span class="sxs-lookup"><span data-stu-id="a74da-152">Add code to display products</span></span>

<span data-ttu-id="a74da-153">In diesem Schritt fügen Sie Code zum Auffüllen der **ListView** -Steuerelement mit Produktdaten für die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="a74da-153">In this step, you add code to populate the **ListView** control with database product data.</span></span> <span data-ttu-id="a74da-154">Der Code unterstützt alle Produkte und Produkte einzelne Kategorie anzeigt.</span><span class="sxs-lookup"><span data-stu-id="a74da-154">The code supports showing all products and individual category products.</span></span>

1. <span data-ttu-id="a74da-155">In **Projektmappen-Explorer**, mit der rechten Maustaste *"ProductList.aspx"* und wählen Sie dann **Ansichtscode**.</span><span class="sxs-lookup"><span data-stu-id="a74da-155">In **Solution Explorer**, right-click *ProductList.aspx* and then select **View Code**.</span></span>
2. <span data-ttu-id="a74da-156">Ersetzen Sie den vorhandenen Code in die *ProductList.aspx.cs* Datei mit diesem:</span><span class="sxs-lookup"><span data-stu-id="a74da-156">Replace the existing code in the *ProductList.aspx.cs* file with this:</span></span>   

    [!code-csharp[Main](display_data_items_and_details/samples/sample3.cs)]

<span data-ttu-id="a74da-157">Dieser Code zeigt die `GetProducts` Methode, die die **ListView** des Steuerelements `ItemType` -Eigenschaft verweist auf *"ProductList.aspx"*.</span><span class="sxs-lookup"><span data-stu-id="a74da-157">This code shows the `GetProducts` method that the **ListView** control's `ItemType` property references in *ProductList.aspx*.</span></span> <span data-ttu-id="a74da-158">Um die Ergebnisse an eine bestimmte Datenbankkategorie beschränken möchten, im Code wird die `categoryId` Wert aus der Abfragezeichenfolge übergeben *"ProductList.aspx"*.</span><span class="sxs-lookup"><span data-stu-id="a74da-158">To limit the results to a specific database category, the code sets the `categoryId` value from the query string passed to *ProductList.aspx*.</span></span> <span data-ttu-id="a74da-159">Die `QueryStringAttribute` -Klasse in der `System.Web.ModelBinding` Namespace wird verwendet, um das Abrufen von Abfragezeichenfolgen-Variablen `id`Werts.</span><span class="sxs-lookup"><span data-stu-id="a74da-159">The `QueryStringAttribute` class in the `System.Web.ModelBinding` namespace is used to retrieve the query string variable `id`'s value.</span></span> <span data-ttu-id="a74da-160">Dies weist die modellbindung, zur Laufzeit der Wert einer Abfragezeichenfolge zum Binden der `categoryId` Parameter.</span><span class="sxs-lookup"><span data-stu-id="a74da-160">This instructs model binding to, at run time, bind a query string value to the `categoryId` parameter.</span></span>

<span data-ttu-id="a74da-161">Wenn eine gültige Kategorie (`categoryId`) wird übergeben, die Ergebnisse sind nur für diese Kategorie in der Datenbank waren.</span><span class="sxs-lookup"><span data-stu-id="a74da-161">When a valid category (`categoryId`) is passed, the results are limited to that category's database products.</span></span> <span data-ttu-id="a74da-162">Beispielsweise, wenn die *ProductsList.aspx* Seiten-URL ist dies:</span><span class="sxs-lookup"><span data-stu-id="a74da-162">For instance, if the *ProductsList.aspx* page URL is this:</span></span>

[!code-console[Main](display_data_items_and_details/samples/sample4.cmd)]

<span data-ttu-id="a74da-163">Die Seite zeigt nur die Produkte, in denen die `categoryId` gleich `1`.</span><span class="sxs-lookup"><span data-stu-id="a74da-163">The page displays only the products where the `categoryId` equals `1`.</span></span>

<span data-ttu-id="a74da-164">Wenn keine Abfragezeichenfolge übergeben wird, werden alle Produkte angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a74da-164">All products are displayed if no query string is passed.</span></span>

<span data-ttu-id="a74da-165">Die Quellen für diese Methoden werden aufgerufen *Wert Anbieter* (wie z. B. `QueryString`), und die Parameterattribute, die angeben, welcher Wertanbieter verwendet heißen *Wertattribute Anbieter* ( wie z. B. `id`).</span><span class="sxs-lookup"><span data-stu-id="a74da-165">The value sources for these methods are called *value providers* (such as `QueryString`), and the parameter attributes that indicate which value provider to use are called *value provider attributes* (such as `id`).</span></span> <span data-ttu-id="a74da-166">ASP.NET umfasst Wertanbieter und die Attribute für alle typischen Web Forms-Anwendung Benutzer-Eingabequellen.</span><span class="sxs-lookup"><span data-stu-id="a74da-166">ASP.NET includes value providers and attributes for all typical Web Forms application user input sources.</span></span> <span data-ttu-id="a74da-167">Dazu gehören die Abfragezeichenfolgen, Cookies, Formularwerte, Steuerelemente, Ansichtszustand, Sitzungsstatus und Profileigenschaften.</span><span class="sxs-lookup"><span data-stu-id="a74da-167">These include the query string, cookies, form values, controls, view state, session state, and profile properties.</span></span> <span data-ttu-id="a74da-168">Sie können auch benutzerdefinierte Wertanbieter erstellen.</span><span class="sxs-lookup"><span data-stu-id="a74da-168">You can also write custom value providers.</span></span>

### <a name="run-the-application"></a><span data-ttu-id="a74da-169">Ausführen der Anwendung</span><span class="sxs-lookup"><span data-stu-id="a74da-169">Run the application</span></span>

<span data-ttu-id="a74da-170">Führen Sie die Anwendung jetzt alle Produkte oder eine Produktkategorie angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a74da-170">Run the application now to view all products or a category's products.</span></span>

1. <span data-ttu-id="a74da-171">Drücken Sie in Visual Studio **F5** zum Ausführen der Anwendung.</span><span class="sxs-lookup"><span data-stu-id="a74da-171">In Visual Studio, press **F5** to run the application.</span></span>
 <span data-ttu-id="a74da-172">Der Browser wird geöffnet und zeigt die *"default.aspx"* Seite.</span><span class="sxs-lookup"><span data-stu-id="a74da-172">The browser opens and shows the *Default.aspx* page.</span></span>

2. <span data-ttu-id="a74da-173">Wählen Sie im Menü "Product Category" **Autos**.</span><span class="sxs-lookup"><span data-stu-id="a74da-173">From the product category menu, select **Cars**.</span></span>

   <span data-ttu-id="a74da-174">Die *"ProductList.aspx"* Seite angezeigt wird, nur die Produkte aus der **Autos** Kategorie.</span><span class="sxs-lookup"><span data-stu-id="a74da-174">The *ProductList.aspx* page appears, showing only products from the **Cars** category.</span></span> <span data-ttu-id="a74da-175">Weiter unten in diesem Tutorial zeigen Sie die Produktdetails.</span><span class="sxs-lookup"><span data-stu-id="a74da-175">Later in this tutorial, you display product details.</span></span>

    ![Anzeigen von Daten Elementen und Details - Autos](display_data_items_and_details/_static/image2.png)

3. <span data-ttu-id="a74da-177">Wählen Sie **Produkte** im oberen Menü aus.</span><span class="sxs-lookup"><span data-stu-id="a74da-177">Select **Products** from the top menu.</span></span>
 <span data-ttu-id="a74da-178">Die *"ProductList.aspx"* Seite zeigt jetzt alle Produkte.</span><span class="sxs-lookup"><span data-stu-id="a74da-178">The *ProductList.aspx* page now displays all products.</span></span> 

    ![Anzeigen von Daten Elementen und Details - Produkte](display_data_items_and_details/_static/image3.png)

4. <span data-ttu-id="a74da-180">Schließen Sie den Browser, und kehren Sie zu Visual Studio zurück.</span><span class="sxs-lookup"><span data-stu-id="a74da-180">Close the browser and return to Visual Studio.</span></span>

### <a name="add-a-data-control-to-display-product-details"></a><span data-ttu-id="a74da-181">Fügen Sie ein Steuerelement zum Anzeigen von Produktdetails hinzu.</span><span class="sxs-lookup"><span data-stu-id="a74da-181">Add a Data Control to display product details</span></span>

<span data-ttu-id="a74da-182">Ändern der *ProductDetails.aspx* Markup, die Sie im vorherigen Tutorial zur Anzeige von Informationen von bestimmten Produkt hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="a74da-182">Modify the *ProductDetails.aspx* markup that you added in the previous tutorial to display specific product information:</span></span>

1. <span data-ttu-id="a74da-183">In **Projektmappen-Explorer**öffnen *ProductDetails.aspx*.</span><span class="sxs-lookup"><span data-stu-id="a74da-183">In **Solution Explorer**, open *ProductDetails.aspx*.</span></span>

2. <span data-ttu-id="a74da-184">Ersetzen Sie das vorhandene Markup durch dieses Markup an:</span><span class="sxs-lookup"><span data-stu-id="a74da-184">Replace the existing markup with this markup:</span></span>

    [!code-aspx-csharp[Main](display_data_items_and_details/samples/sample5.aspx)] 

<span data-ttu-id="a74da-185">Dieses Markup wird ein **FormView** -Steuerelement zum Anzeigen von Details für bestimmte Produkte.</span><span class="sxs-lookup"><span data-stu-id="a74da-185">This markup uses a **FormView** control to display specific product details.</span></span> <span data-ttu-id="a74da-186">Verwendet Methoden wie zum Anzeigen von Daten, in denen *"ProductList.aspx"*.</span><span class="sxs-lookup"><span data-stu-id="a74da-186">It uses methods like those used to display data in *ProductList.aspx*.</span></span> <span data-ttu-id="a74da-187">Die **FormView** Steuerelement wird verwendet, um ein einzelner Datensatz aus einer Datenquelle zu einem Zeitpunkt angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a74da-187">The **FormView** control is used to display a single record at a time from a data source.</span></span> <span data-ttu-id="a74da-188">Bei Verwendung der **FormView** -Steuerelement, erstellen Sie Vorlagen, um das Anzeigen und Bearbeiten von datengebundenen Werten.</span><span class="sxs-lookup"><span data-stu-id="a74da-188">When you use the **FormView** control, you create templates to display and edit data-bound values.</span></span> <span data-ttu-id="a74da-189">Diese Vorlagen enthalten Steuerelemente binden von Ausdrücken, und formatieren, die definieren, Design und die Funktionen des Formulars.</span><span class="sxs-lookup"><span data-stu-id="a74da-189">These templates contain controls, binding expressions, and formatting that define the form's look and functionality.</span></span>

<span data-ttu-id="a74da-190">Verbinden den bisherigen Markupcode mit der Datenbank ist zusätzlichen Code erforderlich.</span><span class="sxs-lookup"><span data-stu-id="a74da-190">Connecting the previous markup to the database requires additional code.</span></span>

1. <span data-ttu-id="a74da-191">In **Projektmappen-Explorer**, mit der rechten Maustaste *ProductDetails.aspx* und wählen Sie dann **Ansichtscode**.</span><span class="sxs-lookup"><span data-stu-id="a74da-191">In **Solution Explorer**, right-click *ProductDetails.aspx* and then select **View Code**.</span></span>  
   <span data-ttu-id="a74da-192">Die *ProductDetails.aspx.cs* Datei wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a74da-192">The *ProductDetails.aspx.cs* file is displayed.</span></span>

2. <span data-ttu-id="a74da-193">Ersetzen Sie dabei den vorhandenen Code:</span><span class="sxs-lookup"><span data-stu-id="a74da-193">Replace the existing code with this:</span></span>   

    [!code-csharp[Main](display_data_items_and_details/samples/sample6.cs)]

<span data-ttu-id="a74da-194">Dieser Code überprüft wird, für eine "`productID`" Wert der Abfragezeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="a74da-194">This code checks for a "`productID`" query string value.</span></span> <span data-ttu-id="a74da-195">Wenn Sie ein gültiger Wert gefunden wird, wird das entsprechende Produkt angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a74da-195">If a valid value is found, the matching product is displayed.</span></span> <span data-ttu-id="a74da-196">Wenn die Abfragezeichenfolge nicht gefunden oder der Wert ungültig ist, wird kein Produkt angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a74da-196">If the query string isn't found, or its value isn't valid, no product is displayed.</span></span>

### <a name="run-the-application"></a><span data-ttu-id="a74da-197">Ausführen der Anwendung</span><span class="sxs-lookup"><span data-stu-id="a74da-197">Run the application</span></span>

<span data-ttu-id="a74da-198">Jetzt können Sie die Anwendung bestimmte Produktdetails basierend auf der Produkt-ID finden Sie unter ausführen.</span><span class="sxs-lookup"><span data-stu-id="a74da-198">Now you can run the application to see specific product details based on product ID.</span></span>

1. <span data-ttu-id="a74da-199">Drücken Sie in Visual Studio **F5** zum Ausführen der Anwendung.</span><span class="sxs-lookup"><span data-stu-id="a74da-199">In Visual Studio, press **F5** to run the application.</span></span>  
 <span data-ttu-id="a74da-200">Der Browser öffnet *"default.aspx"*.</span><span class="sxs-lookup"><span data-stu-id="a74da-200">The browser opens to *Default.aspx*.</span></span>

2. <span data-ttu-id="a74da-201">Wählen Sie im Menü Kategorie **Boote**.</span><span class="sxs-lookup"><span data-stu-id="a74da-201">From the category menu, select **Boats**.</span></span>  
 <span data-ttu-id="a74da-202">Die *"ProductList.aspx"* angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="a74da-202">The *ProductList.aspx* page is displayed.</span></span>

3. <span data-ttu-id="a74da-203">Wählen Sie **Artikel also**.</span><span class="sxs-lookup"><span data-stu-id="a74da-203">Select **Paper Boat**.</span></span>  
 <span data-ttu-id="a74da-204">Die *ProductDetails.aspx* angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="a74da-204">The *ProductDetails.aspx* page is displayed.</span></span>   

    ![Anzeigen von Daten Elementen und Details - Produkte](display_data_items_and_details/_static/image4.png)

<span data-ttu-id="a74da-206">Im nächsten Tutorial fügen Sie einen Einkaufswagen gelegt hat, an die Anwendung Wingtip Toys.</span><span class="sxs-lookup"><span data-stu-id="a74da-206">In the next tutorial, you add a shopping cart to the Wingtip Toys application.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a74da-207">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a74da-207">Additional resources</span></span>

[<span data-ttu-id="a74da-208">Abrufen und Anzeigen von Daten mit modellbindung und Web forms</span><span class="sxs-lookup"><span data-stu-id="a74da-208">Retrieving and displaying data with model binding and web forms</span></span>](../../presenting-and-managing-data/model-binding/retrieving-data.md)

> [!div class="step-by-step"]
> <span data-ttu-id="a74da-209">[Zurück](ui_and_navigation.md)
> [Weiter](shopping-cart.md)</span><span class="sxs-lookup"><span data-stu-id="a74da-209">[Previous](ui_and_navigation.md)
[Next](shopping-cart.md)</span></span>
