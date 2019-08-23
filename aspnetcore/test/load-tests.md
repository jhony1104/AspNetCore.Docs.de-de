---
title: ASP.net Core laden/Belastungstest
author: Jeremy-Meng
description: Erfahren Sie mehr über verschiedene wichtige Tools und Ansätze für Auslastungs Tests und Belastungstests ASP.net Core apps.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: test/loadtests
ms.openlocfilehash: 7a9dfc1fedf747ab26daa573b61ed01c31709058
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975245"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="fdbdf-103">ASP.net Core laden/Belastungstest</span><span class="sxs-lookup"><span data-stu-id="fdbdf-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="fdbdf-104">Auslastungs Tests und Belastungstests sind wichtig, um sicherzustellen, dass eine Web-App Performance und skalierbar ist.</span><span class="sxs-lookup"><span data-stu-id="fdbdf-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="fdbdf-105">Ihre Ziele sind unterschiedlich, obwohl Sie häufig ähnliche Tests gemeinsam nutzen.</span><span class="sxs-lookup"><span data-stu-id="fdbdf-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="fdbdf-106">**Auslastungs Tests** &ndash; Testen Sie, ob die APP eine bestimmte Last von Benutzern für ein bestimmtes Szenario verarbeiten kann, während das Antwort Ziel weiterhin erfüllt wird.</span><span class="sxs-lookup"><span data-stu-id="fdbdf-106">**Load tests** &ndash; Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="fdbdf-107">Die APP wird unter normalen Bedingungen ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="fdbdf-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="fdbdf-108">**Belastungstests** &ndash; Testen Sie die APP-Stabilität, wenn Sie unter Extrembedingungen ausgeführt wird, häufig für längere Zeit.</span><span class="sxs-lookup"><span data-stu-id="fdbdf-108">**Stress tests** &ndash; Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="fdbdf-109">Die Tests platzieren hohe Benutzer Auslastung, entweder Spitzen oder allmählich höhere Auslastung, in der APP oder beschränken die computingressourcen der app.</span><span class="sxs-lookup"><span data-stu-id="fdbdf-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="fdbdf-110">Belastungstests bestimmen, ob eine zu Belastungs Ende App nach einem Fehler wieder hergestellt werden kann und ordnungsgemäß zum erwarteten Verhalten zurückkehren kann.</span><span class="sxs-lookup"><span data-stu-id="fdbdf-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="fdbdf-111">Unter Belastung wird die APP nicht unter normalen Bedingungen ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="fdbdf-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="fdbdf-112">Visual Studio 2019 ist die letzte Version von Visual Studio mit Auslastungs Test Features.</span><span class="sxs-lookup"><span data-stu-id="fdbdf-112">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="fdbdf-113">Für Kunden, die zukünftig Auslastungs TestTools benötigen, empfehlen wir alternative Tools, wie z. b. Apache JMeter, Akamai cloudtest und blazemeter.</span><span class="sxs-lookup"><span data-stu-id="fdbdf-113">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="fdbdf-114">Weitere Informationen finden Sie in den [Anmerkungen zu dieser Version von Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span><span class="sxs-lookup"><span data-stu-id="fdbdf-114">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

<span data-ttu-id="fdbdf-115">Der Auslastungs Test Dienst in Azure devops endet in 2020.</span><span class="sxs-lookup"><span data-stu-id="fdbdf-115">The load testing service in Azure DevOps is ending in 2020.</span></span> <span data-ttu-id="fdbdf-116">Weitere Informationen finden Sie unter [Dienst Ende des cloudbasierten Auslastungs Tests](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span><span class="sxs-lookup"><span data-stu-id="fdbdf-116">For more information, see [Cloud-based load testing service end of life](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="fdbdf-117">Visual Studio Tools</span><span class="sxs-lookup"><span data-stu-id="fdbdf-117">Visual Studio tools</span></span>

<span data-ttu-id="fdbdf-118">Visual Studio ermöglicht Benutzern das Erstellen, entwickeln und Debuggen von webleistungs-und Auslastungs Tests.</span><span class="sxs-lookup"><span data-stu-id="fdbdf-118">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="fdbdf-119">Eine Option ist verfügbar, um Tests zu erstellen, indem Aktionen in einem Webbrowser aufgezeichnet werden.</span><span class="sxs-lookup"><span data-stu-id="fdbdf-119">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="fdbdf-120">Informationen zum Erstellen, konfigurieren und Ausführen eines Auslastungs Testprojekts mit Visual Studio 2017 finden [Sie unter Schnellstart: Erstellen eines Auslastungstestprojekts](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span><span class="sxs-lookup"><span data-stu-id="fdbdf-120">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="fdbdf-121">Auslastungs Tests können so konfiguriert werden, dass Sie lokal oder mithilfe von Azure devops in der Cloud ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="fdbdf-121">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="azure-devops"></a><span data-ttu-id="fdbdf-122">Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="fdbdf-122">Azure DevOps</span></span>

<span data-ttu-id="fdbdf-123">Auslastungs Testläufe können mithilfe des [Azure devops-Test Plans](/azure/devops/test/load-test/index?view=vsts) Dienstanbieter gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="fdbdf-123">Load test runs can be started using the [Azure DevOps Test Plans](/azure/devops/test/load-test/index?view=vsts) service.</span></span>

![Startseite für Azure devops-Auslastungs Tests](./load-tests/_static/azure-devops-load-test.png)

<span data-ttu-id="fdbdf-125">Der Dienst unterstützt die folgenden Testformate:</span><span class="sxs-lookup"><span data-stu-id="fdbdf-125">The service supports the following test formats:</span></span>

* <span data-ttu-id="fdbdf-126">Visual Studio &ndash; -Webtest in Visual Studio erstellt.</span><span class="sxs-lookup"><span data-stu-id="fdbdf-126">Visual Studio &ndash; Web test created in Visual Studio.</span></span>
* <span data-ttu-id="fdbdf-127">Http- &ndash; Archiv erfasste HTTP-Datenverkehr innerhalb des Archivs wird während des Tests wiedergegeben.</span><span class="sxs-lookup"><span data-stu-id="fdbdf-127">HTTP Archive &ndash; Captured HTTP traffic inside archive is replayed during testing.</span></span>
* <span data-ttu-id="fdbdf-128">[URL-basiert](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) &ndash; Ermöglicht das Angeben von URLs für Auslastungs Tests, Anforderungs Typen, Header und Abfrage Zeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="fdbdf-128">[URL-based](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) &ndash; Allows specifying URLs to load test, request types, headers, and query strings.</span></span> <span data-ttu-id="fdbdf-129">Es können Lauf Zeit Einstellungsparameter wie Dauer, Auslastungs Muster und Anzahl von Benutzern konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="fdbdf-129">Run setting parameters such as duration, load pattern, and number of users can be configured.</span></span>
* <span data-ttu-id="fdbdf-130">[Apache JMeter](https://jmeter.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="fdbdf-130">[Apache JMeter](https://jmeter.apache.org/).</span></span>

## <a name="azure-portal"></a><span data-ttu-id="fdbdf-131">Azure-Portal</span><span class="sxs-lookup"><span data-stu-id="fdbdf-131">Azure portal</span></span>

<span data-ttu-id="fdbdf-132">[Azure-Portal ermöglicht das Einrichten und Ausführen von Auslastungs Tests von Web-Apps](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) direkt über die Registerkarte **Leistung** der APP Service in Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="fdbdf-132">[Azure portal allows setting up and running load testing of web apps](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) directly from the **Performance** tab of the App Service in Azure portal.</span></span>

![Azure App Service in Azure-Portal](./load-tests/_static/azure-appservice-perf-test.png)

<span data-ttu-id="fdbdf-134">Der Test kann ein manueller Test mit einer angegebenen URL oder einer Visual Studio-Webtest Datei sein, die mehrere URLs testen kann.</span><span class="sxs-lookup"><span data-stu-id="fdbdf-134">The test can be a manual test with a specified URL or a Visual Studio Web Test file, which can test multiple URLs.</span></span>

![Neue Seite "Leistungs Test" auf Azure-Portal](./load-tests/_static/azure-appservice-perf-test-config.png)

<span data-ttu-id="fdbdf-136">Am Ende des Tests zeigen generierte Berichte die Leistungsmerkmale der APP an.</span><span class="sxs-lookup"><span data-stu-id="fdbdf-136">At end of the test, generated reports show the performance characteristics of the app.</span></span> <span data-ttu-id="fdbdf-137">Beispiel Statistiken:</span><span class="sxs-lookup"><span data-stu-id="fdbdf-137">Example statistics include:</span></span>

* <span data-ttu-id="fdbdf-138">Durchschnittliche Reaktionszeit</span><span class="sxs-lookup"><span data-stu-id="fdbdf-138">Average response time</span></span>
* <span data-ttu-id="fdbdf-139">Maximaler Durchsatz: Anforderungen pro Sekunde</span><span class="sxs-lookup"><span data-stu-id="fdbdf-139">Max throughput: requests per second</span></span>
* <span data-ttu-id="fdbdf-140">Fehler Prozentsatz</span><span class="sxs-lookup"><span data-stu-id="fdbdf-140">Failure percentage</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="fdbdf-141">Drittanbieter Tools</span><span class="sxs-lookup"><span data-stu-id="fdbdf-141">Third-party tools</span></span>

<span data-ttu-id="fdbdf-142">Die folgende Liste enthält webleistungs Tools von Drittanbietern mit verschiedenen Funktionsgruppen:</span><span class="sxs-lookup"><span data-stu-id="fdbdf-142">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="fdbdf-143">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="fdbdf-143">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="fdbdf-144">Apachebench (ab)</span><span class="sxs-lookup"><span data-stu-id="fdbdf-144">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="fdbdf-145">Gator</span><span class="sxs-lookup"><span data-stu-id="fdbdf-145">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="fdbdf-146">Locust</span><span class="sxs-lookup"><span data-stu-id="fdbdf-146">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="fdbdf-147">Webanstieg für Westwind</span><span class="sxs-lookup"><span data-stu-id="fdbdf-147">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="fdbdf-148">Wird netult</span><span class="sxs-lookup"><span data-stu-id="fdbdf-148">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="fdbdf-149">Vegeta</span><span class="sxs-lookup"><span data-stu-id="fdbdf-149">Vegeta</span></span>](https://github.com/tsenart/vegeta)
