---
title: ASP.NET Core laden/Belastungstests
author: Jeremy-Meng
description: Beschreibt verschiedene wichtige Tools und Ansätze für Auslastungstests und Belastungstests in ASP.NET Core-apps.
ms.author: riande
ms.custom: mvc
ms.date: 01/04/2019
uid: test/loadtests
ms.openlocfilehash: 08c4251059b7d9f4549ad710054d8299c4943465
ms.sourcegitcommit: 7d6019f762fc5b8cbedcd69801e8310f51a17c18
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58419380"
---
# <a name="load-and-stress-testing-aspnet-core"></a><span data-ttu-id="b14ef-103">Laden und Belastungstests Testen von ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b14ef-103">Load and stress testing ASP.NET Core</span></span>

<span data-ttu-id="b14ef-104">Auslastungstests und Belastungstests sind wichtig, sicherzustellen, dass eine Web-app leistungsfähig ist und skalierbar.</span><span class="sxs-lookup"><span data-stu-id="b14ef-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="b14ef-105">Ihre Ziele unterscheiden, obwohl sie häufig ähnliche Tests freigeben.</span><span class="sxs-lookup"><span data-stu-id="b14ef-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="b14ef-106">**Auslastungstests**: Testet, ob die app auf eine angegebene Last von Benutzern für eine bestimmte Szenario behandeln kann, und dabei trotzdem das Antwortziel.</span><span class="sxs-lookup"><span data-stu-id="b14ef-106">**Load tests**: Tests whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="b14ef-107">Die app wird unter normalen Bedingungen ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="b14ef-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="b14ef-108">**Belastungstests**: Tests app Stabilität bei der Ausführung unter extremen Bedingungen und häufig eine längere Zeit:</span><span class="sxs-lookup"><span data-stu-id="b14ef-108">**Stress tests**: Tests app stability when running under extreme conditions and often a long period of time:</span></span>

* <span data-ttu-id="b14ef-109">Hohe benutzerauslastung – Spitzen oder allmählich.</span><span class="sxs-lookup"><span data-stu-id="b14ef-109">High user load – either spikes or gradually increasing.</span></span>
* <span data-ttu-id="b14ef-110">Compute-Ressourcen beschränkt.</span><span class="sxs-lookup"><span data-stu-id="b14ef-110">Limited computing resources.</span></span>

<span data-ttu-id="b14ef-111">Bei hoher Belastung kann die app Wiederherstellen nach einem Fehler und zurück zum erwarteten Verhalten?</span><span class="sxs-lookup"><span data-stu-id="b14ef-111">Under stress, can the app recover from failure and gracefully return to expected behavior?</span></span> <span data-ttu-id="b14ef-112">Bei hoher Belastung die app ist *nicht* unter normalen Bedingungen ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="b14ef-112">Under stress, the app is *not* run under normal conditions.</span></span>

