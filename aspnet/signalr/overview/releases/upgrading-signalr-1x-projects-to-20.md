---
uid: signalr/overview/releases/upgrading-signalr-1x-projects-to-20
title: Aktualisieren von SignalR 1.x-Projekte auf Version 2 | Microsoft-Dokumentation
author: bradygaster
description: In diesem Thema wird beschrieben, wie Sie ein vorhandenes SignalR 1.x-Projekt auf SignalR aktualisieren 2.x, und Beheben von Problemen, die während des Upgrades auftreten können...
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: adcfef99-9bc5-489d-a91b-9b7c2bc35e04
msc.legacyurl: /signalr/overview/releases/upgrading-signalr-1x-projects-to-20
msc.type: authoredcontent
ms.openlocfilehash: a1cb4903f3cdeef70ffd0f624a3a2170f641a395
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837337"
---
<a name="upgrading-signalr-1x-projects-to-version-2"></a><span data-ttu-id="18bb5-103">Aktualisieren von SignalR 1.x-Projekte auf Version 2</span><span class="sxs-lookup"><span data-stu-id="18bb5-103">Upgrading SignalR 1.x Projects to version 2</span></span>
====================
<span data-ttu-id="18bb5-104">durch [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="18bb5-104">by [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="18bb5-105">In diesem Thema wird beschrieben, wie Sie ein vorhandenes SignalR 1.x-Projekt auf SignalR aktualisieren 2.x, und Beheben von Problemen, die während des Upgrades auftreten können.</span><span class="sxs-lookup"><span data-stu-id="18bb5-105">This topic describes how to upgrade an existing SignalR 1.x project to SignalR 2.x, and how to troubleshoot issues that may arise during the upgrade process.</span></span>
>
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="18bb5-106">Softwareversionen, die in diesem Tutorial verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="18bb5-106">Software versions used in the tutorial</span></span>
>
>
> - [<span data-ttu-id="18bb5-107">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="18bb5-107">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="18bb5-108">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="18bb5-108">.NET 4.5</span></span>
> - <span data-ttu-id="18bb5-109">SignalR-Versionen 1 und 2</span><span class="sxs-lookup"><span data-stu-id="18bb5-109">SignalR versions 1 and 2</span></span>
>
>
>
> ## <a name="using-visual-studio-2012-with-this-tutorial"></a><span data-ttu-id="18bb5-110">In diesem Tutorial mithilfe von Visual Studio 2012</span><span class="sxs-lookup"><span data-stu-id="18bb5-110">Using Visual Studio 2012 with this tutorial</span></span>
>
>
> <span data-ttu-id="18bb5-111">Um Visual Studio 2012 mit diesem Tutorial verwenden möchten, führen Sie folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="18bb5-111">To use Visual Studio 2012 with this tutorial, do the following:</span></span>
>
> - <span data-ttu-id="18bb5-112">Aktualisieren Ihrer [-Paket-Manager](http://docs.nuget.org/docs/start-here/installing-nuget) auf die neueste Version.</span><span class="sxs-lookup"><span data-stu-id="18bb5-112">Update your [Package Manager](http://docs.nuget.org/docs/start-here/installing-nuget) to the latest version.</span></span>
> - <span data-ttu-id="18bb5-113">Installieren Sie die [Webplattform-Installer](https://www.microsoft.com/web/downloads/platform.aspx).</span><span class="sxs-lookup"><span data-stu-id="18bb5-113">Install the [Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx).</span></span>
> - <span data-ttu-id="18bb5-114">Suchen Sie in den Webplattform-Installer, und installieren Sie **ASP.NET und Web Tools 2013.1 für Visual Studio 2012**.</span><span class="sxs-lookup"><span data-stu-id="18bb5-114">In the Web Platform Installer, search for and install **ASP.NET and Web Tools 2013.1 for Visual Studio 2012**.</span></span> <span data-ttu-id="18bb5-115">Dadurch wird Visual Studio-Vorlagen für SignalR-Klassen wie z. B. installiert **Hub**.</span><span class="sxs-lookup"><span data-stu-id="18bb5-115">This will install Visual Studio templates for SignalR classes such as **Hub**.</span></span>
> - <span data-ttu-id="18bb5-116">Einige Vorlagen (z. B. **OWIN-Startklasse**) nicht zur Verfügung; verwenden Sie für diese stattdessen eine Klassendatei.</span><span class="sxs-lookup"><span data-stu-id="18bb5-116">Some templates (such as **OWIN Startup Class**) will not be available; for these, use a Class file instead.</span></span>
>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="18bb5-117">Fragen und Kommentare</span><span class="sxs-lookup"><span data-stu-id="18bb5-117">Questions and comments</span></span>
>
> <span data-ttu-id="18bb5-118">Lassen Sie Feedback, auf wie Ihnen in diesem Tutorial gefallen hat und was wir in den Kommentaren am unteren Rand der Seite verbessern können.</span><span class="sxs-lookup"><span data-stu-id="18bb5-118">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="18bb5-119">Wenn Sie Fragen, die nicht direkt mit dem Tutorial verknüpft sind haben, können Sie sie veröffentlichen das [ASP.NET SignalR-Forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) oder [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="18bb5-119">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


<span data-ttu-id="18bb5-120">SignalR 2 bietet eine einheitliche Entwicklungsumgebung bietet über Serverplattformen hinweg [OWIN](http://owin.org).</span><span class="sxs-lookup"><span data-stu-id="18bb5-120">SignalR 2 offers a consistent development experience across server platforms using [OWIN](http://owin.org).</span></span> <span data-ttu-id="18bb5-121">Dieser Artikel beschreibt die Schritte, die erforderlich sind, um einer SignalR 1.x-Anwendung auf Version 2 zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="18bb5-121">This article describes the few steps that are needed to update a SignalR 1.x application to version 2.</span></span>

<span data-ttu-id="18bb5-122">Obwohl empfohlen wird, Anwendungen mit SignalR 2 zu aktualisieren, werden SignalR 1.x weiterhin unterstützt.</span><span class="sxs-lookup"><span data-stu-id="18bb5-122">While it is encouraged to upgrade applications to SignalR 2, SignalR 1.x will still be supported.</span></span>

<span data-ttu-id="18bb5-123">Dieses Tutorial beschreibt, wie Sie eine im Internet gehostete Anwendung mit SignalR 2 zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="18bb5-123">This tutorial describes how to upgrade a web-hosted application to SignalR 2.</span></span> <span data-ttu-id="18bb5-124">Selbst gehostete Anwendungen (diejenigen, die ein Server in einer Konsolenanwendung, Windows-Dienst oder andere geplante Prozesses hosten) werden jetzt als SignalR-2 unterstützt.</span><span class="sxs-lookup"><span data-stu-id="18bb5-124">Self-hosted applications (those that host a server in a console application, Windows service, or other process) are now supported under SignalR 2.</span></span> <span data-ttu-id="18bb5-125">Informationen zum Erstellen einer selbst gehosteten Anwendung mit SignalR 2 beginnen, finden Sie unter [Lernprogramm: Selfhosten von SignalR](../deployment/tutorial-signalr-self-host.md).</span><span class="sxs-lookup"><span data-stu-id="18bb5-125">For information on how to get started creating a self-hosted application with SignalR 2, see [Tutorial: SignalR Self-Host](../deployment/tutorial-signalr-self-host.md).</span></span>

## <a name="contents"></a><span data-ttu-id="18bb5-126">Inhalt</span><span class="sxs-lookup"><span data-stu-id="18bb5-126">Contents</span></span>

<span data-ttu-id="18bb5-127">Die folgenden Abschnitte beschreiben die Aufgaben zum beim Upgrade von SignalR-Projekte, und Beheben von Problemen, die auftreten können.</span><span class="sxs-lookup"><span data-stu-id="18bb5-127">The following sections describe tasks involved with upgrading SignalR projects, and how to troubleshoot issues that may arise.</span></span>

- [<span data-ttu-id="18bb5-128">Anpassen von mit VSTU Aktualisieren die Tutorials: Erste Schritte mit SignalR 2</span><span class="sxs-lookup"><span data-stu-id="18bb5-128">Example: Upgrading the Getting Started tutorial to SignalR 2</span></span>](#example)
- [<span data-ttu-id="18bb5-129">Problembehandlung für Fehler, die während des Upgrades</span><span class="sxs-lookup"><span data-stu-id="18bb5-129">Troubleshooting errors encountered during upgrading</span></span>](#troubleshooting)

<a id="example"></a>

## <a name="example-upgrading-the-getting-started-tutorial-application-to-signalr-2"></a><span data-ttu-id="18bb5-130">Beispiel: Aktualisieren die erste Schritte-Tutorial Anwendung mit SignalR 2</span><span class="sxs-lookup"><span data-stu-id="18bb5-130">Example: Upgrading the Getting Started tutorial application to SignalR 2</span></span>

<span data-ttu-id="18bb5-131">In diesem Abschnitt Aktualisieren Sie die Anwendung, die erstellt werden, der [SignalR 1.x-Version der Getting Started Tutorial](../older-versions/index.md) mit SignalR 2.</span><span class="sxs-lookup"><span data-stu-id="18bb5-131">In this section, you'll update the application created in the [SignalR 1.x version of the Getting Started Tutorial](../older-versions/index.md) to use SignalR 2.</span></span>

1. <span data-ttu-id="18bb5-132">Nachdem Sie das erste Schritte-Tutorial abgeschlossen haben, mit der rechten Maustaste auf das Projekt, und wählen **Eigenschaften**.</span><span class="sxs-lookup"><span data-stu-id="18bb5-132">Once you've finished the Getting Started tutorial, right-click on the project, and select **Properties**.</span></span> <span data-ttu-id="18bb5-133">Überprüfen Sie, ob die **Zielframework** nastaven NA hodnotu **.NET Framework 4.5.**</span><span class="sxs-lookup"><span data-stu-id="18bb5-133">Verify that the **Target framework** is set to **.NET Framework 4.5.**</span></span>
2. <span data-ttu-id="18bb5-134">Öffnen Sie die Paket-Manager-Konsole.</span><span class="sxs-lookup"><span data-stu-id="18bb5-134">Open the Package Manager Console.</span></span> <span data-ttu-id="18bb5-135">Entfernen von SignalR 1.x aus dem Projekt mit dem folgenden Befehl:</span><span class="sxs-lookup"><span data-stu-id="18bb5-135">Remove SignalR 1.x from the project using the following command:</span></span>

    [!code-powershell[Main](upgrading-signalr-1x-projects-to-20/samples/sample1.ps1)]
3. <span data-ttu-id="18bb5-136">Installieren Sie SignalR 2. mit dem folgenden Befehl ein:</span><span class="sxs-lookup"><span data-stu-id="18bb5-136">Install SignalR 2 using the following command:</span></span>

    [!code-powershell[Main](upgrading-signalr-1x-projects-to-20/samples/sample2.ps1)]
4. <span data-ttu-id="18bb5-137">Aktualisieren Sie in der HTML-Seite den Skriptverweis für SignalR entsprechend der Version des Skripts nun im Projekt enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="18bb5-137">In the HTML page, update the script reference for SignalR to match the version of the script now included in the project.</span></span>

    [!code-html[Main](upgrading-signalr-1x-projects-to-20/samples/sample3.html)]
5. <span data-ttu-id="18bb5-138">Entfernen Sie den Aufruf MapHubs, in der globalen Anwendung-Klasse.</span><span class="sxs-lookup"><span data-stu-id="18bb5-138">In the global application class, remove the call to MapHubs.</span></span>

    [!code-csharp[Main](upgrading-signalr-1x-projects-to-20/samples/sample4.cs)]
6. <span data-ttu-id="18bb5-139">Mit der rechten Maustaste in der Projektmappe, und wählen **hinzufügen**, **neues Element...** . Wählen Sie im Dialogfeld **Owin-Startklasse**.</span><span class="sxs-lookup"><span data-stu-id="18bb5-139">Right-click the solution, and select **Add**, **New Item...**. In the dialog, select **Owin Startup Class**.</span></span> <span data-ttu-id="18bb5-140">Nennen Sie die neue Klasse **"Startup.cs"**.</span><span class="sxs-lookup"><span data-stu-id="18bb5-140">Name the new class **Startup.cs**.</span></span>

    ![](upgrading-signalr-1x-projects-to-20/_static/image1.png)
7. <span data-ttu-id="18bb5-141">Ersetzen Sie den Inhalt der "Startup.cs" durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="18bb5-141">Replace the contents of Startup.cs with the following code:</span></span>

    [!code-csharp[Main](upgrading-signalr-1x-projects-to-20/samples/sample5.cs)]

    <span data-ttu-id="18bb5-142">Das Assemblyattribut Owins-Startvorgangs, führt die Klasse hinzugefügt der `Configuration` Methode, wenn die Owin gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="18bb5-142">The assembly attribute adds the class to Owin's startup process, which executes the `Configuration` method when Owin starts up.</span></span> <span data-ttu-id="18bb5-143">Dies wiederum ruft die `MapSignalR` -Methode, die Routen für alle SignalR-Hubs in der Anwendung erstellt.</span><span class="sxs-lookup"><span data-stu-id="18bb5-143">This in turn calls the `MapSignalR` method, which creates routes for all SignalR hubs in the application.</span></span>
8. <span data-ttu-id="18bb5-144">Führen Sie das Projekt, und kopieren Sie die URL der Hauptseite in einen anderen Browser oder Browserbereich wie vorher.</span><span class="sxs-lookup"><span data-stu-id="18bb5-144">Run the project, and copy the URL of the main page into another browser or browser pane, as before.</span></span> <span data-ttu-id="18bb5-145">Jede Seite wird gefragt, einen Benutzernamen ein, und von jeder Seite gesendeten Daten beider Bereiche Browser angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="18bb5-145">Each page will ask for a username, and messages sent from each page should be visible in both browser panes.</span></span>

<a id="troubleshooting"></a>

## <a name="troubleshooting-errors-encountered-during-upgrading"></a><span data-ttu-id="18bb5-146">Problembehandlung für Fehler, die während des Upgrades</span><span class="sxs-lookup"><span data-stu-id="18bb5-146">Troubleshooting errors encountered during upgrading</span></span>

<span data-ttu-id="18bb5-147">Dieser Abschnitt beschreibt Probleme, die während des Upgrades auftreten können.</span><span class="sxs-lookup"><span data-stu-id="18bb5-147">This section describes issues that may arise during upgrading.</span></span> <span data-ttu-id="18bb5-148">Eine umfangreichere Liste von Fehlern und Problemen, die mit einer SignalR-Anwendung auftreten können, finden Sie unter [SignalR Problembehandlung](../testing-and-debugging/troubleshooting.md).</span><span class="sxs-lookup"><span data-stu-id="18bb5-148">For a more comprehensive list of errors and issues that may occur with a SignalR application, see [SignalR Troubleshooting](../testing-and-debugging/troubleshooting.md).</span></span>

### <a name="the-call-is-ambiguous-between-the-following-methods-or-properties"></a><span data-ttu-id="18bb5-149">'Der Aufruf ist nicht eindeutig zwischen folgenden Methoden oder Eigenschaften'</span><span class="sxs-lookup"><span data-stu-id="18bb5-149">'The call is ambiguous between the following methods or properties'</span></span>

<span data-ttu-id="18bb5-150">Dieser Fehler tritt auf, wenn ein Verweis auf `Microsoft.AspNet.SignalR.Owin` wird nicht entfernt.</span><span class="sxs-lookup"><span data-stu-id="18bb5-150">This error will occur if a reference to `Microsoft.AspNet.SignalR.Owin` is not removed.</span></span> <span data-ttu-id="18bb5-151">Dieses Paket ist veraltet. der Verweis muss entfernt werden, und die 1.x-Version des Pakets SelfHost muss deinstalliert werden.</span><span class="sxs-lookup"><span data-stu-id="18bb5-151">This package is deprecated; the reference must be removed and the 1.x version of the SelfHost package must be uninstalled.</span></span>

### <a name="hub-methods-fail-silently"></a><span data-ttu-id="18bb5-152">Hub-Methoden fehl automatisch.</span><span class="sxs-lookup"><span data-stu-id="18bb5-152">Hub methods fail silently</span></span>

<span data-ttu-id="18bb5-153">Überprüfen, ob die Skriptverweise in Ihrem Client bis zum Datums- und, sind die `OwinStartup` Attribut für Ihr Startup-Klasse, die richtige Klasse und den Assemblynamen für das Projekt hat.</span><span class="sxs-lookup"><span data-stu-id="18bb5-153">Verify that the script references in your client are up to date, and that the `OwinStartup` attribute for your Startup class has the correct class and assembly names for your project.</span></span> <span data-ttu-id="18bb5-154">Versuchen Sie außerdem, die die Hubs-Adresse (/ Signalr/Hubs) in Ihrem Browser zu öffnen; alle Fehler, die angezeigt wird bietet weitere Informationen zu welcher Fehler vorliegt.</span><span class="sxs-lookup"><span data-stu-id="18bb5-154">Also, try opening up the hubs address (/signalr/hubs) in your browser; any error that appears will offer more information about what's going wrong.</span></span>
