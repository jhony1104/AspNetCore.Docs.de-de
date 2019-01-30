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
<a name="using-page-inspector-in-aspnet-mvc"></a><span data-ttu-id="d0370-104">Verwenden der Seitenprüfung in ASP.NET MVC</span><span class="sxs-lookup"><span data-stu-id="d0370-104">Using Page Inspector in ASP.NET MVC</span></span>
====================
<span data-ttu-id="d0370-105">von Tim Ammann</span><span class="sxs-lookup"><span data-stu-id="d0370-105">by Tim Ammann</span></span>

> <span data-ttu-id="d0370-106">Der Seitenprüfung in Visual Studio 2012 ist ein Webentwicklungstool mit einem integrierten Browser.</span><span class="sxs-lookup"><span data-stu-id="d0370-106">Page Inspector in Visual Studio 2012 is a web development tool with an integrated browser.</span></span> <span data-ttu-id="d0370-107">Auswählen eines Elements in der integrierten Browser und der Seitenprüfung sofort hervorgehoben, Quell- und CSS-des-Elements.</span><span class="sxs-lookup"><span data-stu-id="d0370-107">Select any element in the integrated browser, and Page Inspector instantly highlights the element's source and CSS.</span></span> <span data-ttu-id="d0370-108">Sie können eine MVC-Ansicht navigieren, finden die Quellen der gerenderten Markup, und schnell Browsertools direkt in Visual Studio-Umgebung verwenden.</span><span class="sxs-lookup"><span data-stu-id="d0370-108">You can browse any MVC view, quickly find the sources of rendered markup, and use browser tools right within the Visual Studio environment.</span></span>
> 
> [<span data-ttu-id="d0370-109">Video ansehen</span><span class="sxs-lookup"><span data-stu-id="d0370-109">Watch the Video</span></span>](../../videos/mvc-4/using-page-inspector-in-aspnet-mvc.md)
> 
> <span data-ttu-id="d0370-110">Dieses Tutorial veranschaulicht, wie Überprüfungsmodus zu aktivieren und schnell suchen und Bearbeiten von Markup und CSS in Ihrer Webprojekt.</span><span class="sxs-lookup"><span data-stu-id="d0370-110">This tutorial shows how to enable Inspection Mode, and then quickly locate and edit markup and CSS within your web project.</span></span> <span data-ttu-id="d0370-111">Das Tutorial verwendet ein MVC-Projekt, aber Sie können auch die Seitenprüfung für [Web Forms](https://go.microsoft.com/?linkid=9802001) und anderen ASP.NET-Anwendungen.</span><span class="sxs-lookup"><span data-stu-id="d0370-111">The tutorial uses an MVC Project, but you can also use Page Inspector for [Web Forms](https://go.microsoft.com/?linkid=9802001) and other ASP.NET applications.</span></span>
> 
> <span data-ttu-id="d0370-112">Das Tutorial enthält die folgenden Abschnitte:</span><span class="sxs-lookup"><span data-stu-id="d0370-112">The tutorial has the following sections:</span></span>
> 
> - [<span data-ttu-id="d0370-113">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="d0370-113">Prerequisites</span></span>](#_1_prerequisites)
> - [<span data-ttu-id="d0370-114">Erstellen einer Webanwendung</span><span class="sxs-lookup"><span data-stu-id="d0370-114">Create a Web Application</span></span>](#_2_creating_a)
> - [<span data-ttu-id="d0370-115">Verwenden der Seitenprüfung, navigieren Sie zu einer Ansicht</span><span class="sxs-lookup"><span data-stu-id="d0370-115">Use Page Inspector to Browse to a View</span></span>](#_3_using_page)
> - [<span data-ttu-id="d0370-116">Überprüfungsmodus zu aktivieren</span><span class="sxs-lookup"><span data-stu-id="d0370-116">Enable Inspection Mode</span></span>](#_4_inspection_mode)
> - [<span data-ttu-id="d0370-117">Verwenden der Seitenprüfung Markup ändern</span><span class="sxs-lookup"><span data-stu-id="d0370-117">Use Page Inspector to Make Changes to Markup</span></span>](#_5_using_page)
> - [<span data-ttu-id="d0370-118">Überprüfungsmodus und HTML-Fenster</span><span class="sxs-lookup"><span data-stu-id="d0370-118">Inspection Mode and the HTML Window</span></span>](#_6_inspection_mode)
> - [<span data-ttu-id="d0370-119">Vorschau der CSS-Änderungen im Fenster Stile</span><span class="sxs-lookup"><span data-stu-id="d0370-119">Preview CSS Changes in the Styles window</span></span>](#_7_previewing_css)
> - [<span data-ttu-id="d0370-120">CSS Auto Sync</span><span class="sxs-lookup"><span data-stu-id="d0370-120">CSS Auto Sync</span></span>](#css_auto_sync)
> - [<span data-ttu-id="d0370-121">Mithilfe der CSS-Farbauswahl</span><span class="sxs-lookup"><span data-stu-id="d0370-121">Using the CSS Color Picker</span></span>](#css_color_picker)
> - [<span data-ttu-id="d0370-122">Zuordnen von dynamische Seitenelemente zu JavaScript</span><span class="sxs-lookup"><span data-stu-id="d0370-122">Mapping Dynamic Page Elements to JavaScript</span></span>](#map_dynamic_elements)


<a id="_prerequisites"></a><a id="_1_prerequisites"></a>

## <a name="prerequisites"></a><span data-ttu-id="d0370-123">Vorraussetzungen</span><span class="sxs-lookup"><span data-stu-id="d0370-123">Prerequisites</span></span>

- <span data-ttu-id="d0370-124">[Visual Studio 2012](https://www.microsoft.com/visualstudio/11) oder [Visual Studio Express 2012 für Web](https://www.microsoft.com/visualstudio/11/downloads#express-web).</span><span class="sxs-lookup"><span data-stu-id="d0370-124">[Visual Studio 2012](https://www.microsoft.com/visualstudio/11) or [Visual Studio Express 2012 for Web](https://www.microsoft.com/visualstudio/11/downloads#express-web).</span></span>

> [!NOTE]
> <span data-ttu-id="d0370-125">Rufen Sie die neueste Version der Seitenprüfung mit [Webplattform-Installer](https://go.microsoft.com/fwlink/?LinkId=255386) Windows Azure SDK für .NET 2.0 zu installieren.</span><span class="sxs-lookup"><span data-stu-id="d0370-125">To get the latest version of Page Inspector, use [Web Platform Installer](https://go.microsoft.com/fwlink/?LinkId=255386) to install the Windows Azure SDK for .NET 2.0.</span></span>


<span data-ttu-id="d0370-126">Die Seitenprüfung ist Microsoft Web Developer Tools enthalten.</span><span class="sxs-lookup"><span data-stu-id="d0370-126">Page Inspector is bundled with Microsoft Web Developer Tools.</span></span> <span data-ttu-id="d0370-127">Die neueste Version ist 1.3.</span><span class="sxs-lookup"><span data-stu-id="d0370-127">The latest version is 1.3.</span></span> <span data-ttu-id="d0370-128">Um die Version haben Sie, Visual Studio ausführen, und wählen Sie **Info zu Microsoft Visual Studio** aus der **Hilfe** Menü.</span><span class="sxs-lookup"><span data-stu-id="d0370-128">To check which version you have, run Visual Studio and select **About Microsoft Visual Studio** from the **Help** menu.</span></span>

<a id="_creating_a_web"></a><a id="_2_creating_a"></a>

## <a name="create-a-web-application"></a><span data-ttu-id="d0370-129">Erstellen einer Webanwendung</span><span class="sxs-lookup"><span data-stu-id="d0370-129">Create a Web Application</span></span>

<span data-ttu-id="d0370-130">Erstellen Sie zunächst eine Webanwendung, der Seitenprüfung mit verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d0370-130">First, create a web application that you will use Page Inspector with.</span></span> <span data-ttu-id="d0370-131">Wählen Sie in Visual Studio **Datei** &gt; **neues Projekt**.</span><span class="sxs-lookup"><span data-stu-id="d0370-131">In Visual Studio, choose **File** &gt; **New Project**.</span></span> <span data-ttu-id="d0370-132">Erweitern Sie auf der linken Seite **Visual C#-** Option **Web**, und wählen Sie dann **ASP.NET MVC 4-Webanwendung**.</span><span class="sxs-lookup"><span data-stu-id="d0370-132">On the left, expand **Visual C#**, select **Web**, and then select **ASP.NET MVC4 Web Application**.</span></span>

![Neue ASP.NET MVC-Anwendung](using-page-inspector-in-aspnet-mvc/_static/image2.png)

<span data-ttu-id="d0370-134">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="d0370-134">Click **OK**.</span></span>

<span data-ttu-id="d0370-135">In der **neues ASP.NET MVC 4-Projekt** wählen Sie im Dialogfeld **Internetanwendung**.</span><span class="sxs-lookup"><span data-stu-id="d0370-135">In the **New ASP.NET MVC 4 Project** dialog box, select **Internet Application**.</span></span> <span data-ttu-id="d0370-136">Lassen Sie **Razor** als die standardansichts-Engine.</span><span class="sxs-lookup"><span data-stu-id="d0370-136">Leave **Razor** as the default view engine.</span></span>

![Neues ASP.NET MVC-Projekt – Internetanwendung](using-page-inspector-in-aspnet-mvc/_static/image4.png)

<span data-ttu-id="d0370-138">Öffnet die Anwendung in **Quelle** anzeigen.</span><span class="sxs-lookup"><span data-stu-id="d0370-138">The application opens in **Source** view.</span></span>

![Neue ASP.NET MVC-Anwendung in der Quellansicht](using-page-inspector-in-aspnet-mvc/_static/image6.png)

<span data-ttu-id="d0370-140">Nun, da Sie eine Anwendung gearbeitet haben, können Sie der Seitenprüfung verwenden, überprüfen und ändern Sie sie.</span><span class="sxs-lookup"><span data-stu-id="d0370-140">Now that you have an application to work with, you can use Page Inspector to examine and modify it.</span></span>

<a id="_starting_page_inspector"></a><a id="_3_using_page"></a>

## <a name="use-page-inspector-to-browse-to-a-view"></a><span data-ttu-id="d0370-141">Verwenden der Seitenprüfung, navigieren Sie zu einer Ansicht</span><span class="sxs-lookup"><span data-stu-id="d0370-141">Use Page Inspector to Browse to a View</span></span>

<span data-ttu-id="d0370-142">In Visual Studio 2012, Sie können mit der rechten Maustaste einer beliebigen Ansicht in das Projekt, wählen **in Seitenprüfung anzeigen**, Page Inspector wird ermitteln, die Route und die Seite angezeigt.</span><span class="sxs-lookup"><span data-stu-id="d0370-142">In Visual Studio 2012, you can right-click any view in your project, select **View in Page Inspector**, and Page Inspector will figure out the route and display the page.</span></span>

<span data-ttu-id="d0370-143">In **Projektmappen-Explorer**, erweitern Sie die **Ansichten** Ordner und klicken Sie dann die **Startseite** Ordner.</span><span class="sxs-lookup"><span data-stu-id="d0370-143">In **Solution Explorer**, expand the **Views** folder and then the **Home** folder.</span></span> <span data-ttu-id="d0370-144">Klicken Sie mit der rechten Maustaste auf die Datei "Index.cshtml", und wählen Sie **in Seitenprüfung anzeigen**.</span><span class="sxs-lookup"><span data-stu-id="d0370-144">Right click the Index.cshtml file and choose **View in Page Inspector**.</span></span>

![Ansicht "Index.cshtml" in der Seitenprüfung](using-page-inspector-in-aspnet-mvc/_static/image8.png)

<span data-ttu-id="d0370-146">Standardmäßig wird der Seitenprüfung auf der linken Seite der Visual Studio-Umgebung als Fenster angedockt.</span><span class="sxs-lookup"><span data-stu-id="d0370-146">By default, Page Inspector is docked as a window on the left side of the Visual Studio environment.</span></span> <span data-ttu-id="d0370-147">Falls gewünscht, können Sie an anderer Stelle andocken, oder lösen das Fenster.</span><span class="sxs-lookup"><span data-stu-id="d0370-147">If you prefer, you can dock it elsewhere, or undock the window.</span></span> <span data-ttu-id="d0370-148">Weitere Informationen finden Sie unter [How to: Arrange and Dock Windows (Vorgehensweise: Anordnen und Andocken von Fenstern)](https://msdn.microsoft.com/library/z4y0hsax.aspx).</span><span class="sxs-lookup"><span data-stu-id="d0370-148">See [How to: Arrange and Dock Windows](https://msdn.microsoft.com/library/z4y0hsax.aspx).</span></span>

<span data-ttu-id="d0370-149">Im obere Bereich des Fensters die Seitenprüfung zeigt die aktuelle Seite in einem Browserfenster angezeigt.</span><span class="sxs-lookup"><span data-stu-id="d0370-149">The top pane of the Page Inspector window shows the current page in a browser window.</span></span> <span data-ttu-id="d0370-150">Im unteren Bereich zeigt die Seite im HTML-Markup verwendet werden, sowie einige Registerkarten, mit die Sie verschiedene Aspekte der Seite untersuchen können.</span><span class="sxs-lookup"><span data-stu-id="d0370-150">The bottom pane shows the page in HTML markup, along with some tabs that let you inspect different aspects of the page.</span></span> <span data-ttu-id="d0370-151">Der untere Bereich ist ähnlich wie die [F12-Entwicklertools](https://msdn.microsoft.com/ie/aa740478) in Internet Explorer.</span><span class="sxs-lookup"><span data-stu-id="d0370-151">The bottom pane is similar to the [F12 Developer Tools](https://msdn.microsoft.com/ie/aa740478) in Internet Explorer.</span></span>

![ASP.NET MVC-Anwendung in der Seitenprüfung](using-page-inspector-in-aspnet-mvc/_static/image10.png)

<span data-ttu-id="d0370-153">In diesem Tutorial verwenden Sie die **HTML** und **Stile** Registerkarten, navigieren schnell, und nehmen Sie Änderungen an der Anwendung.</span><span class="sxs-lookup"><span data-stu-id="d0370-153">In this tutorial, you will use the **HTML** and **Styles** tabs to navigate quickly and make changes to the application.</span></span>

<a id="_examining_(&quot;decomposing&quot;)_the"></a><a id="_inspection_mode_and"></a><a id="_4_inspection_mode"></a>

## <a name="enableinspection-mode"></a><span data-ttu-id="d0370-154">EnableInspection-Modus</span><span class="sxs-lookup"><span data-stu-id="d0370-154">EnableInspection Mode</span></span>

<span data-ttu-id="d0370-155">Um die Seitenprüfung in den Überprüfungsmodus zu versetzen, klicken Sie auf die **prüfen** Schaltfläche.</span><span class="sxs-lookup"><span data-stu-id="d0370-155">To put Page Inspector into Inspection Mode, click the **Inspect** button.</span></span> <span data-ttu-id="d0370-156">Bei aktiviertem Überprüfungsmodus im Wenn Sie den Mauszeiger über einen beliebigen Teil der gerenderten Seite halten wird das entsprechende Quellmarkup oder der Code hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="d0370-156">In Inspection Mode, when you hold the mouse pointer over any part of the rendered page, the corresponding source markup or code is highlighted.</span></span>

![Überprüfungsmodus umschalten](using-page-inspector-in-aspnet-mvc/_static/image12.png)

<span data-ttu-id="d0370-158">Jetzt können bewegen Sie die Maus über verschiedene Teile der Seite innerhalb der Seitenprüfung.</span><span class="sxs-lookup"><span data-stu-id="d0370-158">Now move your mouse over different parts of the page within Page Inspector.</span></span> <span data-ttu-id="d0370-159">Wie Sie dies tun, wird der Mauszeiger ändert sich zu einem großen Pluszeichen, und untergeordnete Element wird hervorgehoben:</span><span class="sxs-lookup"><span data-stu-id="d0370-159">As you do, the mouse pointer changes to a large plus sign, and the element underneath is highlighted:</span></span>

![Mit dem Mauszeiger auf div.content-Wrappers](using-page-inspector-in-aspnet-mvc/_static/image14.png)

<span data-ttu-id="d0370-161">Wenn Sie den Mauszeiger bewegen, werden Visual Studio die entsprechende Razor-Syntax in der Quelldatei hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="d0370-161">As you move the mouse pointer, Visual Studio highlights the corresponding Razor syntax in the source file.</span></span> <span data-ttu-id="d0370-162">Wenn das HTML-Element aus einer anderen Quelldatei stammt, wird Visual Studio automatisch die Datei geöffnet.</span><span class="sxs-lookup"><span data-stu-id="d0370-162">If the HTML element comes from another source file, Visual Studio automatically opens the file.</span></span>

<span data-ttu-id="d0370-163">In der Seitenprüfung die **HTML** Registerkarte zeigt den HTML-Code, der von der Razor-Syntax generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="d0370-163">In Page Inspector, the **HTML** tab shows the HTML that was generated from the Razor syntax.</span></span> <span data-ttu-id="d0370-164">Wenn Sie den Mauszeiger bewegen, werden die HTML-Elemente hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="d0370-164">As you move the mouse pointer, the HTML elements are highlighted.</span></span> <span data-ttu-id="d0370-165">Die **Stile** Registerkarte zeigt die CSS-Regeln für das Element.</span><span class="sxs-lookup"><span data-stu-id="d0370-165">The **Styles** tab shows the CSS rules for the element.</span></span>

<a id="_5_using_page"></a>

## <a name="use-page-inspector-to-make-changes-to-markup"></a><span data-ttu-id="d0370-166">Verwenden der Seitenprüfung Markup ändern</span><span class="sxs-lookup"><span data-stu-id="d0370-166">Use Page Inspector to Make Changes to Markup</span></span>

<span data-ttu-id="d0370-167">Der Seitenprüfung können Sie Markup finden, deren Speicherort möglicherweise nicht offensichtlich.</span><span class="sxs-lookup"><span data-stu-id="d0370-167">Page Inspector lets you find markup whose location might not be obvious.</span></span> <span data-ttu-id="d0370-168">Sie können dann ändern Sie das Markup und finden Sie die Änderungen.</span><span class="sxs-lookup"><span data-stu-id="d0370-168">Then you can modify the markup and see the resulting changes.</span></span>

<span data-ttu-id="d0370-169">Um dies zu sehen, klicken Sie auf **prüfen** und führen Sie einen Bildlauf zum unteren Rand der Seite im Fenster der Seitenprüfung.</span><span class="sxs-lookup"><span data-stu-id="d0370-169">To see this, click **Inspect** and then scroll to the bottom of the page in the Page Inspector window.</span></span>

<span data-ttu-id="d0370-170">Wenn Sie den Mauszeiger in den Footerbereich verschieben, wird die Seitenprüfung öffnet die \_Layout.cshtml-Datei und hebt Sie im Abschnitt der Layoutseite, die Sie ausgewählt haben.</span><span class="sxs-lookup"><span data-stu-id="d0370-170">When you move the mouse pointer into the footer area, Page Inspector opens the \_Layout.cshtml file and highlights the section of the layout page that you have selected.</span></span> <span data-ttu-id="d0370-171">Wie Sie sehen können, sind die Fußzeile in die Layoutdatei und nicht die Ansicht selbst definiert ist.</span><span class="sxs-lookup"><span data-stu-id="d0370-171">As you can see, the footer are is defined in the layout file, and not the view itself.</span></span>

![Fußzeile](using-page-inspector-in-aspnet-mvc/_static/image16.png)

<span data-ttu-id="d0370-173">Verschieben Sie den Mauszeiger jetzt auf die Zeile mit dem Copyright <a id="a"> </a>beachten.</span><span class="sxs-lookup"><span data-stu-id="d0370-173">Now move your mouse pointer over the line with the copyright <a id="a"></a>notice.</span></span> <span data-ttu-id="d0370-174">In der \_Layout.cshtml-Seite, die entsprechende Zeile wird hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="d0370-174">In the \_Layout.cshtml page, the corresponding line is highlighted.</span></span>

![Hervorgehobene Zeile der Fußzeile-copyright](using-page-inspector-in-aspnet-mvc/_static/image18.png)

<span data-ttu-id="d0370-176">Hinzufügen von Text an das Ende der Zeile in der \_Layout.cshtml-Datei.</span><span class="sxs-lookup"><span data-stu-id="d0370-176">Add some text to the end of the line in the \_Layout.cshtml file.</span></span>

<span data-ttu-id="d0370-177">&lt;p&gt;&amp;kopieren; @DateTime.Now.Year -Meine ASP.NET MVC-Anwendung um eine gelungene!  &lt; /p&gt;</span><span class="sxs-lookup"><span data-stu-id="d0370-177">&lt;p&gt;&amp;copy; @DateTime.Now.Year - My ASP.NET MVC Application Rocks!&lt;/p&gt;</span></span>

<span data-ttu-id="d0370-178">Jetzt drücken Sie Strg + Alt + Eingabe, oder klicken Sie auf die Update-Leiste zum Anzeigen der Ergebnisse im Fenster Browser der Seitenprüfung.</span><span class="sxs-lookup"><span data-stu-id="d0370-178">Now, press Ctrl+Alt+Enter or click the Update Bar to see the results in the Page Inspector browser window.</span></span>

![Meine ASP.NET Application Rocks!](using-page-inspector-in-aspnet-mvc/_static/image20.png)

<span data-ttu-id="d0370-180">Sie können daran gedacht, dass die Fußzeile in die Datei "Index.cshtml" definiert, aber es stellte sich heraus in der \_Layout.cshtml, und die Seitenprüfung, die sie für Sie gefunden.</span><span class="sxs-lookup"><span data-stu-id="d0370-180">You might have thought that the footer defined in Index.cshtml, but it turned out to be in the \_Layout.cshtml, and Page Inspector found it for you.</span></span>

<a id="_inspection_mode_and_1"></a><a id="_6_inspection_mode"></a>

## <a name="inspection-mode-and-the-html-window"></a><span data-ttu-id="d0370-181">Überprüfungsmodus und HTML-Fenster</span><span class="sxs-lookup"><span data-stu-id="d0370-181">Inspection Mode and the HTML Window</span></span>

<span data-ttu-id="d0370-182">Als Nächstes müssen Sie einen kurzen Blick auf die HTML-Fenster, und wie sie Elemente für Sie zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="d0370-182">Next, you will have a quick look at the HTML window and how it maps elements for you.</span></span>

<span data-ttu-id="d0370-183">Klicken Sie auf **prüfen** der Seitenprüfung im Überprüfungsmodus zu platzieren.</span><span class="sxs-lookup"><span data-stu-id="d0370-183">Click **Inspect** to put Page Inspector in Inspection Mode.</span></span>

<span data-ttu-id="d0370-184">Klicken Sie auf den oberen Teil der Seite die Fehlermeldung "Ihre Logohere".</span><span class="sxs-lookup"><span data-stu-id="d0370-184">Click the top part of the page that says "Your logohere".</span></span> <span data-ttu-id="d0370-185">Untersuchen Sie ein bestimmtes Element im Detail, damit die Anzeige im Browserfenster nicht mehr geändert werden, wie Sie den Mauszeiger bewegen.</span><span class="sxs-lookup"><span data-stu-id="d0370-185">You are examining a particular element in more detail, so the display in the browser window no longer changes as you move the mouse pointer.</span></span>

<span data-ttu-id="d0370-186">Nun verschieben Sie den Mauszeiger an die **HTML** Fenster.</span><span class="sxs-lookup"><span data-stu-id="d0370-186">Now move the mouse pointer to the **HTML** window.</span></span> <span data-ttu-id="d0370-187">Während Sie den Mauszeiger bewegen, wird der Seitenprüfung beschrieben, das Element innerhalb der **HTML** Fenster und das entsprechende Element im Browserfenster hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="d0370-187">As you move the mouse pointer, Page Inspector outlines the element within the **HTML** window and highlights the corresponding element in the browser window.</span></span>

![HTML-Fenster](using-page-inspector-in-aspnet-mvc/_static/image22.png)

<span data-ttu-id="d0370-189">Wie zuvor die Seitenprüfung öffnet die \_Layout.cshtml-Datei für Sie in eine temporäre Registerkarte. Klicken Sie auf die \_Layout.cshtml temporäre Registerkarte, und das entsprechende Markup markiert die &lt;Header&gt; Abschnitt für Sie:</span><span class="sxs-lookup"><span data-stu-id="d0370-189">As before, Page Inspector opens the \_Layout.cshtml file for you in a temporary tab. Click the \_Layout.cshtml temporary tab, and the corresponding markup will be highlighted in the &lt;header&gt; section for you:</span></span>

![Hervorgehobene markup](using-page-inspector-in-aspnet-mvc/_static/image24.png)

<a id="_using_page_inspector"></a><a id="_7_previewing_css"></a>

## <a name="preview-css-changes-in-the-styles-window"></a><span data-ttu-id="d0370-191">Vorschau der CSS-Änderungen im Fenster Stile</span><span class="sxs-lookup"><span data-stu-id="d0370-191">Preview CSS Changes in the Styles Window</span></span>

<span data-ttu-id="d0370-192">Als Nächstes verwenden Sie die Seitenprüfung **Stile** Fenster Vorschau der Änderungen an CSS.</span><span class="sxs-lookup"><span data-stu-id="d0370-192">Next, you will use the Page Inspector **Styles** window to preview changes to CSS.</span></span>

<span data-ttu-id="d0370-193">Klicken Sie auf **prüfen** der Seitenprüfung im Überprüfungsmodus zu platzieren.</span><span class="sxs-lookup"><span data-stu-id="d0370-193">Click **Inspect** to put Page Inspector in Inspection Mode.</span></span>

<span data-ttu-id="d0370-194">Verschieben Sie im Fenster Browser der Seitenprüfung den Mauszeiger über den Abschnitt "Startseite", bis die **div.content-Wrappers** Bezeichnung angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="d0370-194">In the Page Inspector browser window, move the mouse pointer over the "Home Page" section until the **div.content-wrapper** label appears.</span></span>

![Mit dem Mauszeiger auf div.content-Wrappers](using-page-inspector-in-aspnet-mvc/_static/image26.png)

<span data-ttu-id="d0370-196">Klicken Sie im Abschnitt div.content-Wrappers einmal, und verschieben Sie den Mauszeiger an die **Stile** Fenster.</span><span class="sxs-lookup"><span data-stu-id="d0370-196">Click within the div.content-wrapper section once, and then move the mouse pointer to the **Styles** window.</span></span> <span data-ttu-id="d0370-197">Die **Stile** Fenster zeigt alle CSS-Regeln für dieses Element.</span><span class="sxs-lookup"><span data-stu-id="d0370-197">The **Styles** window shows all of the CSS rules for this element.</span></span> <span data-ttu-id="d0370-198">Scrollen Sie zur Klassenselektor Suchen der .featured .content-Wrapper.</span><span class="sxs-lookup"><span data-stu-id="d0370-198">Scroll down to find the .featured .content-wrapper class selector.</span></span> <span data-ttu-id="d0370-199">Deaktivieren Sie nun das Kontrollkästchen für die Background-Color-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="d0370-199">Now clear the checkbox for the background-color property.</span></span>

![Clear-Hintergrundfarbe](using-page-inspector-in-aspnet-mvc/_static/image28.png)

<span data-ttu-id="d0370-201">Beachten Sie, wie die Änderung sofort im Browser der Seitenprüfung-Fenster zeigt in der Vorschau.</span><span class="sxs-lookup"><span data-stu-id="d0370-201">Notice how the change previews instantly in the Page Inspector browser window.</span></span>

<span data-ttu-id="d0370-202">Wählen Sie das Kontrollkästchen erneut aus, doppelklicken Sie auf den Wert der Eigenschaft, und ändern Sie es sich in Rot.</span><span class="sxs-lookup"><span data-stu-id="d0370-202">Select the checkbox again, then double-click the property value and change it to red.</span></span> <span data-ttu-id="d0370-203">Die Änderung wird sofort aus:</span><span class="sxs-lookup"><span data-stu-id="d0370-203">The change shows immediately:</span></span>

![Rote Hintergrundfarbe](using-page-inspector-in-aspnet-mvc/_static/image30.png)

<span data-ttu-id="d0370-205">Die **Stile** Fenster macht es einfach, test und Vorschau CSS Änderungen erforderlich, bevor die Änderungen zu, um den Stil übernehmen Stylesheet selbst.</span><span class="sxs-lookup"><span data-stu-id="d0370-205">The **Styles** window makes it easy to test and preview CSS changes before you commit the changes to the style sheet itself.</span></span>

<a id="css_auto_sync"></a>
## <a name="css-auto-sync"></a><span data-ttu-id="d0370-206">Automatische CSS-Synchronisierung</span><span class="sxs-lookup"><span data-stu-id="d0370-206">CSS Auto Sync</span></span>

> [!NOTE]
> <span data-ttu-id="d0370-207">Dieses Feature erfordert Version 1.3 der Seitenprüfung.</span><span class="sxs-lookup"><span data-stu-id="d0370-207">This feature requires version 1.3 of Page Inspector.</span></span>


<span data-ttu-id="d0370-208">Das Feature für die automatische CSS-Synchronisierung können Sie eine CSS-Datei direkt bearbeiten und die Änderungen sofort im Browser Seitenprüfung.</span><span class="sxs-lookup"><span data-stu-id="d0370-208">The CSS Auto-Sync feature allows you to edit a CSS file directly, and see the changes immediately in the Page Inspector browser.</span></span>

<span data-ttu-id="d0370-209">Klicken Sie auf **prüfen** der Seitenprüfung im Überprüfungsmodus zu platzieren.</span><span class="sxs-lookup"><span data-stu-id="d0370-209">Click **Inspect** to put Page Inspector in Inspection Mode.</span></span>

<span data-ttu-id="d0370-210">Im Browser der Seitenprüfung verschieben Sie den Mauszeiger über den Abschnitt "Startseite", bis die **div.content-Wrappers** Bezeichnung angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="d0370-210">In the Page Inspector browser, move the mouse pointer over the "Home Page" section until the **div.content-wrapper** label appears.</span></span> <span data-ttu-id="d0370-211">Klicken Sie auf einmal, um dieses Element auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="d0370-211">Click once to select this element.</span></span>

<span data-ttu-id="d0370-212">Die **Stile** Fenster zeigt alle CSS-Regeln für dieses Element.</span><span class="sxs-lookup"><span data-stu-id="d0370-212">The **Styles** window shows all of the CSS rules for this element.</span></span> <span data-ttu-id="d0370-213">Scrollen Sie zur Klassenselektor Suchen der .featured .content-Wrapper.</span><span class="sxs-lookup"><span data-stu-id="d0370-213">Scroll down to find the .featured .content-wrapper class selector.</span></span> <span data-ttu-id="d0370-214">Klicken Sie auf ".featured .content-Wrapper".</span><span class="sxs-lookup"><span data-stu-id="d0370-214">Click on ".featured .content-wrapper".</span></span> <span data-ttu-id="d0370-215">Seitenprüfung öffnet die CSS-Datei, die dieses Format ("Site.CSS") definiert und hebt den entsprechenden CSS-Stil.</span><span class="sxs-lookup"><span data-stu-id="d0370-215">Page Inspector opens the CSS file that defines this style (Site.css) and highlights the corresponding CSS style.</span></span>

![](using-page-inspector-in-aspnet-mvc/_static/image32.png)

<span data-ttu-id="d0370-216">Ändern Sie nun den Wert für `background-color` auf "Rot".</span><span class="sxs-lookup"><span data-stu-id="d0370-216">Now change the value for `background-color` to "red".</span></span> <span data-ttu-id="d0370-217">Die Änderung wird sofort im Browser Seitenprüfung angezeigt.</span><span class="sxs-lookup"><span data-stu-id="d0370-217">The change appears immediately in the Page Inspector browser.</span></span>

![](using-page-inspector-in-aspnet-mvc/_static/image34.png)

<a id="css_color_picker"></a>
## <a name="using-the-css-color-picker"></a><span data-ttu-id="d0370-218">Mithilfe der CSS-Farbauswahl</span><span class="sxs-lookup"><span data-stu-id="d0370-218">Using the CSS Color Picker</span></span>

<span data-ttu-id="d0370-219">CSS-Editor in Visual Studio 2012 verfügt über eine Farbauswahl angezeigt, die ganz einfach auswählen, und fügen Sie Farben.</span><span class="sxs-lookup"><span data-stu-id="d0370-219">The CSS editor in Visual Studio 2012 has a color picker that makes it easy to choose and insert colors.</span></span> <span data-ttu-id="d0370-220">Die Farbauswahl enthält eine Standardpalette mit Farben, unterstützt standardfarbnamen, Hashcodes, RGB-, RGBA-, HSL- und HSLA-Farben und verwaltet eine Liste der Farben, die Sie zuletzt in das Dokument verwendet haben.</span><span class="sxs-lookup"><span data-stu-id="d0370-220">The color picker includes a standard palette of colors, supports standard color names, hash codes, RGB, RGBA, HSL, and HSLA colors, and maintains a list of the colors you've used most recently in the document.</span></span>

<span data-ttu-id="d0370-221">Im vorherigen Abschnitt haben Sie den Wert geändert der `background-color` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="d0370-221">In the previous section, you changed the value of the `background-color` property.</span></span> <span data-ttu-id="d0370-222">Um die Farbauswahl aufzurufen, platzieren Sie die Einfügemarke nach dem Eigenschaftennamen und den Typ **#** oder **Rgb (**.</span><span class="sxs-lookup"><span data-stu-id="d0370-222">To invoke the color picker, place the insertion point after the property name and type **#** or **rgb(**.</span></span>

![Die CSS-Auswahl Farbleiste](using-page-inspector-in-aspnet-mvc/_static/image36.png)

<span data-ttu-id="d0370-224">Klicken Sie auf eine Farbe auszuwählen, oder drücken die nach-unten-Taste, und klicken Sie dann verwenden Sie die linke und Rechte Pfeiltasten, um die Farben zu durchlaufen.</span><span class="sxs-lookup"><span data-stu-id="d0370-224">Click on a color to select it, or press the down arrow key and then use the left and right arrow keys to traverse the colors.</span></span> <span data-ttu-id="d0370-225">Wenn Sie eine Farbe besuchen, wird der entsprechende Hexadezimalwert in der Vorschau angezeigt:</span><span class="sxs-lookup"><span data-stu-id="d0370-225">When you visit a color, the corresponding hex value is previewed:</span></span>

![Background-Color-Wert der Eigenschaft, die in der Vorschau angezeigt](using-page-inspector-in-aspnet-mvc/_static/image38.png)

<span data-ttu-id="d0370-227">Wenn der Farbleiste nicht den gewünschten genaue Farbe haben, können Sie der Farbe Auswahl Pop aus.</span><span class="sxs-lookup"><span data-stu-id="d0370-227">If the color bar doesn't have the exact color you want, you can use the color picker pop-down.</span></span> <span data-ttu-id="d0370-228">Um es zu öffnen, klicken Sie auf die Chevron-Doppelpfeil am rechten Ende der Farbleiste, oder ein-oder zweimal drücken Sie den Pfeil nach unten auf der Tastatur.</span><span class="sxs-lookup"><span data-stu-id="d0370-228">To open it, click the double chevron at the right end of the color bar, or press the Down Arrow once or twice on the keyboard.</span></span>

![CSS-Farbe Auswahl Pop-ab](using-page-inspector-in-aspnet-mvc/_static/image40.png)

<span data-ttu-id="d0370-230">Klicken Sie auf eine Farbe aus der senkrechte Strich auf der rechten Seite.</span><span class="sxs-lookup"><span data-stu-id="d0370-230">Click a color from the vertical bar on the right.</span></span> <span data-ttu-id="d0370-231">Dies zeigt einen Farbverlauf für diese Farbe im Hauptfenster.</span><span class="sxs-lookup"><span data-stu-id="d0370-231">This shows a gradient for that color in the main window.</span></span> <span data-ttu-id="d0370-232">Wählen Sie eine Farbe direkt aus der senkrechte Strich durch Drücken der EINGABETASTE, oder klicken Sie auf einem beliebigen Zeitpunkt im Hauptfenster mit größerer Genauigkeit zu wählen.</span><span class="sxs-lookup"><span data-stu-id="d0370-232">Choose a color directly from the vertical bar by pressing Enter, or click any point in the main window to choose with greater precision.</span></span>

<span data-ttu-id="d0370-233">Wenn eine Farbe vorliegt, auf dem Bildschirm, die Sie verwenden möchten (es muss keine werden innerhalb der Visual Studio-Benutzeroberfläche), Sie können den Wert erfassen, indem Sie mit dem Tool Pipette in der unteren rechten Ecke.</span><span class="sxs-lookup"><span data-stu-id="d0370-233">If there is a color on your computer screen that you want to use (it doesn't have to be inside the Visual Studio user interface), you can capture its value by using the eyedropper tool on the lower right.</span></span>

<span data-ttu-id="d0370-234">Sie können auch die Deckkraft einer Farbe ändern, indem Sie den Schieberegler am unteren Rand der Farbauswahl.</span><span class="sxs-lookup"><span data-stu-id="d0370-234">You also can change the opacity of a color by moving the slider at the bottom of the color picker.</span></span> <span data-ttu-id="d0370-235">Auf diese Weise Änderungen Farbe, die Werte in RGBA-Werte, da das RGBA-Format Deckkraft darstellen kann.</span><span class="sxs-lookup"><span data-stu-id="d0370-235">Doing so changes color values to RGBA values, because the RGBA format can represent opacity.</span></span>

<span data-ttu-id="d0370-236">Nachdem Sie eine Farbe ausgewählt haben, drücken Sie die EINGABETASTE, und geben Sie ein Semikolon zum Abschließen des Background-Color-Eintrags in der *"Site.CSS"* Datei.</span><span class="sxs-lookup"><span data-stu-id="d0370-236">After you have chosen a color, press Enter, and then type a semicolon to complete the background-color entry in the *Site.css* file.</span></span>

<a id="_the_update_bar"></a>

### <a name="the-page-inspector-update-bar"></a><span data-ttu-id="d0370-237">Die Seite Inspector-Update-Leiste</span><span class="sxs-lookup"><span data-stu-id="d0370-237">The Page Inspector Update Bar</span></span>

<span data-ttu-id="d0370-238">Die Seitenprüfung sofort erkennt die Änderung an der *"Site.CSS"* Datei und zeigt eine Warnung in eine Update-Leiste.</span><span class="sxs-lookup"><span data-stu-id="d0370-238">Page Inspector immediately detects the change to the *Site.css* file and displays an alert in an update bar.</span></span>

![Update-Leiste](using-page-inspector-in-aspnet-mvc/_static/image42.png)

<span data-ttu-id="d0370-240">Speichern Sie alle Dateien und den Browser der Seitenprüfung zu aktualisieren, drücken Strg + Alt + Eingabe, oder klicken Sie auf die Update-Leiste.</span><span class="sxs-lookup"><span data-stu-id="d0370-240">To save all your files and refresh the Page Inspector browser, press Ctrl+Alt+Enter or click the update bar.</span></span> <span data-ttu-id="d0370-241">Die Änderung in die Hervorhebungsfarbe wird im Browser angezeigt.</span><span class="sxs-lookup"><span data-stu-id="d0370-241">The change in the highlight color appears in the browser.</span></span>

<a id="map_dynamic_elements"></a>
## <a name="mapping-dynamic-page-elements-to-javascript"></a><span data-ttu-id="d0370-242">Zuordnen von dynamische Seitenelemente zu JavaScript</span><span class="sxs-lookup"><span data-stu-id="d0370-242">Mapping Dynamic Page Elements to JavaScript</span></span>

<span data-ttu-id="d0370-243">Bei modernen Webanwendungen werden die Elemente auf der Seite häufig dynamisch mit JavaScript generiert.</span><span class="sxs-lookup"><span data-stu-id="d0370-243">In modern web applications, elements in the page are often generated dynamically with JavaScript.</span></span> <span data-ttu-id="d0370-244">Das bedeutet, dass es keine statischen Markup (HTML oder Razor), das die diese Elemente entspricht.</span><span class="sxs-lookup"><span data-stu-id="d0370-244">That means there is no static markup (either HTML or Razor) that corresponds to these page elements.</span></span>

<span data-ttu-id="d0370-245">Mit Version 1.3 können die Seitenprüfung Elemente zuordnen, die die Seite wieder an die entsprechenden JavaScript-Code dynamisch hinzugefügt wurden.</span><span class="sxs-lookup"><span data-stu-id="d0370-245">With version 1.3, Page Inspector can now map items that were dynamically added to the page back to the corresponding JavaScript code.</span></span> <span data-ttu-id="d0370-246">Um dieses Feature zu demonstrieren, verwenden wir die [Single-Page-Anwendung (SPA) Vorlage](../../../single-page-application/overview/introduction/knockoutjs-template.md).</span><span class="sxs-lookup"><span data-stu-id="d0370-246">To demonstrate this feature, we'll use the [Single Page Application (SPA) template](../../../single-page-application/overview/introduction/knockoutjs-template.md).</span></span>

> [!NOTE]
> <span data-ttu-id="d0370-247">Die SPA-Vorlage erfordert die [ASP.NET- und Web Tools 2012.2](https://go.microsoft.com/fwlink/?LinkId=282650) aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="d0370-247">The SPA template requires the [ASP.NET and Web Tools 2012.2](https://go.microsoft.com/fwlink/?LinkId=282650) update.</span></span>


<span data-ttu-id="d0370-248">Wählen Sie in Visual Studio **Datei** &gt; **neues Projekt**.</span><span class="sxs-lookup"><span data-stu-id="d0370-248">In Visual Studio, choose **File** &gt; **New Project**.</span></span> <span data-ttu-id="d0370-249">Erweitern Sie auf der linken Seite **Visual C#-** Option **Web**, und wählen Sie dann **ASP.NET MVC 4-Webanwendung**.</span><span class="sxs-lookup"><span data-stu-id="d0370-249">On the left, expand **Visual C#**, select **Web**, and then select **ASP.NET MVC4 Web Application**.</span></span> <span data-ttu-id="d0370-250">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="d0370-250">Click **OK**.</span></span>

<span data-ttu-id="d0370-251">In der **neues ASP.NET MVC 4-Projekt** wählen Sie im Dialogfeld **Single Page Application**.</span><span class="sxs-lookup"><span data-stu-id="d0370-251">In the **New ASP.NET MVC 4 Project** dialog, select **Single Page Application**.</span></span>

<span data-ttu-id="d0370-252">Erweitern Sie im Projektmappen-Explorer die **Ansichten** Ordner und klicken Sie dann die **Startseite** Ordner.</span><span class="sxs-lookup"><span data-stu-id="d0370-252">In Solution Explorer, expand the **Views** folder and then the **Home** folder.</span></span> <span data-ttu-id="d0370-253">Klicken Sie mit der rechten Maustaste auf die Datei "Index.cshtml", und wählen Sie **in Seitenprüfung anzeigen**.</span><span class="sxs-lookup"><span data-stu-id="d0370-253">Right click the Index.cshtml file and choose **View in Page Inspector**.</span></span>

<span data-ttu-id="d0370-254">Das erste angezeigte im Browser Seitenprüfung ist eine Anmeldeseite.</span><span class="sxs-lookup"><span data-stu-id="d0370-254">The first thing that is displayed in the Page Inspector browser is a login page.</span></span> <span data-ttu-id="d0370-255">Klicken Sie auf "Registrieren", und erstellen Sie einen Benutzernamen und Kennwort.</span><span class="sxs-lookup"><span data-stu-id="d0370-255">Click "Sign Up" and create a user name and password.</span></span> <span data-ttu-id="d0370-256">Nachdem Sie haben registriert, wird die Anwendung meldet an und erstellt eine to-Do-Liste mit einige Beispiel-Elemente.</span><span class="sxs-lookup"><span data-stu-id="d0370-256">Once you sign up, the application logs you in and creates a to-do list with some sample items.</span></span>

<span data-ttu-id="d0370-257">Klicken Sie auf **prüfen** der Seitenprüfung im Überprüfungsmodus zu platzieren.</span><span class="sxs-lookup"><span data-stu-id="d0370-257">Click **Inspect** to put Page Inspector in Inspection Mode.</span></span> <span data-ttu-id="d0370-258">Die Browser der Seitenprüfung klicken Sie auf eines der to-do-Elemente.</span><span class="sxs-lookup"><span data-stu-id="d0370-258">In the Page Inspector browser, click on one of the to-do items.</span></span> <span data-ttu-id="d0370-259">Beachten Sie, dass das Element anstelle von blau hervorgehoben wird, neben dem Elementnamen in orange dargestellt, die mit "JS" hervorgehoben ist.</span><span class="sxs-lookup"><span data-stu-id="d0370-259">Notice that instead of being highlighted in blue, the element is highlighted in orange, with "JS" next to the element name.</span></span> <span data-ttu-id="d0370-260">Dies gibt an, dass das Element dynamisch über ein Skript erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="d0370-260">This indicates that the element was created dynamically through script.</span></span>

![](using-page-inspector-in-aspnet-mvc/_static/image44.png)

<span data-ttu-id="d0370-261">Darüber hinaus eine orangefarbene Unterstreichung wird angezeigt, auf die **Aufrufliste** Registerkarte. Dies bedeutet, dass die **Aufrufliste** Bereich enthält weitere Informationen über das Element.</span><span class="sxs-lookup"><span data-stu-id="d0370-261">In addition, an orange underline appears on the **Call Stack** tab. This indicates that the **Call Stack** pane has more information about the element.</span></span>

<span data-ttu-id="d0370-262">Klicken Sie auf die **Aufrufliste** Registerkarte. Die **Aufrufliste** Bereich zeigt die Aufrufliste für den JavaScript-Aufruf, der das Element erstellt.</span><span class="sxs-lookup"><span data-stu-id="d0370-262">Click on the **Call Stack** tab. The **Call Stack** pane shows the call stack for the JavaScript call that created the element.</span></span> <span data-ttu-id="d0370-263">Aufrufe von externen Bibliotheken wie jQuery reduziert, damit Sie die Aufrufe an das Anwendungsskript leicht erkennen können.</span><span class="sxs-lookup"><span data-stu-id="d0370-263">Calls to external libraries such as jQuery are collapsed, so that you can easily see the calls to your application script.</span></span>

![](using-page-inspector-in-aspnet-mvc/_static/image46.png)

<span data-ttu-id="d0370-264">Um den vollständigen Stapel, einschließlich Aufrufe von externen Bibliotheken finden Sie unter können Sie die Knoten mit der Bezeichnung "Externe Bibliotheken" erweitern:</span><span class="sxs-lookup"><span data-stu-id="d0370-264">To see the full stack, including calls to external libraries, you can expand the nodes labeled "External Libraries":</span></span>

![](using-page-inspector-in-aspnet-mvc/_static/image48.png)

<span data-ttu-id="d0370-265">Wenn Sie ein Element in der Aufrufliste klicken, wird Visual Studio öffnet die Codedatei, und das entsprechende Skript hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="d0370-265">If you click an item in the call stack, Visual Studio opens the code file and highlights the corresponding script.</span></span>

![](using-page-inspector-in-aspnet-mvc/_static/image50.png)

## <a name="see-also"></a><span data-ttu-id="d0370-266">Siehe auch</span><span class="sxs-lookup"><span data-stu-id="d0370-266">See Also</span></span>

<span data-ttu-id="d0370-267">[Einführung in ASP.NET MVC 4 mit Visual Studio](../older-versions/getting-started-with-aspnet-mvc4/intro-to-aspnet-mvc-4.md) (ASP.NET-Website)</span><span class="sxs-lookup"><span data-stu-id="d0370-267">[Intro to ASP.NET MVC 4 with Visual Studio](../older-versions/getting-started-with-aspnet-mvc4/intro-to-aspnet-mvc-4.md) (ASP.net website)</span></span>

<span data-ttu-id="d0370-268">[Einführung in der Seitenprüfung](https://channel9.msdn.com/posts/visual-studio-vnext-introducing-page-inspector/) (Channel 9-Video)</span><span class="sxs-lookup"><span data-stu-id="d0370-268">[Introducing Page Inspector](https://channel9.msdn.com/posts/visual-studio-vnext-introducing-page-inspector/) (Channel 9 video)</span></span>

<span data-ttu-id="d0370-269">[Fehlermeldungen der Seitenprüfung](https://go.microsoft.com/?linkid=9813062) (MSDN)</span><span class="sxs-lookup"><span data-stu-id="d0370-269">[Page Inspector Error Messages](https://go.microsoft.com/?linkid=9813062) (MSDN)</span></span>
