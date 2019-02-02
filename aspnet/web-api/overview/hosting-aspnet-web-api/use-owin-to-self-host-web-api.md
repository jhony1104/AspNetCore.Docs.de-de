---
uid: web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api
title: Verwenden von OWIN zum Selfhosten von ASP.NET-Web-API | Microsoft-Dokumentation
author: rick-anderson
description: In diesem Tutorial wird gezeigt, wie ASP.NET Web-API in einer Konsolenanwendung, die mithilfe von OWIN zum selfhosten der Web-API-Framework gehostet wird. Öffnen Sie die Web Interface for .NET (OWIN) d...
ms.author: riande
ms.date: 07/09/2013
ms.assetid: a90a04ce-9d07-43ad-8250-8a92fb2bd3d5
msc.legacyurl: /web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api
msc.type: authoredcontent
ms.openlocfilehash: 59ce24aa47ca590fbe9b617dbbe8bc6b3711849e
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667387"
---
<a name="use-owin-to-self-host-aspnet-web-api"></a><span data-ttu-id="fe8a6-104">Verwenden von OWIN zum Selfhosten von ASP.NET-Web-API</span><span class="sxs-lookup"><span data-stu-id="fe8a6-104">Use OWIN to Self-Host ASP.NET Web API</span></span> 
====================

