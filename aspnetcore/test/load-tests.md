---
title: ASP.NET Core laden/Belastungstests
author: Jeremy-Meng
description: Lernen Sie einige wichtige Tools und Ansätze für Auslastungstests und Belastungstests in ASP.NET Core-apps.
ms.author: riande
ms.custom: mvc
ms.date: 04/05/2019
uid: test/loadtests
ms.openlocfilehash: 0a8449ea2c9df0f2ac93058f03af0a1a2aa66508
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59068182"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="bcbfe-103">ASP.NET Core laden/Belastungstests</span><span class="sxs-lookup"><span data-stu-id="bcbfe-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="bcbfe-104">Auslastungstests und Belastungstests sind wichtig, sicherzustellen, dass eine Web-app leistungsfähig ist und skalierbar.</span><span class="sxs-lookup"><span data-stu-id="bcbfe-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="bcbfe-105">Ihre Ziele unterscheiden, obwohl sie häufig ähnliche Tests freigeben.</span><span class="sxs-lookup"><span data-stu-id="bcbfe-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="bcbfe-106">**Auslastungstests** &ndash; testen, ob die app auf eine angegebene Last von Benutzern für eine bestimmte Szenario behandeln kann, und dabei trotzdem das Antwortziel.</span><span class="sxs-lookup"><span data-stu-id="bcbfe-106">**Load tests** &ndash; Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="bcbfe-107">Die app wird unter normalen Bedingungen ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="bcbfe-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="bcbfe-108">**Belastungstests** &ndash; Stabilität der app zu testen, wenn unter extremen Bedingungen häufig für längere Zeit ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="bcbfe-108">**Stress tests** &ndash; Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="bcbfe-109">Die Tests hohe benutzerauslastung, entweder "Spitzen" oder "schrittweise zunehmenden Auslastung, platzieren Sie in der app, oder sie der app-Compute-Ressourcen beschränken.</span><span class="sxs-lookup"><span data-stu-id="bcbfe-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="bcbfe-110">Belastungstests bestimmen, ob eine app unter Last Wiederherstellen nach einem Fehler und erwarteten Verhalten zurück.</span><span class="sxs-lookup"><span data-stu-id="bcbfe-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="bcbfe-111">Bei hoher Belastung ist nicht die app unter normalen Bedingungen ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="bcbfe-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="bcbfe-112">Visual Studio-2019 ist die letzte Version von Visual Studio mit Funktionen für Auslastungstests.</span><span class="sxs-lookup"><span data-stu-id="bcbfe-112">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="bcbfe-113">Für Kunden Auslastungstesttools in der Zukunft empfehlen wir die anderen Tools wie Apache JMeter Akamai CloudTest und BlazeMeter.</span><span class="sxs-lookup"><span data-stu-id="bcbfe-113">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="bcbfe-114">Weitere Informationen finden Sie unter den [Anmerkungen zu Visual Studio 2019](/visualstudio/releases/2019/release-notes#test-tools).</span><span class="sxs-lookup"><span data-stu-id="bcbfe-114">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes#test-tools).</span></span>