<span data-ttu-id="b14ef-113">Visual Studio 2019 ist die letzte Version von Visual Studio mit Auslastungstestfunktionen.</span><span class="sxs-lookup"><span data-stu-id="b14ef-113">Visual Studio 2019 will be the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="b14ef-114">Kunden, die Auslastungstesttools benötigen, empfehlen wir, alternative Auslastungstesttools wie Apache JMeter, Akamai CloudTest oder Blazemeter zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="b14ef-114">For customers requiring load testing tools, we recommend using alternate load testing tools such as Apache JMeter, Akamai CloudTest, Blazemeter.</span></span> <span data-ttu-id="b14ef-115">Weitere Informationen finden Sie unter den [2019 Preview Release Anmerkungen zur Visual Studio](/visualstudio/releases/2019/release-notes-preview#test-tools).</span><span class="sxs-lookup"><span data-stu-id="b14ef-115">For more information, see the [Visual Studio 2019 Preview Release Notes](/visualstudio/releases/2019/release-notes-preview#test-tools).</span></span>

<span data-ttu-id="b14ef-116">Die Dienst-Auslastungstests in Azure DevOps endet in 2020.</span><span class="sxs-lookup"><span data-stu-id="b14ef-116">The load testing service in Azure DevOps is ending in 2020.</span></span> <span data-ttu-id="b14ef-117">Weitere Informationen finden Sie unter [cloudbasierte Auslastungstests Service Ende ihrer Lebensdauer](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span><span class="sxs-lookup"><span data-stu-id="b14ef-117">For more information see [Cloud-based load testing service end of life](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="b14ef-118">Visual Studio Tools</span><span class="sxs-lookup"><span data-stu-id="b14ef-118">Visual Studio Tools</span></span>

<span data-ttu-id="b14ef-119">Visual Studio ermöglicht das Erstellen, entwickeln und Debuggen von webleistungs-und Auslastungstest.</span><span class="sxs-lookup"><span data-stu-id="b14ef-119">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="b14ef-120">Eine Option ist verfügbar, um Tests zu erstellen, durch Aufzeichnen der Aktionen im Webbrowser.</span><span class="sxs-lookup"><span data-stu-id="b14ef-120">An option is available to create tests by recording actions in web browser.</span></span>

<span data-ttu-id="b14ef-121">[Schnellstart: Erstellen ein auslastungstestprojekts](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017) erstellen, konfigurieren und Ausführen eines Auslastungstests mit Visual Studio 2017 Projekte veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="b14ef-121">[Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017) shows how to create, configure, and run a load test projects using Visual Studio 2017.</span></span>

<span data-ttu-id="b14ef-122">Weitere Informationen finden Sie unter [Zusätzliche Ressourcen](#add).</span><span class="sxs-lookup"><span data-stu-id="b14ef-122">See [Additional Resources](#add) for more information.</span></span>

<span data-ttu-id="b14ef-123">Auslastungstests können in einer lokalen ausgeführt oder führen Sie in der Cloud mit Azure DevOps konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="b14ef-123">Load tests can be configured to run in on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="azure-devops"></a><span data-ttu-id="b14ef-124">Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="b14ef-124">Azure DevOps</span></span>

<span data-ttu-id="b14ef-125">Auslastungstestläufe können gestartet werden, mithilfe der [Azure DevOps Testplänen](/azure/devops/test/load-test/index?view=vsts) Service.</span><span class="sxs-lookup"><span data-stu-id="b14ef-125">Load test runs can be started using the [Azure DevOps Test Plans](/azure/devops/test/load-test/index?view=vsts) service.</span></span>

![Azure-Angebotsseite der DevOps-Auslastungstests](./load-tests/_static/azure-devops-load-test.png)

<span data-ttu-id="b14ef-127">Der Dienst unterstützt die folgenden Typen von Test-Format an:</span><span class="sxs-lookup"><span data-stu-id="b14ef-127">The service supports the following types of test format:</span></span>

* <span data-ttu-id="b14ef-128">Visual Studio-Test – Webtest in Visual Studio erstellt.</span><span class="sxs-lookup"><span data-stu-id="b14ef-128">Visual Studio test – web test created in Visual Studio.</span></span>
* <span data-ttu-id="b14ef-129">HTTP-Archiv-basierten Test – erfassten HTTP-Datenverkehr in das Archiv ist während der Tests wiedergegeben.</span><span class="sxs-lookup"><span data-stu-id="b14ef-129">HTTP Archive-based test – captured HTTP traffic inside archive is replayed during testing.</span></span>
* <span data-ttu-id="b14ef-130">[URL-basierter Test](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) – ermöglicht das Angeben von URLs, um den Test, Anforderungstypen, Header und Abfragezeichenfolgen zu laden.</span><span class="sxs-lookup"><span data-stu-id="b14ef-130">[URL-based test](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) – allows specifying URLs to load test, request types, headers, and query strings.</span></span> <span data-ttu-id="b14ef-131">Festlegen von Parametern, z. B. Dauer ausgeführt werden, kann eine Auslastungsmuster, Anzahl der Benutzer usw., konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="b14ef-131">Run setting parameters such as duration, load pattern, number of users, etc., can be configured.</span></span>
* <span data-ttu-id="b14ef-132">[Apache JMeter](https://jmeter.apache.org/) test.</span><span class="sxs-lookup"><span data-stu-id="b14ef-132">[Apache JMeter](https://jmeter.apache.org/) test.</span></span>

## <a name="azure-portal"></a><span data-ttu-id="b14ef-133">Azure-Portal</span><span class="sxs-lookup"><span data-stu-id="b14ef-133">Azure portal</span></span>

<span data-ttu-id="b14ef-134">[Azure-Portal ermöglicht das Einrichten und Ausführen von Web-Apps, die Auslastungstests](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) direkt über die Registerkarte "Leistung" des App Service im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="b14ef-134">[Azure portal allows setting up and running load testing of Web Apps,](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) directly from the Performance tab of the App Service in Azure portal.</span></span>

![Azure App Service in Azure-Portal](./load-tests/_static/azure-appservice-perf-test.png)

<span data-ttu-id="b14ef-136">Der Test kann einen manuellen Test mit einer angegebenen URL oder eine Visual Studio-Webtest-Datei, die mehrere URLs zu testen sein.</span><span class="sxs-lookup"><span data-stu-id="b14ef-136">The test can be a manual test with a specified URL, or a Visual Studio Web Test file, which can test multiple URLs.</span></span>

![Neue Leistung-Testseite im Azure-Portal](./load-tests/_static/azure-appservice-perf-test-config.png)

<span data-ttu-id="b14ef-138">Am Ende des Tests werden Berichte generiert, um die Leistungsmerkmale der app anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="b14ef-138">At end of the test, reports are generated to show the performance characteristics of the app.</span></span> <span data-ttu-id="b14ef-139">Beispiel-Statistiken umfassen:</span><span class="sxs-lookup"><span data-stu-id="b14ef-139">Example statistics include:</span></span>

* <span data-ttu-id="b14ef-140">Durchschnittliche Antwortzeit</span><span class="sxs-lookup"><span data-stu-id="b14ef-140">Average response time</span></span>
* <span data-ttu-id="b14ef-141">Maximaler Durchsatz: Anforderungen pro Sekunde</span><span class="sxs-lookup"><span data-stu-id="b14ef-141">Max throughput: requests per second</span></span>
* <span data-ttu-id="b14ef-142">Ausführungsfehler in Prozent</span><span class="sxs-lookup"><span data-stu-id="b14ef-142">Failure percentage</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="b14ef-143">Drittanbieter-Tools</span><span class="sxs-lookup"><span data-stu-id="b14ef-143">Third-party Tools</span></span>

<span data-ttu-id="b14ef-144">Die folgende Liste enthält die Drittanbieter-Web-Leistungstools mit verschiedenen Featuresätze:</span><span class="sxs-lookup"><span data-stu-id="b14ef-144">The following list contains third-party web performance tools with various feature sets:</span></span>

* <span data-ttu-id="b14ef-145">[Apache JMeter](https://jmeter.apache.org/) : Vollständige ausgewählte Sammlung von Testtools laden.</span><span class="sxs-lookup"><span data-stu-id="b14ef-145">[Apache JMeter](https://jmeter.apache.org/) : Full featured suite of load testing tools.</span></span> <span data-ttu-id="b14ef-146">Threadgebunden: benötigen Sie einen Thread pro Benutzer.</span><span class="sxs-lookup"><span data-stu-id="b14ef-146">Thread-bound: need one thread per user.</span></span>
* [<span data-ttu-id="b14ef-147">Ab – Apache HTTP Server Benchmark tool</span><span class="sxs-lookup"><span data-stu-id="b14ef-147">ab - Apache HTTP server benchmarking tool</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* <span data-ttu-id="b14ef-148">[Gatling](https://gatling.io/) : Desktop-Tool mit einer grafischen Benutzeroberfläche und Test Recorder.</span><span class="sxs-lookup"><span data-stu-id="b14ef-148">[Gatling](https://gatling.io/) : Desktop tool with a GUI and test recorders.</span></span> <span data-ttu-id="b14ef-149">Bieten eine bessere Leistung als JMeter.</span><span class="sxs-lookup"><span data-stu-id="b14ef-149">More performant than JMeter.</span></span>
* <span data-ttu-id="b14ef-150">[Locust.io](https://locust.io/) : Nicht begrenzt von Threads.</span><span class="sxs-lookup"><span data-stu-id="b14ef-150">[Locust.io](https://locust.io/) : Not bounded by threads.</span></span>

<a name="add"></a>

## <a name="additional-resources"></a><span data-ttu-id="b14ef-151">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b14ef-151">Additional Resources</span></span>

<span data-ttu-id="b14ef-152">[Laden Sie die Test-blogreihe](https://blogs.msdn.microsoft.com/charles_sterling/2015/06/01/load-test-series-part-i-creating-web-performance-tests-for-a-load-test/) von Charles Sterling.</span><span class="sxs-lookup"><span data-stu-id="b14ef-152">[Load Test blog series](https://blogs.msdn.microsoft.com/charles_sterling/2015/06/01/load-test-series-part-i-creating-web-performance-tests-for-a-load-test/) by Charles Sterling.</span></span> <span data-ttu-id="b14ef-153">Vom, aber die meisten Themen sind immer noch relevant.</span><span class="sxs-lookup"><span data-stu-id="b14ef-153">Dated but most of the topics are still relevant.</span></span>
