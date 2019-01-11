---
title: ASP.NET Core laden/Belastungstests
author: Jeremy-Meng
description: Beschreibt verschiedene wichtige Tools und Ansätze für Auslastungstests und Belastungstests in ASP.NET Core-apps.
ms.author: riande
ms.custom: mvc
ms.date: 01/04/2019
uid: test/loadtests
ms.openlocfilehash: d989bc841a372bed7ebf2c84c6abe1a57762ad04
ms.sourcegitcommit: cec77d5ad8a0cedb1ecbec32834111492afd0cd2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207355"
---
# <a name="load-and-stress-testing-aspnet-core"></a><span data-ttu-id="2db49-103">Laden und Belastungstests Testen von ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2db49-103">Load and stress testing ASP.NET Core</span></span>

<span data-ttu-id="2db49-104">Auslastungstests und Belastungstests sind wichtig, sicherzustellen, dass eine Web-app leistungsfähig ist und skalierbar.</span><span class="sxs-lookup"><span data-stu-id="2db49-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="2db49-105">Ihre Ziele unterscheiden sich auch sie häufig ähnliche Tests verwenden.</span><span class="sxs-lookup"><span data-stu-id="2db49-105">Their goals are different even they often share similar tests.</span></span>

<span data-ttu-id="2db49-106">**Auslastungstests**: Testet, ob die app auf eine angegebene Last von Benutzern für eine bestimmte Szenario behandeln kann, und dabei trotzdem das Antwortziel.</span><span class="sxs-lookup"><span data-stu-id="2db49-106">**Load tests**: Tests whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="2db49-107">Die app wird unter normalen Bedingungen ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2db49-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="2db49-108">**Belastungstests**: Tests app Stabilität bei der Ausführung unter extremen Bedingungen und häufig eine längere Zeit:</span><span class="sxs-lookup"><span data-stu-id="2db49-108">**Stress tests**: Tests app stability when running under extreme conditions and often a long period of time:</span></span>

* <span data-ttu-id="2db49-109">Hohe benutzerauslastung – Spitzen oder allmählich.</span><span class="sxs-lookup"><span data-stu-id="2db49-109">High user load – either spikes or gradually increasing.</span></span>
* <span data-ttu-id="2db49-110">Compute-Ressourcen beschränkt.</span><span class="sxs-lookup"><span data-stu-id="2db49-110">Limited computing resources.</span></span>  

<span data-ttu-id="2db49-111">Bei hoher Belastung kann die app Wiederherstellen nach einem Fehler und zurück zum erwarteten Verhalten?</span><span class="sxs-lookup"><span data-stu-id="2db49-111">Under stress, can the app recover from failure and gracefully return to expected behavior?</span></span> <span data-ttu-id="2db49-112">Bei hoher Belastung die app ist *nicht* unter normalen Bedingungen ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2db49-112">Under stress, the app is *not* run under normal conditions.</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="2db49-113">Visual Studio Tools</span><span class="sxs-lookup"><span data-stu-id="2db49-113">Visual Studio Tools</span></span>

<span data-ttu-id="2db49-114">Visual Studio ermöglicht das Erstellen, entwickeln und Debuggen von webleistungs-und Auslastungstest.</span><span class="sxs-lookup"><span data-stu-id="2db49-114">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="2db49-115">Eine Option ist verfügbar, um Tests zu erstellen, durch Aufzeichnen der Aktionen im Webbrowser.</span><span class="sxs-lookup"><span data-stu-id="2db49-115">An option is available to create tests by recording actions in web browser.</span></span>

<span data-ttu-id="2db49-116">[Schnellstart: Erstellen ein auslastungstestprojekts](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017) erstellen, konfigurieren und Ausführen eines Auslastungstests mit Visual Studio 2017 Projekte veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="2db49-116">[Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017) shows how to create, configure, and run a load test projects using Visual Studio 2017.</span></span>

