---
title: ASP.NET Core laden/Belastungstests
author: Jeremy-Meng
description: Lernen Sie einige wichtige Tools und Ansätze für Auslastungstests und Belastungstests in ASP.NET Core-apps.
ms.author: riande
ms.custom: mvc
ms.date: 04/05/2019
uid: test/loadtests
ms.openlocfilehash: 3c21da6c799bc3080a1a16cb62ae4535b8890a1b
ms.sourcegitcommit: bee530454ae2b3c25dc7ffebf93536f479a14460
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67724491"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="4f57a-103">ASP.NET Core laden/Belastungstests</span><span class="sxs-lookup"><span data-stu-id="4f57a-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="4f57a-104">Auslastungstests und Belastungstests sind wichtig, sicherzustellen, dass eine Web-app leistungsfähig ist und skalierbar.</span><span class="sxs-lookup"><span data-stu-id="4f57a-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="4f57a-105">Ihre Ziele unterscheiden, obwohl sie häufig ähnliche Tests freigeben.</span><span class="sxs-lookup"><span data-stu-id="4f57a-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="4f57a-106">**Auslastungstests** &ndash; testen, ob die app auf eine angegebene Last von Benutzern für eine bestimmte Szenario behandeln kann, und dabei trotzdem das Antwortziel.</span><span class="sxs-lookup"><span data-stu-id="4f57a-106">**Load tests** &ndash; Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="4f57a-107">Die app wird unter normalen Bedingungen ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="4f57a-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="4f57a-108">**Belastungstests** &ndash; Stabilität der app zu testen, wenn unter extremen Bedingungen häufig für längere Zeit ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="4f57a-108">**Stress tests** &ndash; Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="4f57a-109">Die Tests hohe benutzerauslastung, entweder "Spitzen" oder "schrittweise zunehmenden Auslastung, platzieren Sie in der app, oder sie der app-Compute-Ressourcen beschränken.</span><span class="sxs-lookup"><span data-stu-id="4f57a-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="4f57a-110">Belastungstests bestimmen, ob eine app unter Last Wiederherstellen nach einem Fehler und erwarteten Verhalten zurück.</span><span class="sxs-lookup"><span data-stu-id="4f57a-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="4f57a-111">Bei hoher Belastung ist nicht die app unter normalen Bedingungen ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="4f57a-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="4f57a-112">Visual Studio-2019 ist die letzte Version von Visual Studio mit Funktionen für Auslastungstests.</span><span class="sxs-lookup"><span data-stu-id="4f57a-112">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="4f57a-113">Für Kunden Auslastungstesttools in der Zukunft empfehlen wir die anderen Tools wie Apache JMeter Akamai CloudTest und BlazeMeter.</span><span class="sxs-lookup"><span data-stu-id="4f57a-113">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="4f57a-114">Weitere Informationen finden Sie unter den [Anmerkungen zu Visual Studio 2019](/visualstudio/releases/2019/release-notes#test-tools).</span><span class="sxs-lookup"><span data-stu-id="4f57a-114">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes#test-tools).</span></span>

