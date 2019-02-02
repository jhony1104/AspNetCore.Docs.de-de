---
uid: aspnet/overview/owin-and-katana/owin-startup-class-detection
title: OWIN-Startup-Klasse Erkennung | Microsoft-Dokumentation
author: Praburaj
description: Dieses Tutorial veranschaulicht das Konfigurieren der OWIN-Startup-Klasse geladen wird. Weitere Informationen zu OWIN finden Sie eine Übersicht des Katana-Projekt. In diesem Tutorial wurde...
ms.author: riande
ms.date: 01/28/2019
ms.assetid: 08257f55-36f4-4e39-9c88-2a5602838c79
msc.legacyurl: /aspnet/overview/owin-and-katana/owin-startup-class-detection
msc.type: authoredcontent
ms.openlocfilehash: 0b34cca8b48383dbb028106651758dff889ed614
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667296"
---
<a name="owin-startup-class-detection"></a><span data-ttu-id="8fa99-105">Erkennung der OWIN-Startup-Klasse</span><span class="sxs-lookup"><span data-stu-id="8fa99-105">OWIN Startup Class Detection</span></span>
====================

> <span data-ttu-id="8fa99-106">Dieses Tutorial veranschaulicht das Konfigurieren der OWIN-Startup-Klasse geladen wird.</span><span class="sxs-lookup"><span data-stu-id="8fa99-106">This tutorial shows how to configure which OWIN startup class is loaded.</span></span> <span data-ttu-id="8fa99-107">Weitere Informationen zu OWIN, finden Sie unter [eine Übersicht des Katana-Projekt](an-overview-of-project-katana.md).</span><span class="sxs-lookup"><span data-stu-id="8fa99-107">For more information on OWIN, see [An Overview of Project Katana](an-overview-of-project-katana.md).</span></span> <span data-ttu-id="8fa99-108">In diesem Tutorial wurde von Rick Anderson geschrieben ( [ @RickAndMSFT ](https://twitter.com/#!/RickAndMSFT) ), Praburaj Thiagarajan und Howard Dierking ( [ @howard \_Dierking](https://twitter.com/howard_dierking) ).</span><span class="sxs-lookup"><span data-stu-id="8fa99-108">This tutorial was written by Rick Anderson ( [@RickAndMSFT](https://twitter.com/#!/RickAndMSFT) ), Praburaj Thiagarajan, and Howard Dierking ( [@howard\_dierking](https://twitter.com/howard_dierking) ).</span></span>
>
> ## <a name="prerequisites"></a><span data-ttu-id="8fa99-109">Vorraussetzungen</span><span class="sxs-lookup"><span data-stu-id="8fa99-109">Prerequisites</span></span>
>
> [<span data-ttu-id="8fa99-110">Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="8fa99-110">Visual Studio 2017</span></span>](https://visualstudio.microsoft.com/downloads/)


## <a name="owin-startup-class-detection"></a><span data-ttu-id="8fa99-111">Erkennung der OWIN-Startup-Klasse</span><span class="sxs-lookup"><span data-stu-id="8fa99-111">OWIN Startup Class Detection</span></span>

 <span data-ttu-id="8fa99-112">Jede OWIN-Anwendung verfügt über ein Startup-Klasse, in dem Sie Komponenten für die Pipeline der Anwendung angeben.</span><span class="sxs-lookup"><span data-stu-id="8fa99-112">Every OWIN Application has a startup class where you specify components for the application pipeline.</span></span> <span data-ttu-id="8fa99-113">Es gibt verschiedene Möglichkeiten, die Sie Ihrer Startup-Klasse mit der Runtime eine Verbindung herstellen können, die Sie abhängig von der hosting-Modell auswählen (OwinHost, IIS und IIS Express).</span><span class="sxs-lookup"><span data-stu-id="8fa99-113">There are different ways you can connect your startup class with the runtime, depending on the hosting model you choose (OwinHost, IIS, and IIS-Express).</span></span> <span data-ttu-id="8fa99-114">Die Startup-Klasse, die in diesem Tutorial gezeigt, kann in jede hostanwendung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="8fa99-114">The startup class shown in this tutorial can be used in every hosting application.</span></span> <span data-ttu-id="8fa99-115">Sie verbinden die Startup-Klasse, mit der hosting-Laufzeit mit, die einen dieser Ansätze:</span><span class="sxs-lookup"><span data-stu-id="8fa99-115">You connect the startup class with the hosting runtime using one of the these approaches:</span></span>

1. <span data-ttu-id="8fa99-116">**Benennungskonvention**: Katana sucht eine Klasse namens `Startup` im Namespace, die Übereinstimmungen zwischen den Assemblynamen oder den globalen Namespace.</span><span class="sxs-lookup"><span data-stu-id="8fa99-116">**Naming Convention**: Katana looks for a class named `Startup` in namespace matching the assembly name or the global namespace.</span></span>
2. <span data-ttu-id="8fa99-117">**OwinStartup Attribut**: Dies ist der Ansatz die meisten Entwickler werden an die Startup-Klasse.</span><span class="sxs-lookup"><span data-stu-id="8fa99-117">**OwinStartup Attribute**: This is the approach most developers will take to specify the startup class.</span></span> <span data-ttu-id="8fa99-118">Das folgende Attribut legt als Startup-Klasse `TestStartup` fest und gibt als ihren Namespace `StartupDemo` an.</span><span class="sxs-lookup"><span data-stu-id="8fa99-118">The following attribute will set the startup class to the `TestStartup` class in the `StartupDemo` namespace.</span></span>

    [!code-csharp[Main](owin-startup-class-detection/samples/sample1.cs)]

   <span data-ttu-id="8fa99-119">Die `OwinStartup` -Attribut überschreibt die Benennungskonvention.</span><span class="sxs-lookup"><span data-stu-id="8fa99-119">The `OwinStartup` attribute overrides the naming convention.</span></span> <span data-ttu-id="8fa99-120">Sie können auch einen Anzeigenamen mit diesem Attribut angeben, jedoch mit einem geeigneten Namen müssen Sie auch die `appSetting` Element in der Konfigurationsdatei.</span><span class="sxs-lookup"><span data-stu-id="8fa99-120">You can also specify a friendly name with this attribute, however, using a friendly name requires you to also use the `appSetting` element in the configuration file.</span></span>
3. <span data-ttu-id="8fa99-121">**Der AppSetting-Element in der Konfigurationsdatei**: Die `appSetting` -Element überschreibt die `OwinStartup` -Attribut und Benennungskonvention.</span><span class="sxs-lookup"><span data-stu-id="8fa99-121">**The appSetting element in the Configuration file**: The `appSetting` element overrides the `OwinStartup` attribute and naming convention.</span></span> <span data-ttu-id="8fa99-122">Sie haben mehrere Klassen von Start (jede mit einer `OwinStartup` Attribut) und konfigurieren Sie die Startup-Klasse in einer mit-Markup ähnlich der folgenden Konfigurationsdatei geladen werden:</span><span class="sxs-lookup"><span data-stu-id="8fa99-122">You can have multiple startup classes (each using an `OwinStartup` attribute) and configure which startup class will be loaded in a configuration file using markup similar to the following:</span></span>

    [!code-xml[Main](owin-startup-class-detection/samples/sample2.xml)]

   <span data-ttu-id="8fa99-123">Der folgende Schlüssel, der explizit, die "Startup"-Klasse und die Assembly angibt kann auch verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="8fa99-123">The following key, which explicitly specifies the startup class and assembly can also be used:</span></span>

    [!code-xml[Main](owin-startup-class-detection/samples/sample3.xml)]

   <span data-ttu-id="8fa99-124">Die folgenden XML-Code in der Konfigurationsdatei gibt den Anzeigenamen Startup-Klassennamen `ProductionConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="8fa99-124">The following XML in the configuration file specifies a friendly startup class name of `ProductionConfiguration`.</span></span>

    [!code-xml[Main](owin-startup-class-detection/samples/sample4.xml)]

   <span data-ttu-id="8fa99-125">Das obenstehende Markup muss verwendet werden, durch den folgenden `OwinStartup` Attribut, das gibt einen Anzeigenamen und bewirkt, dass die `ProductionStartup2` Klasse ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="8fa99-125">The above markup must be used with the following `OwinStartup` attribute which specifies a friendly name and causes the `ProductionStartup2` class to run.</span></span>

    [!code-csharp[Main](owin-startup-class-detection/samples/sample5.cs?highlight=1,16)]
4. <span data-ttu-id="8fa99-126">So deaktivieren Sie OWIN-Startup-Ermittlung Hinzufügen der `appSetting owin:AutomaticAppStartup` mit einem Wert von `"false"` in der Datei "Web.config".</span><span class="sxs-lookup"><span data-stu-id="8fa99-126">To disable OWIN startup discovery add the `appSetting owin:AutomaticAppStartup` with a value of `"false"` in the web.config file.</span></span>

    [!code-xml[Main](owin-startup-class-detection/samples/sample6.xml)]

## <a name="create-an-aspnet-web-app-using-owin-startup"></a><span data-ttu-id="8fa99-127">Erstellen einer ASP.NET Web-App mithilfe von OWIN-Startup</span><span class="sxs-lookup"><span data-stu-id="8fa99-127">Create an ASP.NET Web App using OWIN Startup</span></span>

1. <span data-ttu-id="8fa99-128">Erstellen Sie eine leere ASP.NET-Webanwendung und nennen sie **StartupDemo**.</span><span class="sxs-lookup"><span data-stu-id="8fa99-128">Create an empty Asp.Net web application and name it **StartupDemo**.</span></span> <span data-ttu-id="8fa99-129">– Installieren `Microsoft.Owin.Host.SystemWeb` mithilfe des NuGet-Paket-Managers.</span><span class="sxs-lookup"><span data-stu-id="8fa99-129">- Install `Microsoft.Owin.Host.SystemWeb` using the NuGet package manager.</span></span> <span data-ttu-id="8fa99-130">Aus der **Tools** die Option **NuGet Paket-Manager**, und **Paket-Manager Konsole**.</span><span class="sxs-lookup"><span data-stu-id="8fa99-130">From the **Tools** menu, select **NuGet Package Manager**, and then **Package Manager Console**.</span></span> <span data-ttu-id="8fa99-131">Geben Sie folgenden Befehl ein:</span><span class="sxs-lookup"><span data-stu-id="8fa99-131">Enter the following command:</span></span>

    [!code-powershell[Main](owin-startup-class-detection/samples/sample7.ps1)]
2. <span data-ttu-id="8fa99-132">Hinzufügen einer OWIN-Startup-Klasse.</span><span class="sxs-lookup"><span data-stu-id="8fa99-132">Add an OWIN startup class.</span></span> <span data-ttu-id="8fa99-133">Klicken Sie in Visual Studio 2017 mit der rechten Maustaste in des Projekts, und wählen **Klasse hinzufügen**. – In der **neues Element hinzufügen** Dialogfeld Geben Sie *OWIN* in das Suchfeld ein, und ändern Sie den Namen "Startup.cs", und wählen Sie dann **hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="8fa99-133">In Visual Studio 2017 right-click the project and select **Add Class**.- In the **Add New Item** dialog box, enter *OWIN* in the search field, and change the name to Startup.cs, and then select **Add**.</span></span>

     ![](owin-startup-class-detection/_static/image1.png)

   <span data-ttu-id="8fa99-134">Das nächste Mal, die Sie hinzufügen möchten eine *Owin-Startklasse*, werden in zur Verfügung, aus der **hinzufügen** im Menü.</span><span class="sxs-lookup"><span data-stu-id="8fa99-134">The next time you want to add an *Owin Startup class*, it will be in available from the **Add** menu.</span></span>

     ![](owin-startup-class-detection/_static/image2.png)

   <span data-ttu-id="8fa99-135">Alternativ können Sie mit der rechten Maustaste in des Projekts und wählen Sie **hinzufügen**, und wählen Sie dann **neues Element**, und wählen Sie dann die **Owin-Startklasse**.</span><span class="sxs-lookup"><span data-stu-id="8fa99-135">Alternatively, you can right-click the project and select **Add**, then select **New Item**, and then select the **Owin Startup class**.</span></span>

     ![](owin-startup-class-detection/_static/image3.png)

- <span data-ttu-id="8fa99-136">Ersetzen Sie den generierten Code in die *"Startup.cs"* Datei durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="8fa99-136">Replace the generated code in the *Startup.cs* file with the following:</span></span>

    [!code-csharp[Main](owin-startup-class-detection/samples/sample8.cs?highlight=5,7,15-28,31-34)]

  <span data-ttu-id="8fa99-137">Die `app.Use` Lambda-Ausdruck wird verwendet, um die angegebenen middlewarekomponente, für die OWIN-Pipeline zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="8fa99-137">The `app.Use` lambda expression is used to register the specified middleware component to the OWIN pipeline.</span></span> <span data-ttu-id="8fa99-138">In diesem Fall wird die Protokollierung von eingehenden Anforderungen vor dem Antworten auf die eingehende Anforderung festgelegt.</span><span class="sxs-lookup"><span data-stu-id="8fa99-138">In this case we are setting up logging of incoming requests before responding to the incoming request.</span></span> <span data-ttu-id="8fa99-139">Die `next` Parameter ist der Delegat ( [Func](https://msdn.microsoft.com/library/bb534960(v=vs.100).aspx) &lt; [Aufgabe](https://msdn.microsoft.com/library/dd321424(v=vs.100).aspx) &gt; ) an die nächste Komponente in der Pipeline.</span><span class="sxs-lookup"><span data-stu-id="8fa99-139">The `next` parameter is the delegate ( [Func](https://msdn.microsoft.com/library/bb534960(v=vs.100).aspx) &lt; [Task](https://msdn.microsoft.com/library/dd321424(v=vs.100).aspx) &gt; ) to the next component in the pipeline.</span></span> <span data-ttu-id="8fa99-140">Die `app.Run` Lambda-Ausdruck wird die Pipeline, um eingehende Anforderungen verknüpft und stellt den Antwortmechanismus bereit.</span><span class="sxs-lookup"><span data-stu-id="8fa99-140">The `app.Run` lambda expression hooks up the pipeline to incoming requests and provides the response mechanism.</span></span>
     > [!NOTE]
     > <span data-ttu-id="8fa99-141">Im obigen Code haben wir eine auskommentiert ist die `OwinStartup` -Attribut, und wir haben die Konvention der Ausführung der Klasse, die mit dem Namen der vertrauenden Seite `Startup` . – drücken Sie ***F5*** zum Ausführen der Anwendung.</span><span class="sxs-lookup"><span data-stu-id="8fa99-141">In the code above we have commented out the `OwinStartup` attribute and we're relying on the convention of running the class named `Startup` .- Press ***F5*** to run the application.</span></span> <span data-ttu-id="8fa99-142">Drücken Sie mehrmals aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="8fa99-142">Hit refresh a few times.</span></span>

    <span data-ttu-id="8fa99-143">![](owin-startup-class-detection/_static/image4.png) Hinweis: Die Anzahl, die in den Abbildungen in diesem Tutorial gezeigt entspricht nicht die Anzahl der verfügbaren.</span><span class="sxs-lookup"><span data-stu-id="8fa99-143">![](owin-startup-class-detection/_static/image4.png) Note: The number shown in the images in this tutorial will not match the number you see.</span></span> <span data-ttu-id="8fa99-144">Die Millisekunden-Zeichenfolge wird verwendet, um eine neue Antwort anzuzeigen, wenn Sie die Seite aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="8fa99-144">The millisecond string is used to show a new response when you refresh the page.</span></span>
  <span data-ttu-id="8fa99-145">Sehen Sie die Ablaufverfolgungsinformationen in die **Ausgabe** Fenster.</span><span class="sxs-lookup"><span data-stu-id="8fa99-145">You can see the trace information in the **Output** window.</span></span>

    ![](owin-startup-class-detection/_static/image5.png)

## <a name="add-more-startup-classes"></a><span data-ttu-id="8fa99-146">Fügen Sie weitere Startup-Klassen</span><span class="sxs-lookup"><span data-stu-id="8fa99-146">Add More Startup Classes</span></span>

<span data-ttu-id="8fa99-147">In diesem Abschnitt fügen wir eine andere Startup-Klasse.</span><span class="sxs-lookup"><span data-stu-id="8fa99-147">In this section we'll add another Startup class.</span></span> <span data-ttu-id="8fa99-148">Sie können mehrere OWIN-Startklasse Ihrer Anwendung hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="8fa99-148">You can add multiple OWIN startup class to your application.</span></span> <span data-ttu-id="8fa99-149">Beispielsweise empfiehlt es sich beim Start-Klassen für die Entwicklung, Test und Produktion erstellen.</span><span class="sxs-lookup"><span data-stu-id="8fa99-149">For example, you might want to create startup classes for development, testing and production.</span></span>

1. <span data-ttu-id="8fa99-150">Erstellen Sie eine neue OWIN-Startup-Klasse, und nennen Sie sie `ProductionStartup`.</span><span class="sxs-lookup"><span data-stu-id="8fa99-150">Create a new OWIN Startup class and name it `ProductionStartup`.</span></span>
2. <span data-ttu-id="8fa99-151">Ersetzen Sie den generierten Code durch den folgenden:</span><span class="sxs-lookup"><span data-stu-id="8fa99-151">Replace the generated code with the following:</span></span>

    [!code-csharp[Main](owin-startup-class-detection/samples/sample9.cs?highlight=14-18)]
3. <span data-ttu-id="8fa99-152">Drücken Sie F5-Steuerelement, um die app auszuführen.</span><span class="sxs-lookup"><span data-stu-id="8fa99-152">Press Control F5 to run the app.</span></span> <span data-ttu-id="8fa99-153">Die `OwinStartup` Attribut gibt an, die Produktions-Startup-Klasse ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="8fa99-153">The `OwinStartup` attribute specifies the production startup class is run.</span></span>

    ![](owin-startup-class-detection/_static/image6.png)
4. <span data-ttu-id="8fa99-154">Erstellen Sie eine andere OWIN-Startup-Klasse und nennen sie `TestStartup`.</span><span class="sxs-lookup"><span data-stu-id="8fa99-154">Create another OWIN Startup class and name it `TestStartup`.</span></span>
5. <span data-ttu-id="8fa99-155">Ersetzen Sie den generierten Code durch den folgenden:</span><span class="sxs-lookup"><span data-stu-id="8fa99-155">Replace the generated code with the following:</span></span>

    [!code-csharp[Main](owin-startup-class-detection/samples/sample10.cs?highlight=6,14-18)]

   <span data-ttu-id="8fa99-156">Die `OwinStartup` Attribut-Überladung, die oben genannten gibt `TestingConfiguration` als die *benutzerfreundliche* Name der Startup-Klasse.</span><span class="sxs-lookup"><span data-stu-id="8fa99-156">The `OwinStartup` attribute overload above specifies `TestingConfiguration` as the *friendly* name of the Startup class.</span></span>
6. <span data-ttu-id="8fa99-157">Öffnen der *"Web.config"* Datei, und fügen Sie den Schlüssel der OWIN-App-Start die angibt, den Anzeigenamen der Startup-Klasse:</span><span class="sxs-lookup"><span data-stu-id="8fa99-157">Open the *web.config* file and add the OWIN App startup key which specifies the friendly name of the Startup class:</span></span>

    [!code-xml[Main](owin-startup-class-detection/samples/sample11.xml?highlight=3-5)]
7. <span data-ttu-id="8fa99-158">Drücken Sie F5-Steuerelement, um die app auszuführen.</span><span class="sxs-lookup"><span data-stu-id="8fa99-158">Press Control F5 to run the app.</span></span> <span data-ttu-id="8fa99-159">Das app-Einstellungselement verwendet wird, Vorrang, und der Konfiguration ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="8fa99-159">The app settings element takes precedent, and the test configuration is run.</span></span>

    ![](owin-startup-class-detection/_static/image7.png)
8. <span data-ttu-id="8fa99-160">Entfernen der *Anzeigenamen* Namen aus der `OwinStartup` -Attribut in der `TestStartup` Klasse.</span><span class="sxs-lookup"><span data-stu-id="8fa99-160">Remove the *friendly* name from the `OwinStartup` attribute in the `TestStartup` class.</span></span>

    [!code-csharp[Main](owin-startup-class-detection/samples/sample12.cs)]
9. <span data-ttu-id="8fa99-161">Ersetzen Sie den Schlüssel zum Systemstart OWIN-App in der *"Web.config"* Datei durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="8fa99-161">Replace the OWIN App startup key in the *web.config* file with the following:</span></span>

    [!code-xml[Main](owin-startup-class-detection/samples/sample13.xml)]
10. <span data-ttu-id="8fa99-162">Wiederherstellen der `OwinStartup` Attribut in jeder Klasse die Attribut-Standardcode, die von Visual Studio generiert:</span><span class="sxs-lookup"><span data-stu-id="8fa99-162">Revert the `OwinStartup` attribute in each class to the default attribute code generated by Visual Studio:</span></span>

    [!code-csharp[Main](owin-startup-class-detection/samples/sample14.cs)]

    <span data-ttu-id="8fa99-163">Die OWIN-App-Start-Schlüssel unten bewirkt, dass die Produktions-Klasse, ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="8fa99-163">Each of the OWIN App startup keys below will cause the production class to run.</span></span>

    [!code-xml[Main](owin-startup-class-detection/samples/sample15.xml)]

    <span data-ttu-id="8fa99-164">Der letzte Schlüssel zum Systemstart gibt der Startmethode für die Konfiguration an.</span><span class="sxs-lookup"><span data-stu-id="8fa99-164">The last startup key specifies the startup configuration method.</span></span> <span data-ttu-id="8fa99-165">Die folgende Schlüssel zum Systemstart OWIN-App können Sie zum Ändern des Namens der Configuration-Klasse, `MyConfiguration` .</span><span class="sxs-lookup"><span data-stu-id="8fa99-165">The following OWIN App startup key allows you to change the name of the configuration class to `MyConfiguration` .</span></span>

    [!code-xml[Main](owin-startup-class-detection/samples/sample16.xml)]

## <a name="using-owinhostexe"></a><span data-ttu-id="8fa99-166">Verwenden von Owinhost.exe</span><span class="sxs-lookup"><span data-stu-id="8fa99-166">Using Owinhost.exe</span></span>

1. <span data-ttu-id="8fa99-167">Ersetzen Sie die Datei "Web.config" mit folgendem Markup:</span><span class="sxs-lookup"><span data-stu-id="8fa99-167">Replace the Web.config file with the following markup:</span></span>

    [!code-xml[Main](owin-startup-class-detection/samples/sample17.xml?highlight=3-6)]

   <span data-ttu-id="8fa99-168">Der letzte Schlüssel wins, also in diesem Fall `TestStartup` angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="8fa99-168">The last key wins, so in this case `TestStartup` is specified.</span></span>
2. <span data-ttu-id="8fa99-169">Installieren Sie aus der PMC Owinhost:</span><span class="sxs-lookup"><span data-stu-id="8fa99-169">Install Owinhost from the PMC:</span></span>

    [!code-console[Main](owin-startup-class-detection/samples/sample18.cmd)]
3. <span data-ttu-id="8fa99-170">Navigieren Sie zum Ordner Anwendung (dem Ordner mit den *"Web.config"* Datei) und in eine Eingabeaufforderung und geben:</span><span class="sxs-lookup"><span data-stu-id="8fa99-170">Navigate to the application folder (the folder containing the *Web.config* file) and in a command prompt and type:</span></span>

    [!code-console[Main](owin-startup-class-detection/samples/sample19.cmd)]

   <span data-ttu-id="8fa99-171">Im Befehlsfenster wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="8fa99-171">The command window will show:</span></span>

    [!code-console[Main](owin-startup-class-detection/samples/sample20.cmd)]
4. <span data-ttu-id="8fa99-172">Startet einen Browser mit der URL `http://localhost:5000/`.</span><span class="sxs-lookup"><span data-stu-id="8fa99-172">Launch a browser with the URL `http://localhost:5000/`.</span></span>

    ![](owin-startup-class-detection/_static/image8.png)

   <span data-ttu-id="8fa99-173">OwinHost berücksichtigt die Start-Konventionen, die oben aufgeführten.</span><span class="sxs-lookup"><span data-stu-id="8fa99-173">OwinHost honored the startup conventions listed above.</span></span>
5. <span data-ttu-id="8fa99-174">Im Befehlsfenster die EINGABETASTE, um OwinHost zu beenden.</span><span class="sxs-lookup"><span data-stu-id="8fa99-174">In the command window, press Enter to exit OwinHost.</span></span>
6. <span data-ttu-id="8fa99-175">In der `ProductionStartup` -Klasse verwenden, fügen Sie das folgende OwinStartup-Attribut, die angibt, den Anzeigenamen *ProductionConfiguration*.</span><span class="sxs-lookup"><span data-stu-id="8fa99-175">In the `ProductionStartup` class, add the following OwinStartup attribute which specifies a friendly name of *ProductionConfiguration*.</span></span>

    [!code-csharp[Main](owin-startup-class-detection/samples/sample21.cs)]
7. <span data-ttu-id="8fa99-176">In der Eingabeaufforderung und geben:</span><span class="sxs-lookup"><span data-stu-id="8fa99-176">In the command prompt and type:</span></span>

    [!code-console[Main](owin-startup-class-detection/samples/sample22.cmd)]

   <span data-ttu-id="8fa99-177">Die Produktions-Startup-Klasse wird geladen.</span><span class="sxs-lookup"><span data-stu-id="8fa99-177">The Production startup class is loaded.</span></span>
    ![](owin-startup-class-detection/_static/image9.png)

   <span data-ttu-id="8fa99-178">Die Anwendung verfügt über mehrere Klassen von Start, und in diesem Beispiel weisen wir eine verzögerte die Startup-Klasse, um bis zur Laufzeit zu laden.</span><span class="sxs-lookup"><span data-stu-id="8fa99-178">Our application has multiple startup classes, and in this example we have deferred which startup class to load until runtime.</span></span>
8. <span data-ttu-id="8fa99-179">Testen Sie die folgenden Startoptionen für die Common Language Runtime:</span><span class="sxs-lookup"><span data-stu-id="8fa99-179">Test the following runtime startup options:</span></span>

    [!code-console[Main](owin-startup-class-detection/samples/sample23.cmd)]
