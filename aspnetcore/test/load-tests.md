---
title: ASP.NET Core laden/Belastungstests
author: Jeremy-Meng
description: Beschreibt verschiedene wichtige Tools und Ansätze für Auslastungstests und Belastungstests in ASP.NET Core-apps.
ms.author: riande
ms.custom: mvc
ms.date: 01/04/2019
uid: test/loadtests
ms.openlocfilehash: 587df6e216943d3eeec779df4d0554dd0fc2fda0
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57345427"
---
# <a name="load-and-stress-testing-aspnet-core"></a><span data-ttu-id="9bd40-103">Laden und Belastungstests Testen von ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9bd40-103">Load and stress testing ASP.NET Core</span></span>

<span data-ttu-id="9bd40-104">Auslastungstests und Belastungstests sind wichtig, sicherzustellen, dass eine Web-app leistungsfähig ist und skalierbar.</span><span class="sxs-lookup"><span data-stu-id="9bd40-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="9bd40-105">Ihre Ziele unterscheiden sich auch sie häufig ähnliche Tests verwenden.</span><span class="sxs-lookup"><span data-stu-id="9bd40-105">Their goals are different even they often share similar tests.</span></span>

<span data-ttu-id="9bd40-106">**Auslastungstests**: Testet, ob die app auf eine angegebene Last von Benutzern für eine bestimmte Szenario behandeln kann, und dabei trotzdem das Antwortziel.</span><span class="sxs-lookup"><span data-stu-id="9bd40-106">**Load tests**: Tests whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="9bd40-107">Die app wird unter normalen Bedingungen ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="9bd40-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="9bd40-108">**Belastungstests**: Tests app Stabilität bei der Ausführung unter extremen Bedingungen und häufig eine längere Zeit:</span><span class="sxs-lookup"><span data-stu-id="9bd40-108">**Stress tests**: Tests app stability when running under extreme conditions and often a long period of time:</span></span>

* <span data-ttu-id="9bd40-109">Hohe benutzerauslastung – Spitzen oder allmählich.</span><span class="sxs-lookup"><span data-stu-id="9bd40-109">High user load – either spikes or gradually increasing.</span></span>
* <span data-ttu-id="9bd40-110">Compute-Ressourcen beschränkt.</span><span class="sxs-lookup"><span data-stu-id="9bd40-110">Limited computing resources.</span></span>  

<span data-ttu-id="9bd40-111">Bei hoher Belastung kann die app Wiederherstellen nach einem Fehler und zurück zum erwarteten Verhalten?</span><span class="sxs-lookup"><span data-stu-id="9bd40-111">Under stress, can the app recover from failure and gracefully return to expected behavior?</span></span> <span data-ttu-id="9bd40-112">Bei hoher Belastung die app ist *nicht* unter normalen Bedingungen ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="9bd40-112">Under stress, the app is *not* run under normal conditions.</span></span>