<span data-ttu-id="4f57a-115">Die Dienst-Auslastungstests in Azure DevOps endet in 2020.</span><span class="sxs-lookup"><span data-stu-id="4f57a-115">The load testing service in Azure DevOps is ending in 2020.</span></span> <span data-ttu-id="4f57a-116">Weitere Informationen finden Sie unter [cloudbasierte Auslastungstests Service Ende ihrer Lebensdauer](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span><span class="sxs-lookup"><span data-stu-id="4f57a-116">For more information, see [Cloud-based load testing service end of life](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="4f57a-117">Visual Studio Tools</span><span class="sxs-lookup"><span data-stu-id="4f57a-117">Visual Studio tools</span></span>

<span data-ttu-id="4f57a-118">Visual Studio ermöglicht das Erstellen, entwickeln und Debuggen von webleistungs-und Auslastungstest.</span><span class="sxs-lookup"><span data-stu-id="4f57a-118">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="4f57a-119">Eine Option ist verfügbar, um Tests zu erstellen, durch Aufzeichnen der Aktionen in einem Webbrowser.</span><span class="sxs-lookup"><span data-stu-id="4f57a-119">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="4f57a-120">Informationen zum Erstellen, konfigurieren und Ausführen eines Auslastungstests Projekte mit Visual Studio 2017 finden Sie unter [Schnellstart: Erstellen eines Auslastungstestprojekts](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span><span class="sxs-lookup"><span data-stu-id="4f57a-120">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span> <span data-ttu-id="4f57a-121">Weitere Informationen finden Sie unter den [Zusatzressourcen](#additional-resources) Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="4f57a-121">For more information, see the [Additional resources](#additional-resources) section.</span></span>

<span data-ttu-id="4f57a-122">Auslastungstests können konfiguriert werden, führen Sie lokal oder Ausführung in der Cloud mit Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="4f57a-122">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="azure-devops"></a><span data-ttu-id="4f57a-123">Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="4f57a-123">Azure DevOps</span></span>

<span data-ttu-id="4f57a-124">Auslastungstestläufe können gestartet werden, mithilfe der [Azure DevOps Testplänen](/azure/devops/test/load-test/index?view=vsts) Service.</span><span class="sxs-lookup"><span data-stu-id="4f57a-124">Load test runs can be started using the [Azure DevOps Test Plans](/azure/devops/test/load-test/index?view=vsts) service.</span></span>

![Azure-Angebotsseite der DevOps-Auslastungstests](./load-tests/_static/azure-devops-load-test.png)

<span data-ttu-id="4f57a-126">Der Dienst unterstützt die folgenden Test-Formate:</span><span class="sxs-lookup"><span data-stu-id="4f57a-126">The service supports the following test formats:</span></span>

* <span data-ttu-id="4f57a-127">Visual Studio &ndash; Webtest in Visual Studio erstellt.</span><span class="sxs-lookup"><span data-stu-id="4f57a-127">Visual Studio &ndash; Web test created in Visual Studio.</span></span>
* <span data-ttu-id="4f57a-128">HTTP-Archiv &ndash; erfassten HTTP-Datenverkehr in das Archiv wird wiedergegeben, während der Tests.</span><span class="sxs-lookup"><span data-stu-id="4f57a-128">HTTP Archive &ndash; Captured HTTP traffic inside archive is replayed during testing.</span></span>
* <span data-ttu-id="4f57a-129">[URL-basierte](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) &ndash; ermöglicht das Angeben von URLs, um den Test, Anforderungstypen, Header und Abfragezeichenfolgen zu laden.</span><span class="sxs-lookup"><span data-stu-id="4f57a-129">[URL-based](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) &ndash; Allows specifying URLs to load test, request types, headers, and query strings.</span></span> <span data-ttu-id="4f57a-130">Festlegen von Parametern, z. B. Dauer ausgeführt werden, kann Auslastungsmuster und Anzahl von Benutzern konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="4f57a-130">Run setting parameters such as duration, load pattern, and number of users can be configured.</span></span>
* <span data-ttu-id="4f57a-131">[Apache JMeter](https://jmeter.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="4f57a-131">[Apache JMeter](https://jmeter.apache.org/).</span></span>

## <a name="azure-portal"></a><span data-ttu-id="4f57a-132">Azure-Portal</span><span class="sxs-lookup"><span data-stu-id="4f57a-132">Azure portal</span></span>

<span data-ttu-id="4f57a-133">[Azure-Portal ermöglicht das Einrichten und Ausführen von Auslastungstests von Web-apps](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) direkt aus der **Leistung** App Service im Azure-Portal auf der Registerkarte.</span><span class="sxs-lookup"><span data-stu-id="4f57a-133">[Azure portal allows setting up and running load testing of web apps](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) directly from the **Performance** tab of the App Service in Azure portal.</span></span>

![Azure App Service im Azure-portal](./load-tests/_static/azure-appservice-perf-test.png)

<span data-ttu-id="4f57a-135">Der Test kann einen manuellen Test mit einer angegebenen URL oder eine Visual Studio-Webtest-Datei, die mehrere URLs zu testen sein.</span><span class="sxs-lookup"><span data-stu-id="4f57a-135">The test can be a manual test with a specified URL or a Visual Studio Web Test file, which can test multiple URLs.</span></span>

![Neue Leistung-Testseite im Azure-portal](./load-tests/_static/azure-appservice-perf-test-config.png)

<span data-ttu-id="4f57a-137">Am Ende des Tests zeigen die generierten Berichte die Leistungsmerkmale der app.</span><span class="sxs-lookup"><span data-stu-id="4f57a-137">At end of the test, generated reports show the performance characteristics of the app.</span></span> <span data-ttu-id="4f57a-138">Beispiel-Statistiken umfassen:</span><span class="sxs-lookup"><span data-stu-id="4f57a-138">Example statistics include:</span></span>

* <span data-ttu-id="4f57a-139">Durchschnittliche Antwortzeit</span><span class="sxs-lookup"><span data-stu-id="4f57a-139">Average response time</span></span>
* <span data-ttu-id="4f57a-140">Maximaler Durchsatz: Anforderungen pro Sekunde</span><span class="sxs-lookup"><span data-stu-id="4f57a-140">Max throughput: requests per second</span></span>
* <span data-ttu-id="4f57a-141">Ausführungsfehler in Prozent</span><span class="sxs-lookup"><span data-stu-id="4f57a-141">Failure percentage</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="4f57a-142">Drittanbieter-tools</span><span class="sxs-lookup"><span data-stu-id="4f57a-142">Third-party tools</span></span>

<span data-ttu-id="4f57a-143">Die folgende Liste enthält die Drittanbieter-Web-Leistungstools mit verschiedenen Featuresätze:</span><span class="sxs-lookup"><span data-stu-id="4f57a-143">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="4f57a-144">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="4f57a-144">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="4f57a-145">ApacheBench (ab)</span><span class="sxs-lookup"><span data-stu-id="4f57a-145">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="4f57a-146">Gatling</span><span class="sxs-lookup"><span data-stu-id="4f57a-146">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="4f57a-147">Locust</span><span class="sxs-lookup"><span data-stu-id="4f57a-147">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="4f57a-148">West Wind WebSurge</span><span class="sxs-lookup"><span data-stu-id="4f57a-148">West Wind WebSurge</span></span>](http://websurge.west-wind.com/)
* [<span data-ttu-id="4f57a-149">Netling</span><span class="sxs-lookup"><span data-stu-id="4f57a-149">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="4f57a-150">Vegeta</span><span class="sxs-lookup"><span data-stu-id="4f57a-150">Vegeta</span></span>](https://github.com/tsenart/vegeta)

## <a name="additional-resources"></a><span data-ttu-id="4f57a-151">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="4f57a-151">Additional resources</span></span>

* [<span data-ttu-id="4f57a-152">Laden Sie die Test-Blog-Reihe</span><span class="sxs-lookup"><span data-stu-id="4f57a-152">Load Test blog series</span></span>](https://blogs.msdn.microsoft.com/charles_sterling/2015/06/01/load-test-series-part-i-creating-web-performance-tests-for-a-load-test/)