> <span data-ttu-id="fe8a6-105">In diesem Tutorial wird gezeigt, wie ASP.NET Web-API in einer Konsolenanwendung, die mithilfe von OWIN zum selfhosten der Web-API-Framework gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="fe8a6-105">This tutorial shows how to host ASP.NET Web API in a console application, using OWIN to self-host the Web API framework.</span></span>
>
> <span data-ttu-id="fe8a6-106">[Öffnen von Weboberfläche für .NET](http://owin.org) (OWIN) definiert eine Abstraktion zwischen Webservern für .NET und Webanwendungen.</span><span class="sxs-lookup"><span data-stu-id="fe8a6-106">[Open Web Interface for .NET](http://owin.org) (OWIN) defines an abstraction between .NET web servers and web applications.</span></span> <span data-ttu-id="fe8a6-107">OWIN entkoppelt die Webanwendung aus dem Server, wodurch OWIN ideal für das Selbsthosting einer Webanwendung in Ihrem eigenen Prozess, außerhalb von IIS.</span><span class="sxs-lookup"><span data-stu-id="fe8a6-107">OWIN decouples the web application from the server, which makes OWIN ideal for self-hosting a web application in your own process, outside of IIS.</span></span>
>
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="fe8a6-108">Softwareversionen, die in diesem Tutorial verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="fe8a6-108">Software versions used in the tutorial</span></span>
>
>
> - [<span data-ttu-id="fe8a6-109">Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="fe8a6-109">Visual Studio 2017</span></span>](https://visualstudio.microsoft.com/downloads/) 
> - <span data-ttu-id="fe8a6-110">Web-API 5.2.7</span><span class="sxs-lookup"><span data-stu-id="fe8a6-110">Web API 5.2.7</span></span>


> [!NOTE]
> <span data-ttu-id="fe8a6-111">Sie finden den vollständigen Quellcode für dieses Tutorial auf [aspnet.codeplex.com](https://aspnet.codeplex.com/SourceControl/latest#Samples/WebApi/OwinSelfhostSample/ReadMe.txt).</span><span class="sxs-lookup"><span data-stu-id="fe8a6-111">You can find the complete source code for this tutorial at [aspnet.codeplex.com](https://aspnet.codeplex.com/SourceControl/latest#Samples/WebApi/OwinSelfhostSample/ReadMe.txt).</span></span>


## <a name="create-a-console-application"></a><span data-ttu-id="fe8a6-112">Erstellen einer Konsolenanwendung</span><span class="sxs-lookup"><span data-stu-id="fe8a6-112">Create a console application</span></span>

<span data-ttu-id="fe8a6-113">Auf der **Datei** Menü **neu**, und wählen Sie dann **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="fe8a6-113">On the **File** menu,  **New**, then select **Project**.</span></span> <span data-ttu-id="fe8a6-114">Von **installiert**unter **Visual C#** Option **Windows Desktop** und wählen Sie dann **Konsolen-App (.Net Framework)**.</span><span class="sxs-lookup"><span data-stu-id="fe8a6-114">From **Installed**, under **Visual C#**, select **Windows Desktop** and then select **Console App (.Net Framework)**.</span></span> <span data-ttu-id="fe8a6-115">Nennen Sie das Projekt "OwinSelfhostSample", und wählen Sie **OK**.</span><span class="sxs-lookup"><span data-stu-id="fe8a6-115">Name the project "OwinSelfhostSample" and select **OK**.</span></span>

[![](use-owin-to-self-host-web-api/_static/image7.png)](use-owin-to-self-host-web-api/_static/image7.png)

## <a name="add-the-web-api-and-owin-packages"></a><span data-ttu-id="fe8a6-116">Fügen Sie die Web-API und OWIN-Pakete</span><span class="sxs-lookup"><span data-stu-id="fe8a6-116">Add the Web API and OWIN packages</span></span>

<span data-ttu-id="fe8a6-117">Von der **Tools** , wählen Sie im Menü **NuGet Package Manager**, und wählen Sie dann **-Paket-Manager-Konsole**.</span><span class="sxs-lookup"><span data-stu-id="fe8a6-117">From the **Tools** menu, select **NuGet Package Manager**, then select **Package Manager Console**.</span></span> <span data-ttu-id="fe8a6-118">Geben Sie im Fenster Paket-Manager-Konsole den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="fe8a6-118">In the Package Manager Console window, enter the following command:</span></span>

`Install-Package Microsoft.AspNet.WebApi.OwinSelfHost`

<span data-ttu-id="fe8a6-119">Hiermit wird dem WebAPI-OWIN-Selfhost-Paket und alle erforderlichen Pakete der OWIN-installiert.</span><span class="sxs-lookup"><span data-stu-id="fe8a6-119">This will install the WebAPI OWIN selfhost package and all the required OWIN packages.</span></span>

[![](use-owin-to-self-host-web-api/_static/image4.png)](use-owin-to-self-host-web-api/_static/image3.png)

## <a name="configure-web-api-for-self-host"></a><span data-ttu-id="fe8a6-120">Konfigurieren von Web-API für die Self-Hosting</span><span class="sxs-lookup"><span data-stu-id="fe8a6-120">Configure Web API for self-host</span></span>

<span data-ttu-id="fe8a6-121">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste in des Projekts, und wählen **hinzufügen** / **Klasse** eine neue Klasse hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="fe8a6-121">In Solution Explorer, right-click the project and select **Add** / **Class** to add a new class.</span></span> <span data-ttu-id="fe8a6-122">Nennen Sie die Klasse `Startup`.</span><span class="sxs-lookup"><span data-stu-id="fe8a6-122">Name the class `Startup`.</span></span>

![](use-owin-to-self-host-web-api/_static/image5.png)

<span data-ttu-id="fe8a6-123">Ersetzen Sie alle die Codebausteine in dieser Datei durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="fe8a6-123">Replace all of the boilerplate code in this file with the following:</span></span>

[!code-csharp[Main](use-owin-to-self-host-web-api/samples/sample1.cs)]

## <a name="add-a-web-api-controller"></a><span data-ttu-id="fe8a6-124">Hinzufügen eines Web-API-Controllers</span><span class="sxs-lookup"><span data-stu-id="fe8a6-124">Add a Web API controller</span></span>

<span data-ttu-id="fe8a6-125">Als Nächstes fügen Sie eine Web-API-Controller-Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="fe8a6-125">Next, add a Web API controller class.</span></span> <span data-ttu-id="fe8a6-126">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste in des Projekts, und wählen **hinzufügen** / **Klasse** eine neue Klasse hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="fe8a6-126">In Solution Explorer, right-click the project and select **Add** / **Class** to add a new class.</span></span> <span data-ttu-id="fe8a6-127">Nennen Sie die Klasse `ValuesController`.</span><span class="sxs-lookup"><span data-stu-id="fe8a6-127">Name the class `ValuesController`.</span></span>

<span data-ttu-id="fe8a6-128">Ersetzen Sie alle die Codebausteine in dieser Datei durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="fe8a6-128">Replace all of the boilerplate code in this file with the following:</span></span>

[!code-csharp[Main](use-owin-to-self-host-web-api/samples/sample2.cs)]

## <a name="start-the-owin-host-and-make-a-request-with-httpclient"></a><span data-ttu-id="fe8a6-129">Starten Sie die OWIN-Host, und stellen Sie eine Anforderung mit "HttpClient"</span><span class="sxs-lookup"><span data-stu-id="fe8a6-129">Start the OWIN Host and make a request with HttpClient</span></span>

<span data-ttu-id="fe8a6-130">Ersetzen Sie alle die Codebausteine in der Datei "Program.cs" durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="fe8a6-130">Replace all of the boilerplate code in the Program.cs file with the following:</span></span>

[!code-csharp[Main](use-owin-to-self-host-web-api/samples/sample3.cs)]

## <a name="run-the-application"></a><span data-ttu-id="fe8a6-131">Ausführen der Anwendung</span><span class="sxs-lookup"><span data-stu-id="fe8a6-131">Run the application</span></span>

<span data-ttu-id="fe8a6-132">Um die Anwendung auszuführen, drücken Sie F5 in Visual Studio aus.</span><span class="sxs-lookup"><span data-stu-id="fe8a6-132">To run the application, press F5 in Visual Studio.</span></span> <span data-ttu-id="fe8a6-133">Die Ausgabe sollte wie folgt aussehen:</span><span class="sxs-lookup"><span data-stu-id="fe8a6-133">The output should look like the following:</span></span>

[!code-console[Main](use-owin-to-self-host-web-api/samples/sample4.cmd)]

![](use-owin-to-self-host-web-api/_static/image6.png)

## <a name="additional-resources"></a><span data-ttu-id="fe8a6-134">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="fe8a6-134">Additional resources</span></span>

[<span data-ttu-id="fe8a6-135">Übersicht über das Katana-Projekt</span><span class="sxs-lookup"><span data-stu-id="fe8a6-135">An Overview of Project Katana</span></span>](../../../aspnet/overview/owin-and-katana/an-overview-of-project-katana.md)

[<span data-ttu-id="fe8a6-136">Hosten von ASP.NET Web-API in einer Azure-Workerrolle</span><span class="sxs-lookup"><span data-stu-id="fe8a6-136">Host ASP.NET Web API in an Azure Worker Role</span></span>](host-aspnet-web-api-in-an-azure-worker-role.md)