<span data-ttu-id="9bd40-113">Visual Studio 2019 ist die letzte Version von Visual Studio mit Auslastungstestfunktionen.</span><span class="sxs-lookup"><span data-stu-id="9bd40-113">Visual Studio 2019 will be the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="9bd40-114">Kunden, die Auslastungstesttools benötigen, empfehlen wir, alternative Auslastungstesttools wie Apache JMeter, Akamai CloudTest oder Blazemeter zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="9bd40-114">For customers requiring load testing tools, we recommend using alternate load testing tools such as Apache JMeter, Akamai CloudTest, Blazemeter.</span></span> <span data-ttu-id="9bd40-115">Weitere Informationen finden Sie unter den [2019 Preview Release Anmerkungen zur Visual Studio](/visualstudio/releases/2019/release-notes-preview#test-tools).</span><span class="sxs-lookup"><span data-stu-id="9bd40-115">For more information, see the [Visual Studio 2019 Preview Release Notes](/visualstudio/releases/2019/release-notes-preview#test-tools).</span></span>

<span data-ttu-id="9bd40-116">Die Dienst-Auslastungstests in Azure DevOps endet in 2020.</span><span class="sxs-lookup"><span data-stu-id="9bd40-116">The load testing service in Azure DevOps is ending in 2020.</span></span> <span data-ttu-id="9bd40-117">Weitere Informationen finden Sie unter [cloudbasierte Auslastungstests Service Ende ihrer Lebensdauer](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span><span class="sxs-lookup"><span data-stu-id="9bd40-117">For more information see [Cloud-based load testing service end of life](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="9bd40-118">Visual Studio Tools</span><span class="sxs-lookup"><span data-stu-id="9bd40-118">Visual Studio Tools</span></span>

<span data-ttu-id="9bd40-119">Visual Studio ermöglicht das Erstellen, entwickeln und Debuggen von webleistungs-und Auslastungstest.</span><span class="sxs-lookup"><span data-stu-id="9bd40-119">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="9bd40-120">Eine Option ist verfügbar, um Tests zu erstellen, durch Aufzeichnen der Aktionen im Webbrowser.</span><span class="sxs-lookup"><span data-stu-id="9bd40-120">An option is available to create tests by recording actions in web browser.</span></span>

<span data-ttu-id="9bd40-121">[Schnellstart: Erstellen ein auslastungstestprojekts](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017) erstellen, konfigurieren und Ausführen eines Auslastungstests mit Visual Studio 2017 Projekte veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="9bd40-121">[Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017) shows how to create, configure, and run a load test projects using Visual Studio 2017.</span></span>

<span data-ttu-id="9bd40-122">Weitere Informationen finden Sie unter [Zusätzliche Ressourcen](#add).</span><span class="sxs-lookup"><span data-stu-id="9bd40-122">See [Additional Resources](#add) for more information.</span></span>

<span data-ttu-id="9bd40-123">Auslastungstests können in einer lokalen ausgeführt oder führen Sie in der Cloud mit Azure DevOps konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="9bd40-123">Load tests can be configured to run in on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="azure-devops"></a><span data-ttu-id="9bd40-124">Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="9bd40-124">Azure DevOps</span></span>

<span data-ttu-id="9bd40-125">Auslastungstestläufe können gestartet werden, mithilfe der [Azure DevOps Testplänen](/azure/devops/test/load-test/index?view=vsts) Service.</span><span class="sxs-lookup"><span data-stu-id="9bd40-125">Load test runs can be started using the [Azure DevOps Test Plans](/azure/devops/test/load-test/index?view=vsts) service.</span></span>

![](./load-tests/_static/azure-devops-load-test.png)

<span data-ttu-id="9bd40-126">Der Dienst unterstützt die folgenden Typen von Test-Format an:</span><span class="sxs-lookup"><span data-stu-id="9bd40-126">The service supports the following types of test format:</span></span>

- <span data-ttu-id="9bd40-127">Visual Studio-Test – Webtest in Visual Studio erstellt.</span><span class="sxs-lookup"><span data-stu-id="9bd40-127">Visual Studio test – web test created in Visual Studio.</span></span>
- <span data-ttu-id="9bd40-128">HTTP-Archiv-basierten Test – erfassten HTTP-Datenverkehr in das Archiv ist während der Tests wiedergegeben.</span><span class="sxs-lookup"><span data-stu-id="9bd40-128">HTTP Archive-based test – captured HTTP traffic inside archive is replayed during testing.</span></span>
- <span data-ttu-id="9bd40-129">[URL-basierter Test](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) – ermöglicht das Angeben von URLs, um den Test, Anforderungstypen, Header und Abfragezeichenfolgen zu laden.</span><span class="sxs-lookup"><span data-stu-id="9bd40-129">[URL-based test](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) – allows specifying URLs to load test, request types, headers, and query strings.</span></span> <span data-ttu-id="9bd40-130">Festlegen von Parametern, z. B. Dauer ausgeführt werden, kann eine Auslastungsmuster, Anzahl der Benutzer usw., konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="9bd40-130">Run setting parameters such as duration, load pattern, number of users, etc., can be configured.</span></span>
- <span data-ttu-id="9bd40-131">[Apache JMeter](https://jmeter.apache.org/) test.</span><span class="sxs-lookup"><span data-stu-id="9bd40-131">[Apache JMeter](https://jmeter.apache.org/) test.</span></span>

## <a name="azure-portal"></a><span data-ttu-id="9bd40-132">Azure-Portal</span><span class="sxs-lookup"><span data-stu-id="9bd40-132">Azure portal</span></span>

<span data-ttu-id="9bd40-133">[Azure-Portal ermöglicht das Einrichten und Ausführen von Web-Apps, die Auslastungstests](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) direkt über die Registerkarte "Leistung" des App Service im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="9bd40-133">[Azure portal allows setting up and running load testing of Web Apps,](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) directly from the Performance tab of the App Service in Azure portal.</span></span>

![](./load-tests/_static/azure-appservice-perf-test.png)

<span data-ttu-id="9bd40-134">Der Test kann einen manuellen Test mit einer angegebenen URL oder eine Visual Studio-Webtest-Datei, die mehrere URLs zu testen sein.</span><span class="sxs-lookup"><span data-stu-id="9bd40-134">The test can be a manual test with a specified URL, or a Visual Studio Web Test file, which can test multiple URLs.</span></span>

![](./load-tests/_static/azure-appservice-perf-test-config.png)

<span data-ttu-id="9bd40-135">Am Ende des Tests werden Berichte generiert, um die Leistungsmerkmale der app anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="9bd40-135">At end of the test, reports are generated to show the performance characteristics of the app.</span></span> <span data-ttu-id="9bd40-136">Beispiel-Statistiken umfassen:</span><span class="sxs-lookup"><span data-stu-id="9bd40-136">Example statistics include:</span></span>

- <span data-ttu-id="9bd40-137">Durchschnittliche Antwortzeit</span><span class="sxs-lookup"><span data-stu-id="9bd40-137">Average response time</span></span>
- <span data-ttu-id="9bd40-138">Maximaler Durchsatz: Anforderungen pro Sekunde</span><span class="sxs-lookup"><span data-stu-id="9bd40-138">Max throughput: requests per second</span></span>
- <span data-ttu-id="9bd40-139">Ausführungsfehler in Prozent</span><span class="sxs-lookup"><span data-stu-id="9bd40-139">Failure percentage</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="9bd40-140">Drittanbieter-Tools</span><span class="sxs-lookup"><span data-stu-id="9bd40-140">Third-party Tools</span></span>

<span data-ttu-id="9bd40-141">Die folgende Liste enthält die Drittanbieter-Web-Leistungstools mit verschiedenen Featuresätze:</span><span class="sxs-lookup"><span data-stu-id="9bd40-141">The following list contains third-party web performance tools with various feature sets:</span></span>

- <span data-ttu-id="9bd40-142">[Apache JMeter](https://jmeter.apache.org/) : Vollständige ausgewählte Sammlung von Testtools laden.</span><span class="sxs-lookup"><span data-stu-id="9bd40-142">[Apache JMeter](https://jmeter.apache.org/) : Full featured suite of load testing tools.</span></span> <span data-ttu-id="9bd40-143">Threadgebunden: benötigen Sie einen Thread pro Benutzer.</span><span class="sxs-lookup"><span data-stu-id="9bd40-143">Thread-bound: need one thread per user.</span></span>
- [<span data-ttu-id="9bd40-144">Ab – Apache HTTP Server Benchmark tool</span><span class="sxs-lookup"><span data-stu-id="9bd40-144">ab - Apache HTTP server benchmarking tool</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
- <span data-ttu-id="9bd40-145">[Gatling](https://gatling.io/) : Desktop-Tool mit einer grafischen Benutzeroberfläche und Test Recorder.</span><span class="sxs-lookup"><span data-stu-id="9bd40-145">[Gatling](https://gatling.io/) : Desktop tool with a GUI and test recorders.</span></span> <span data-ttu-id="9bd40-146">Bieten eine bessere Leistung als JMeter.</span><span class="sxs-lookup"><span data-stu-id="9bd40-146">More performant than JMeter.</span></span>
- <span data-ttu-id="9bd40-147">[Locust.io](https://locust.io/) : Nicht begrenzt von Threads.</span><span class="sxs-lookup"><span data-stu-id="9bd40-147">[Locust.io](https://locust.io/) : Not bounded by threads.</span></span>

<a name="add"></a>
## <a name="additional-resources"></a><span data-ttu-id="9bd40-148">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="9bd40-148">Additional Resources</span></span>

<span data-ttu-id="9bd40-149">[Laden Sie die Test-blogreihe](https://blogs.msdn.microsoft.com/charles_sterling/2015/06/01/load-test-series-part-i-creating-web-performance-tests-for-a-load-test/) von Charles Sterling.</span><span class="sxs-lookup"><span data-stu-id="9bd40-149">[Load Test blog series](https://blogs.msdn.microsoft.com/charles_sterling/2015/06/01/load-test-series-part-i-creating-web-performance-tests-for-a-load-test/) by Charles Sterling.</span></span> <span data-ttu-id="9bd40-150">Vom, aber die meisten Themen sind immer noch relevant.</span><span class="sxs-lookup"><span data-stu-id="9bd40-150">Dated but most of the topics are still relevant.</span></span>
