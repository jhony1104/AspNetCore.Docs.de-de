---
uid: web-api/overview/data/using-web-api-with-entity-framework/part-1
title: Mithilfe von Web-API 2 mit Entitätsframework 6 | Microsoft-Dokumentation
author: MikeWasson
description: In diesem Tutorial erfahren Sie, dass die Grundlagen der Erstellung einer Web-Anwendung mit einer ASP.NET Web-API-back-End. Das Lernprogramm verwendet Entity Framework 6 für das Layout der Daten...
ms.author: riande
ms.date: 01/17/2019
ms.assetid: e879487e-dbcd-4b33-b092-d67c37ae768c
msc.legacyurl: /web-api/overview/data/using-web-api-with-entity-framework/part-1
msc.type: authoredcontent
ms.openlocfilehash: 266c808e3525787181038d2de473194989039e02
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236522"
---
<a name="using-web-api-2-with-entity-framework-6"></a><span data-ttu-id="57d24-104">Mithilfe von Web-API 2 mit Entitätsframework 6</span><span class="sxs-lookup"><span data-stu-id="57d24-104">Using Web API 2 with Entity Framework 6</span></span>
====================

[<span data-ttu-id="57d24-105">Abgeschlossenes Projekt herunterladen</span><span class="sxs-lookup"><span data-stu-id="57d24-105">Download Completed Project</span></span>](https://github.com/MikeWasson/BookService)

> <span data-ttu-id="57d24-106">In diesem Tutorial erfahren Sie, dass die Grundlagen der Erstellung einer Web-Anwendung mit einer ASP.NET Web-API-back-End.</span><span class="sxs-lookup"><span data-stu-id="57d24-106">This tutorial teaches you the basics of creating a web application with an ASP.NET Web API back end.</span></span> <span data-ttu-id="57d24-107">Das Lernprogramm verwendet Entity Framework 6 für die Datenschicht, und klicken Sie auf "Knockout.js", für die clientseitige JavaScript-Anwendung.</span><span class="sxs-lookup"><span data-stu-id="57d24-107">The tutorial uses Entity Framework 6 for the data layer, and Knockout.js for the client-side JavaScript application.</span></span> <span data-ttu-id="57d24-108">Das Tutorial veranschaulicht auch zum Bereitstellen der app in Azure App Service-Web-Apps.</span><span class="sxs-lookup"><span data-stu-id="57d24-108">The tutorial also shows how to deploy the app to Azure App Service Web Apps.</span></span>
>
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="57d24-109">Softwareversionen, die in diesem Tutorial verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="57d24-109">Software versions used in the tutorial</span></span>
>
> - <span data-ttu-id="57d24-110">Web-API 2.1</span><span class="sxs-lookup"><span data-stu-id="57d24-110">Web API 2.1</span></span>
> - <span data-ttu-id="57d24-111">Visual Studio 2017 (Visual Studio 2017 herunterladen [hier](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017))</span><span class="sxs-lookup"><span data-stu-id="57d24-111">Visual Studio 2017 (download Visual Studio 2017 [here](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017))</span></span>
> - <span data-ttu-id="57d24-112">Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="57d24-112">Entity Framework 6</span></span>
> - <span data-ttu-id="57d24-113">.NET 4.7</span><span class="sxs-lookup"><span data-stu-id="57d24-113">.NET 4.7</span></span>
> - <span data-ttu-id="57d24-114">[Knockout.js](http://knockoutjs.com/) 3.1</span><span class="sxs-lookup"><span data-stu-id="57d24-114">[Knockout.js](http://knockoutjs.com/) 3.1</span></span>

<span data-ttu-id="57d24-115">Dieses Tutorial verwendet ASP.NET Web API 2 mit Entity Framework 6, um eine Webanwendung erstellen, die eine Back-End-Datenbank ändert.</span><span class="sxs-lookup"><span data-stu-id="57d24-115">This tutorial uses ASP.NET Web API 2 with Entity Framework 6 to create a web application that manipulates a back-end database.</span></span> <span data-ttu-id="57d24-116">Hier ist ein Screenshot der Anwendung, die Sie erstellen.</span><span class="sxs-lookup"><span data-stu-id="57d24-116">Here is a screen shot of the application that you will create.</span></span>

[![](part-1/_static/image2.png)](part-1/_static/image1.png)

<span data-ttu-id="57d24-117">Die app verwendet eine Single-Page-Anwendung (SPA).</span><span class="sxs-lookup"><span data-stu-id="57d24-117">The app uses a single-page application (SPA) design.</span></span> <span data-ttu-id="57d24-118">"Single-Page-Anwendung" ist der allgemeine Begriff für eine Webanwendung, die eine einzelne HTML-Seite lädt, und klicken Sie dann die Seite wird dynamisch aktualisiert, anstatt neue Seiten laden.</span><span class="sxs-lookup"><span data-stu-id="57d24-118">"Single-page application" is the general term for a web application that loads a single HTML page and then updates the page dynamically, instead of loading new pages.</span></span> <span data-ttu-id="57d24-119">Nach dem ersten Laden der Seite werden die app mit dem Server über AJAX-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="57d24-119">After the initial page load, the app talks with the server through AJAX requests.</span></span> <span data-ttu-id="57d24-120">Die AJAX-Anforderungen JSON-Daten zurückgeben, die die app verwendet wird, um die Benutzeroberfläche zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="57d24-120">The AJAX requests return JSON data, which the app uses to update the UI.</span></span>

<span data-ttu-id="57d24-121">AJAX ist nicht neu, aber heute bestehen die JavaScript-Frameworks, die zum Erstellen und Verwalten einer großen anspruchsvollen SPA-Anwendung zu erleichtern.</span><span class="sxs-lookup"><span data-stu-id="57d24-121">AJAX isn't new, but today there are JavaScript frameworks that make it easier to build and maintain a large sophisticated SPA application.</span></span> <span data-ttu-id="57d24-122">Dieses Tutorial verwendet ["Knockout.js"](http://knockoutjs.com/), aber Sie können alle JavaScript-Clientframework verwenden.</span><span class="sxs-lookup"><span data-stu-id="57d24-122">This tutorial uses [Knockout.js](http://knockoutjs.com/), but you can use any JavaScript client framework.</span></span>

<span data-ttu-id="57d24-123">Hier sind die wichtigsten Bausteine für diese app aus:</span><span class="sxs-lookup"><span data-stu-id="57d24-123">Here are the main building blocks for this app:</span></span>

- <span data-ttu-id="57d24-124">ASP.NET MVC erstellt, die HTML-Seite.</span><span class="sxs-lookup"><span data-stu-id="57d24-124">ASP.NET MVC creates the HTML page.</span></span>
- <span data-ttu-id="57d24-125">ASP.NET Web-API verarbeitet die AJAX-Anforderungen und JSON-Daten zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="57d24-125">ASP.NET Web API handles the AJAX requests and returns JSON data.</span></span>
- <span data-ttu-id="57d24-126">Knockout.js-Datenbindung der HTML-Elemente, die JSON-Daten.</span><span class="sxs-lookup"><span data-stu-id="57d24-126">Knockout.js data-binds the HTML elements to the JSON data.</span></span>
- <span data-ttu-id="57d24-127">Entitätsframework finden Sie in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="57d24-127">Entity Framework talks to the database.</span></span>

## <a name="see-this-app-running-on-azure"></a><span data-ttu-id="57d24-128">Finden Sie unter dieser app in Azure ausführen</span><span class="sxs-lookup"><span data-stu-id="57d24-128">See this app running on Azure</span></span>

<span data-ttu-id="57d24-129">Möchten Sie die fertige Website als live-Web-app ausgeführt wird, finden Sie unter?</span><span class="sxs-lookup"><span data-stu-id="57d24-129">Would you like to see the finished site running as a live web app?</span></span> <span data-ttu-id="57d24-130">Sie können eine vollständige Version der app beim Azure-Konto bereitstellen, durch die folgende Schaltfläche auswählen.</span><span class="sxs-lookup"><span data-stu-id="57d24-130">You can deploy a complete version of the app to your Azure account by selecting the following button.</span></span>

[![](http://azuredeploy.net/deploybutton.png)](https://azuredeploy.net/?WT.mc_id=deploy_azure_aspnet&repository=https://github.com/tfitzmac/BookService)

<span data-ttu-id="57d24-131">Sie benötigen ein Azure-Konto zum Bereitstellen dieser Lösung in Azure.</span><span class="sxs-lookup"><span data-stu-id="57d24-131">You need an Azure account to deploy this solution to Azure.</span></span> <span data-ttu-id="57d24-132">Wenn Sie nicht bereits über ein Konto verfügen, müssen Sie die folgenden Optionen aus:</span><span class="sxs-lookup"><span data-stu-id="57d24-132">If you do not already have an account, you have the following options:</span></span>

- <span data-ttu-id="57d24-133">[Öffnen Sie ein Azure-Konto kostenlos](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A443DD604) – Sie erhalten ein Guthaben zum Ausprobieren Zahlungspflichtiger Azure-Dienste nutzen können, und auch nach dem sie verwendet werden, bis können Sie das Konto behalten und kostenlose Azure-Dienste.</span><span class="sxs-lookup"><span data-stu-id="57d24-133">[Open an Azure account for free](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A443DD604) - You get credits you can use to try out paid Azure services, and even after they're used up you can keep the account and use free Azure services.</span></span>
- <span data-ttu-id="57d24-134">[MSDN-abonnentenvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A443DD604) -Ihr MSDN-Abonnement ein monatliches Guthaben, die Sie für zahlungspflichtige Azure-Dienste verwenden können.</span><span class="sxs-lookup"><span data-stu-id="57d24-134">[Activate MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A443DD604) - Your MSDN subscription gives you credits every month that you can use for paid Azure services.</span></span>

## <a name="create-the-project"></a><span data-ttu-id="57d24-135">Erstellen eines Projekts</span><span class="sxs-lookup"><span data-stu-id="57d24-135">Create the project</span></span>

<span data-ttu-id="57d24-136">Öffnen Sie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="57d24-136">Open Visual Studio.</span></span> <span data-ttu-id="57d24-137">Von der **Datei** , wählen Sie im Menü **neu**, und wählen Sie dann **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="57d24-137">From the **File** menu, select **New**, then select **Project**.</span></span> <span data-ttu-id="57d24-138">(Oder wählen Sie **neues Projekt** auf der Startseite.)</span><span class="sxs-lookup"><span data-stu-id="57d24-138">(Or select **New Project** on the Start page.)</span></span>

<span data-ttu-id="57d24-139">In der **neues Projekt** wählen Sie im Dialogfeld **Web** im linken Bereich und **ASP.NET-Webanwendung ((.NET Framework)** im mittleren Bereich.</span><span class="sxs-lookup"><span data-stu-id="57d24-139">In the **New Project** dialog, select **Web** in the left pane and **ASP.NET Web Application (.NET Framework)** in the middle pane.</span></span> <span data-ttu-id="57d24-140">Nennen Sie das Projekt **BookService** , und wählen Sie **OK**.</span><span class="sxs-lookup"><span data-stu-id="57d24-140">Name the project **BookService** and select **OK**.</span></span>

[![](part-1/_static/image11.png)](part-1/_static/image11.png)

<span data-ttu-id="57d24-141">In der **neues ASP.NET-Projekt** wählen Sie im Dialogfeld die **Web-API-** Vorlage.</span><span class="sxs-lookup"><span data-stu-id="57d24-141">In the **New ASP.NET Project** dialog, select the **Web API** template.</span></span>

[![](part-1/_static/image12.png)](part-1/_static/image12.png)


<span data-ttu-id="57d24-142">Klicken Sie auf **OK**, um das Projekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="57d24-142">Select **OK** to create the project.</span></span>

## <a name="configure-azure-settings-optional"></a><span data-ttu-id="57d24-143">Konfigurieren von Azure-Einstellungen (optional)</span><span class="sxs-lookup"><span data-stu-id="57d24-143">Configure Azure settings (optional)</span></span>

<span data-ttu-id="57d24-144">Nachdem Sie das Projekt erstellt haben, können Sie jederzeit auf Azure App Service-Web-Apps bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="57d24-144">After you create the project, you can choose to deploy to Azure App Service Web Apps at any time.</span></span> 

1. <span data-ttu-id="57d24-145">Projektmappen-Explorer mit der Maustaste auf Ihr Projekt, und wählen Sie **veröffentlichen**.</span><span class="sxs-lookup"><span data-stu-id="57d24-145">In Solution Explorer, right-click on your project and select **Publish**.</span></span>

2. <span data-ttu-id="57d24-146">Wählen Sie im Fenster, das angezeigt wird, **starten**.</span><span class="sxs-lookup"><span data-stu-id="57d24-146">In the window that appears, select **Start**.</span></span> <span data-ttu-id="57d24-147">Die **Veröffentlichungsziel** Dialogfeld wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="57d24-147">The **Pick a publish target** dialog box appears.</span></span>

   [![](part-1/_static/image14.png)](part-1/_static/image14.png)

3. <span data-ttu-id="57d24-148">Klicken Sie auf **Profil erstellen**.</span><span class="sxs-lookup"><span data-stu-id="57d24-148">Select **Create Profile**.</span></span> <span data-ttu-id="57d24-149">Das Dialogfeld **App Service erstellen** wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="57d24-149">The **Create App Service** dialog box appears.</span></span>

   [![](part-1/_static/image15.png)](part-1/_static/image15.png)

   <span data-ttu-id="57d24-150">Akzeptieren Sie die Standardeinstellungen, oder geben Sie unterschiedliche Werte für den Anwendungsnamen, die Ressourcengruppe, die hosting-Plan, Azure-Abonnement und geografischen Region.</span><span class="sxs-lookup"><span data-stu-id="57d24-150">Accept the defaults, or enter different values for the application name, resource group, hosting plan, Azure subscription, and geographical region.</span></span> 

4. <span data-ttu-id="57d24-151">Wählen Sie **erstellen Sie eine SQL­Datenbank**.</span><span class="sxs-lookup"><span data-stu-id="57d24-151">Select **Create a SQL database**.</span></span> <span data-ttu-id="57d24-152">Die **Konfigurieren von SQL Server** Dialogfeld wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="57d24-152">The **Configure SQL Server** dialog box appears.</span></span> 

   [![](part-1/_static/image16.png)](part-1/_static/image16.png)

   <span data-ttu-id="57d24-153">Akzeptieren Sie die Standardeinstellungen, oder geben Sie andere Werte ein.</span><span class="sxs-lookup"><span data-stu-id="57d24-153">Accept the defaults or enter different values.</span></span> <span data-ttu-id="57d24-154">Geben Sie eine **Adminstrator Username** und **Administratorkennwort** für Ihre neue Datenbank.</span><span class="sxs-lookup"><span data-stu-id="57d24-154">Enter an **Adminstrator Username** and **Administrator Password** for your new database.</span></span> <span data-ttu-id="57d24-155">Wählen Sie **OK** Wenn Sie fertig sind.</span><span class="sxs-lookup"><span data-stu-id="57d24-155">Select **OK** when you're done.</span></span> <span data-ttu-id="57d24-156">Die **App Service erstellen** Seite wird erneut angezeigt.</span><span class="sxs-lookup"><span data-stu-id="57d24-156">The **Create App Service** page reappears.</span></span>

5. <span data-ttu-id="57d24-157">Wählen Sie **erstellen** auf Ihr Profil zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="57d24-157">Select **Create** to create your profile.</span></span> <span data-ttu-id="57d24-158">In der unteren rechten Ecke, der angibt, dass die Bereitstellung ausgeführt wird, wird eine Meldung angezeigt.</span><span class="sxs-lookup"><span data-stu-id="57d24-158">A message appears in the lower-right corner indicating that deployment is in progress.</span></span> <span data-ttu-id="57d24-159">Nach einer kurzen Zeit die **veröffentlichen** Fenster wird erneut angezeigt.</span><span class="sxs-lookup"><span data-stu-id="57d24-159">After a short while, the **Publish** window reappears.</span></span>

    [![](part-1/_static/image17.png)](part-1/_static/image17.png)
   
    <span data-ttu-id="57d24-160">Das Profil, das Sie erstellt haben, zum Bereitstellen der app ist jetzt verfügbar.</span><span class="sxs-lookup"><span data-stu-id="57d24-160">The profile you created to deploy the app is now available.</span></span> 


> [!div class="step-by-step"]
> [<span data-ttu-id="57d24-161">Nächste</span><span class="sxs-lookup"><span data-stu-id="57d24-161">Next</span></span>](part-2.md)