<span data-ttu-id="2db49-117">Weitere Informationen finden Sie unter [Zusätzliche Ressourcen](#add).</span><span class="sxs-lookup"><span data-stu-id="2db49-117">See [Additional Resources](#add) for more information.</span></span>

<span data-ttu-id="2db49-118">Auslastungstests können in einer lokalen ausgeführt oder führen Sie in der Cloud mit Azure DevOps konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="2db49-118">Load tests can be configured to run in on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="azure-devops"></a><span data-ttu-id="2db49-119">Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="2db49-119">Azure DevOps</span></span>

<span data-ttu-id="2db49-120">Auslastungstestläufe können gestartet werden, mithilfe der [Azure DevOps Testplänen](/azure/devops/test/load-test/index?view=vsts) Service.</span><span class="sxs-lookup"><span data-stu-id="2db49-120">Load test runs can be started using the [Azure DevOps Test Plans](/azure/devops/test/load-test/index?view=vsts) service.</span></span>

![](./load-tests/_static/azure-devops-load-test.png)

<span data-ttu-id="2db49-121">Der Dienst unterstützt die folgenden Typen von Test-Format an:</span><span class="sxs-lookup"><span data-stu-id="2db49-121">The service supports the following types of test format:</span></span>

- <span data-ttu-id="2db49-122">Visual Studio-Test – Webtest in Visual Studio erstellt.</span><span class="sxs-lookup"><span data-stu-id="2db49-122">Visual Studio test – web test created in Visual Studio.</span></span>
- <span data-ttu-id="2db49-123">HTTP-Archiv-basierten Test – erfassten HTTP-Datenverkehr in das Archiv ist während der Tests wiedergegeben.</span><span class="sxs-lookup"><span data-stu-id="2db49-123">HTTP Archive-based test – captured HTTP traffic inside archive is replayed during testing.</span></span>
- <span data-ttu-id="2db49-124">[URL-basierter Test](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) – ermöglicht das Angeben von URLs, um den Test, Anforderungstypen, Header und Abfragezeichenfolgen zu laden.</span><span class="sxs-lookup"><span data-stu-id="2db49-124">[URL-based test](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) – allows specifying URLs to load test, request types, headers, and query strings.</span></span> <span data-ttu-id="2db49-125">Festlegen von Parametern, z. B. Dauer ausgeführt werden, kann eine Auslastungsmuster, Anzahl der Benutzer usw., konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="2db49-125">Run setting parameters such as duration, load pattern, number of users, etc., can be configured.</span></span>
- <span data-ttu-id="2db49-126">[Apache JMeter](https://jmeter.apache.org/) testen.</span><span class="sxs-lookup"><span data-stu-id="2db49-126">[Apache JMeter](https://jmeter.apache.org/) test.</span></span>

## <a name="azure-portal"></a><span data-ttu-id="2db49-127">Azure-Portal</span><span class="sxs-lookup"><span data-stu-id="2db49-127">Azure portal</span></span>

<span data-ttu-id="2db49-128">[Azure-Portal ermöglicht das Einrichten und Ausführen von Web-Apps, die Auslastungstests](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) direkt über die Registerkarte "Leistung" des App Service im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="2db49-128">[Azure portal allows setting up and running load testing of Web Apps,](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) directly from the Performance tab of the App Service in Azure portal.</span></span>

![](./load-tests/_static/azure-appservice-perf-test.png)

<span data-ttu-id="2db49-129">Der Test kann einen manuellen Test mit einer angegebenen URL oder eine Visual Studio-Webtest-Datei, die mehrere URLs zu testen sein.</span><span class="sxs-lookup"><span data-stu-id="2db49-129">The test can be a manual test with a specified URL, or a Visual Studio Web Test file, which can test multiple URLs.</span></span>

![](./load-tests/_static/azure-appservice-perf-test-config.png)

<span data-ttu-id="2db49-130">Am Ende des Tests werden Berichte generiert, um die Leistungsmerkmale der app anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="2db49-130">At end of the test, reports are generated to show the performance characteristics of the app.</span></span> <span data-ttu-id="2db49-131">Beispiel-Statistiken umfassen:</span><span class="sxs-lookup"><span data-stu-id="2db49-131">Example statistics include:</span></span>

- <span data-ttu-id="2db49-132">Durchschnittliche Antwortzeit</span><span class="sxs-lookup"><span data-stu-id="2db49-132">Average response time</span></span>
- <span data-ttu-id="2db49-133">Maximaler Durchsatz: Anforderungen pro Sekunde</span><span class="sxs-lookup"><span data-stu-id="2db49-133">Max throughput: requests per second</span></span>
- <span data-ttu-id="2db49-134">Ausführungsfehler in Prozent</span><span class="sxs-lookup"><span data-stu-id="2db49-134">Failure percentage</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="2db49-135">Drittanbieter-Tools</span><span class="sxs-lookup"><span data-stu-id="2db49-135">Third-party Tools</span></span>

<span data-ttu-id="2db49-136">Die folgende Liste enthält die Drittanbieter-Web-Leistungstools mit verschiedenen Featuresätze:</span><span class="sxs-lookup"><span data-stu-id="2db49-136">The following list contains third-party web performance tools with various feature sets:</span></span>

- <span data-ttu-id="2db49-137">[Apache JMeter](https://jmeter.apache.org/) : Vollständige ausgewählte Sammlung von Testtools laden.</span><span class="sxs-lookup"><span data-stu-id="2db49-137">[Apache JMeter](https://jmeter.apache.org/) : Full featured suite of load testing tools.</span></span> <span data-ttu-id="2db49-138">Threadgebunden: benötigen Sie einen Thread pro Benutzer.</span><span class="sxs-lookup"><span data-stu-id="2db49-138">Thread-bound: need one thread per user.</span></span>
- [<span data-ttu-id="2db49-139">Ab – Apache HTTP Server Benchmark tool</span><span class="sxs-lookup"><span data-stu-id="2db49-139">ab - Apache HTTP server benchmarking tool</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
- <span data-ttu-id="2db49-140">[Gatling](https://gatling.io/) : Desktop-Tool mit einer grafischen Benutzeroberfläche und Test Recorder.</span><span class="sxs-lookup"><span data-stu-id="2db49-140">[Gatling](https://gatling.io/) : Desktop tool with a GUI and test recorders.</span></span> <span data-ttu-id="2db49-141">Bieten eine bessere Leistung als JMeter.</span><span class="sxs-lookup"><span data-stu-id="2db49-141">More performant than JMeter.</span></span>
- <span data-ttu-id="2db49-142">[Locust.IO](https://locust.io/) : Nicht begrenzt von Threads.</span><span class="sxs-lookup"><span data-stu-id="2db49-142">[Locust.io](https://locust.io/) : Not bounded by threads.</span></span>

<a name="add"></a>
## <a name="additional-resources"></a><span data-ttu-id="2db49-143">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="2db49-143">Additional Resources</span></span>

<span data-ttu-id="2db49-144">[Laden Sie die Test-blogreihe](https://blogs.msdn.microsoft.com/charles_sterling/2015/06/01/load-test-series-part-i-creating-web-performance-tests-for-a-load-test/) von Charles Sterling.</span><span class="sxs-lookup"><span data-stu-id="2db49-144">[Load Test blog series](https://blogs.msdn.microsoft.com/charles_sterling/2015/06/01/load-test-series-part-i-creating-web-performance-tests-for-a-load-test/) by Charles Sterling.</span></span> <span data-ttu-id="2db49-145">Vom, aber die meisten Themen sind immer noch relevant.</span><span class="sxs-lookup"><span data-stu-id="2db49-145">Dated but most of the topics are still relevant.</span></span>
