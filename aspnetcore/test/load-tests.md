---
title: ASP.net Core laden/Belastungstest
author: Jeremy-Meng
description: Erfahren Sie mehr über verschiedene wichtige Tools und Ansätze für Auslastungs Tests und Belastungstests ASP.net Core apps.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: test/loadtests
ms.openlocfilehash: edaa9e873e8e489f0c560c1736f81358ca1720d0
ms.sourcegitcommit: f40c9311058c9b1add4ec043ddc5629384af6c56
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74289025"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="40be0-103">ASP.net Core laden/Belastungstest</span><span class="sxs-lookup"><span data-stu-id="40be0-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="40be0-104">Auslastungs Tests und Belastungstests sind wichtig, um sicherzustellen, dass eine Web-App Performance und skalierbar ist.</span><span class="sxs-lookup"><span data-stu-id="40be0-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="40be0-105">Ihre Ziele sind unterschiedlich, obwohl Sie häufig ähnliche Tests gemeinsam nutzen.</span><span class="sxs-lookup"><span data-stu-id="40be0-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="40be0-106">**Auslastungs Tests** &ndash; testen, ob die APP eine bestimmte Last von Benutzern für ein bestimmtes Szenario verarbeiten kann, während gleichzeitig das Antwort Ziel erfüllt ist.</span><span class="sxs-lookup"><span data-stu-id="40be0-106">**Load tests** &ndash; Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="40be0-107">Die APP wird unter normalen Bedingungen ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="40be0-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="40be0-108">**Belastungstests** &ndash; Test-App-Stabilität, wenn Sie unter Extrembedingungen ausgeführt wird, häufig für einen längeren Zeitraum.</span><span class="sxs-lookup"><span data-stu-id="40be0-108">**Stress tests** &ndash; Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="40be0-109">Die Tests platzieren hohe Benutzer Auslastung, entweder Spitzen oder allmählich höhere Auslastung, in der APP oder beschränken die computingressourcen der app.</span><span class="sxs-lookup"><span data-stu-id="40be0-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="40be0-110">Belastungstests bestimmen, ob eine zu Belastungs Ende App nach einem Fehler wieder hergestellt werden kann und ordnungsgemäß zum erwarteten Verhalten zurückkehren kann.</span><span class="sxs-lookup"><span data-stu-id="40be0-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="40be0-111">Unter Belastung wird die APP nicht unter normalen Bedingungen ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="40be0-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="40be0-112">Visual Studio 2019 ist die letzte Version von Visual Studio mit Auslastungs Test Features.</span><span class="sxs-lookup"><span data-stu-id="40be0-112">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="40be0-113">Für Kunden, die zukünftig Auslastungs TestTools benötigen, empfehlen wir alternative Tools, wie z. b. Apache JMeter, Akamai cloudtest und blazemeter.</span><span class="sxs-lookup"><span data-stu-id="40be0-113">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="40be0-114">Weitere Informationen finden Sie in den [Anmerkungen zu dieser Version von Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span><span class="sxs-lookup"><span data-stu-id="40be0-114">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="40be0-115">Visual Studio Tools</span><span class="sxs-lookup"><span data-stu-id="40be0-115">Visual Studio tools</span></span>

<span data-ttu-id="40be0-116">Visual Studio ermöglicht Benutzern das Erstellen, entwickeln und Debuggen von webleistungs-und Auslastungs Tests.</span><span class="sxs-lookup"><span data-stu-id="40be0-116">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="40be0-117">Eine Option ist verfügbar, um Tests zu erstellen, indem Aktionen in einem Webbrowser aufgezeichnet werden.</span><span class="sxs-lookup"><span data-stu-id="40be0-117">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="40be0-118">Informationen zum Erstellen, konfigurieren und Ausführen von Auslastungs Testprojekten mit Visual Studio 2017 finden Sie unter [Schnellstart: Erstellen eines Auslastungs Testprojekts](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span><span class="sxs-lookup"><span data-stu-id="40be0-118">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="40be0-119">Auslastungs Tests können so konfiguriert werden, dass Sie lokal oder mithilfe von Azure devops in der Cloud ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="40be0-119">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="40be0-120">Drittanbieter Tools</span><span class="sxs-lookup"><span data-stu-id="40be0-120">Third-party tools</span></span>

<span data-ttu-id="40be0-121">Die folgende Liste enthält webleistungs Tools von Drittanbietern mit verschiedenen Funktionsgruppen:</span><span class="sxs-lookup"><span data-stu-id="40be0-121">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="40be0-122">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="40be0-122">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="40be0-123">Apachebench (ab)</span><span class="sxs-lookup"><span data-stu-id="40be0-123">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="40be0-124">Gator</span><span class="sxs-lookup"><span data-stu-id="40be0-124">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="40be0-125">Locust</span><span class="sxs-lookup"><span data-stu-id="40be0-125">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="40be0-126">Webanstieg für Westwind</span><span class="sxs-lookup"><span data-stu-id="40be0-126">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="40be0-127">Wird netult</span><span class="sxs-lookup"><span data-stu-id="40be0-127">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="40be0-128">Vegeta</span><span class="sxs-lookup"><span data-stu-id="40be0-128">Vegeta</span></span>](https://github.com/tsenart/vegeta)