<span data-ttu-id="bcbfe-115">Die Dienst-Auslastungstests in Azure DevOps endet in 2020.</span><span class="sxs-lookup"><span data-stu-id="bcbfe-115">The load testing service in Azure DevOps is ending in 2020.</span></span> <span data-ttu-id="bcbfe-116">Weitere Informationen finden Sie unter [cloudbasierte Auslastungstests Service Ende ihrer Lebensdauer](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span><span class="sxs-lookup"><span data-stu-id="bcbfe-116">For more information, see [Cloud-based load testing service end of life](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="bcbfe-117">Visual Studio Tools</span><span class="sxs-lookup"><span data-stu-id="bcbfe-117">Visual Studio tools</span></span>

<span data-ttu-id="bcbfe-118">Visual Studio ermöglicht das Erstellen, entwickeln und Debuggen von webleistungs-und Auslastungstest.</span><span class="sxs-lookup"><span data-stu-id="bcbfe-118">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="bcbfe-119">Eine Option ist verfügbar, um Tests zu erstellen, durch Aufzeichnen der Aktionen in einem Webbrowser.</span><span class="sxs-lookup"><span data-stu-id="bcbfe-119">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="bcbfe-120">Informationen zum Erstellen, konfigurieren und Ausführen eines Auslastungstests Projekte mit Visual Studio 2017 finden Sie unter [Schnellstart: Erstellen eines Auslastungstestprojekts](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span><span class="sxs-lookup"><span data-stu-id="bcbfe-120">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span> <span data-ttu-id="bcbfe-121">Weitere Informationen finden Sie unter den [Zusatzressourcen](#additional-resources) Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="bcbfe-121">For more information, see the [Additional resources](#additional-resources) section.</span></span>

<span data-ttu-id="bcbfe-122">Auslastungstests können konfiguriert werden, führen Sie lokal oder Ausführung in der Cloud mit Azure DevOps.</span><span class="sxs-lookup"><span data-stu-id="bcbfe-122">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="azure-devops"></a><span data-ttu-id="bcbfe-123">Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="bcbfe-123">Azure DevOps</span></span>

<span data-ttu-id="bcbfe-124">Auslastungstestläufe können gestartet werden, mithilfe der [Azure DevOps Testplänen](/azure/devops/test/load-test/index?view=vsts) Service.</span><span class="sxs-lookup"><span data-stu-id="bcbfe-124">Load test runs can be started using the [Azure DevOps Test Plans](/azure/devops/test/load-test/index?view=vsts) service.</span></span>

![Azure-Angebotsseite der DevOps-Auslastungstests](./load-tests/_static/azure-devops-load-test.png)

<span data-ttu-id="bcbfe-126">Der Dienst unterstützt die folgenden Test-Formate:</span><span class="sxs-lookup"><span data-stu-id="bcbfe-126">The service supports the following test formats:</span></span>

* <span data-ttu-id="bcbfe-127">Visual Studio &ndash; Webtest in Visual Studio erstellt.</span><span class="sxs-lookup"><span data-stu-id="bcbfe-127">Visual Studio &ndash; Web test created in Visual Studio.</span></span>
* <span data-ttu-id="bcbfe-128">HTTP-Archiv &ndash; erfassten HTTP-Datenverkehr in das Archiv wird wiedergegeben, während der Tests.</span><span class="sxs-lookup"><span data-stu-id="bcbfe-128">HTTP Archive &ndash; Captured HTTP traffic inside archive is replayed during testing.</span></span>
* <span data-ttu-id="bcbfe-129">[URL-basierte](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) &ndash; ermöglicht das Angeben von URLs, um den Test, Anforderungstypen, Header und Abfragezeichenfolgen zu laden.</span><span class="sxs-lookup"><span data-stu-id="bcbfe-129">[URL-based](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) &ndash; Allows specifying URLs to load test, request types, headers, and query strings.</span></span> <span data-ttu-id="bcbfe-130">Festlegen von Parametern, z. B. Dauer ausgeführt werden, kann Auslastungsmuster und Anzahl von Benutzern konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="bcbfe-130">Run setting parameters such as duration, load pattern, and number of users can be configured.</span></span>
* <span data-ttu-id="bcbfe-131">[Apache JMeter](https://jmeter.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="bcbfe-131">[Apache JMeter](https://jmeter.apache.org/).</span></span>

## <a name="azure-portal"></a><span data-ttu-id="bcbfe-132">Azure-Portal</span><span class="sxs-lookup"><span data-stu-id="bcbfe-132">Azure portal</span></span>

<span data-ttu-id="bcbfe-133">[Azure-Portal ermöglicht das Einrichten und Ausführen von Auslastungstests von Web-apps](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) direkt aus der **Leistung** App Service im Azure-Portal auf der Registerkarte.</span><span class="sxs-lookup"><span data-stu-id="bcbfe-133">[Azure portal allows setting up and running load testing of web apps](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) directly from the **Performance** tab of the App Service in Azure portal.</span></span>

![Azure App Service im Azure-portal](./load-tests/_static/azure-appservice-perf-test.png)

<span data-ttu-id="bcbfe-135">Der Test kann einen manuellen Test mit einer angegebenen URL oder eine Visual Studio-Webtest-Datei, die mehrere URLs zu testen sein.</span><span class="sxs-lookup"><span data-stu-id="bcbfe-135">The test can be a manual test with a specified URL or a Visual Studio Web Test file, which can test multiple URLs.</span></span>

![Neue Leistung-Testseite im Azure-portal](./load-tests/_static/azure-appservice-perf-test-config.png)

<span data-ttu-id="bcbfe-137">Am Ende des Tests zeigen die generierten Berichte die Leistungsmerkmale der app.</span><span class="sxs-lookup"><span data-stu-id="bcbfe-137">At end of the test, generated reports show the performance characteristics of the app.</span></span> <span data-ttu-id="bcbfe-138">Beispiel-Statistiken umfassen:</span><span class="sxs-lookup"><span data-stu-id="bcbfe-138">Example statistics include:</span></span>

* <span data-ttu-id="bcbfe-139">Durchschnittliche Antwortzeit</span><span class="sxs-lookup"><span data-stu-id="bcbfe-139">Average response time</span></span>
* <span data-ttu-id="bcbfe-140">Maximaler Durchsatz: Anforderungen pro Sekunde</span><span class="sxs-lookup"><span data-stu-id="bcbfe-140">Max throughput: requests per second</span></span>
* <span data-ttu-id="bcbfe-141">Ausführungsfehler in Prozent</span><span class="sxs-lookup"><span data-stu-id="bcbfe-141">Failure percentage</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="bcbfe-142">Drittanbieter-tools</span><span class="sxs-lookup"><span data-stu-id="bcbfe-142">Third-party tools</span></span>

<span data-ttu-id="bcbfe-143">Die folgende Liste enthält die Drittanbieter-Web-Leistungstools mit verschiedenen Featuresätze:</span><span class="sxs-lookup"><span data-stu-id="bcbfe-143">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="bcbfe-144">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="bcbfe-144">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="bcbfe-145">ApacheBench (ab)</span><span class="sxs-lookup"><span data-stu-id="bcbfe-145">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="bcbfe-146">Gatling</span><span class="sxs-lookup"><span data-stu-id="bcbfe-146">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="bcbfe-147">Locust</span><span class="sxs-lookup"><span data-stu-id="bcbfe-147">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="bcbfe-148">West Wind WebSurge</span><span class="sxs-lookup"><span data-stu-id="bcbfe-148">West Wind WebSurge</span></span>](http://websurge.west-wind.com/)
* [<span data-ttu-id="bcbfe-149">Netling</span><span class="sxs-lookup"><span data-stu-id="bcbfe-149">Netling</span></span>](https://github.com/hallatore/Netling)

## <a name="additional-resources"></a><span data-ttu-id="bcbfe-150">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="bcbfe-150">Additional resources</span></span>

* [<span data-ttu-id="bcbfe-151">Laden Sie die Test-Blog-Reihe</span><span class="sxs-lookup"><span data-stu-id="bcbfe-151">Load Test blog series</span></span>](https://blogs.msdn.microsoft.com/charles_sterling/2015/06/01/load-test-series-part-i-creating-web-performance-tests-for-a-load-test/)
