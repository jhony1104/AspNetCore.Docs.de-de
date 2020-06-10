---
title: 'title: author: description: ms.author: ms.custom: ms.date: no-loc:'
author: Jeremy-Meng
description: "'Blazor'"
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/loadtests
ms.openlocfilehash: f0c930a7689c7288a58cfc3ca630d3cd324bf2cd
ms.sourcegitcommit: 58722eb309767e462bdbf3082bd38737a4ef168f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84106805"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="a20c4-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a20c4-103">'Identity'</span></span>

<span data-ttu-id="a20c4-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a20c4-104">'Let's Encrypt'</span></span> <span data-ttu-id="a20c4-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a20c4-105">'Razor'</span></span>

<span data-ttu-id="a20c4-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="a20c4-106">'SignalR' uid:</span></span> <span data-ttu-id="a20c4-107">Auslastungs-/Belastungstests in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a20c4-107">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="a20c4-108">Auslastungstests und Belastungstests sind wichtig, um sicherzustellen, dass eine Web-App leistungsfähig bleibt und skalierbar ist.</span><span class="sxs-lookup"><span data-stu-id="a20c4-108">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="a20c4-109">Ihre Ziele unterscheiden sich, obwohl oftmals ähnliche Tests verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="a20c4-109">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="a20c4-110">**Auslastungstests:** Hier wird getestet, ob die App eine bestimmte Benutzerauslastung für ein bestimmtes Szenario verarbeiten kann und dabei weiterhin reaktionsfähig bleibt.</span><span class="sxs-lookup"><span data-stu-id="a20c4-110">**Load tests**: Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="a20c4-111">Die App wird dabei unter normalen Umständen ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="a20c4-111">The app is run under normal conditions.</span></span>

<span data-ttu-id="a20c4-112">**Belastungstests:** Hier wird die Stabilität der App getestet, wenn sie unter extremen Bedingungen ausgeführt wird, oft auch für einen längeren Zeitraum.</span><span class="sxs-lookup"><span data-stu-id="a20c4-112">**Stress tests**: Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="a20c4-113">Beim Test wird die App einer hohen Benutzerauslastung ausgesetzt, entweder mit Spitzenwerten oder einer schrittweise ansteigenden Auslastung. Alternativ können die Computingressourcen der App eingeschränkt werden.</span><span class="sxs-lookup"><span data-stu-id="a20c4-113">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span> <span data-ttu-id="a20c4-114">Bei Belastungstests wird untersucht, ob sich eine App unter Belastung nach einem Fehler erholen und ordnungsgemäß zum erwarteten Verhalten zurückkehren kann.</span><span class="sxs-lookup"><span data-stu-id="a20c4-114">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="a20c4-115">Unter Belastung wird die App nicht unter normalen Bedingungen ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="a20c4-115">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="a20c4-116">Visual Studio 2019 ist die letzte Version von Visual Studio, die Auslastungstestfeatures bietet.</span><span class="sxs-lookup"><span data-stu-id="a20c4-116">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="a20c4-117">Kunden, die Auslastungstesttools benötigen, können zukünftig alternative Tools wie Apache JMeter, Akamai CloudTest oder BlazeMeter verwenden.</span><span class="sxs-lookup"><span data-stu-id="a20c4-117">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span>

<span data-ttu-id="a20c4-118">Weitere Informationen finden Sie unter [Testtools in den Versionshinweisen zu Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span><span class="sxs-lookup"><span data-stu-id="a20c4-118">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

<span data-ttu-id="a20c4-119">Visual Studio Tools</span><span class="sxs-lookup"><span data-stu-id="a20c4-119">Visual Studio tools</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="a20c4-120">In Visual Studio können Benutzer Tests zur Webleistung und Auslastungstests erstellen, entwickeln und debuggen.</span><span class="sxs-lookup"><span data-stu-id="a20c4-120">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span>

<span data-ttu-id="a20c4-121">Es steht eine Option zur Verfügung, Tests zu erstellen, indem Aktionen in einem Webbrowser aufgezeichnet werden.</span><span class="sxs-lookup"><span data-stu-id="a20c4-121">An option is available to create tests by recording actions in a web browser.</span></span>

* <span data-ttu-id="a20c4-122">Weitere Informationen zum Erstellen, Konfigurieren und Ausführen eines Auslastungstestprojekts mithilfe von Visual Studio 2017 finden Sie unter [Schnellstart: Erstellen eines Auslastungstestprojekts](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span><span class="sxs-lookup"><span data-stu-id="a20c4-122">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>
* <span data-ttu-id="a20c4-123">Auslastungstests können so konfiguriert werden, dass sie lokal oder in der Cloud mithilfe von Azure DevOps ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="a20c4-123">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>
* <span data-ttu-id="a20c4-124">Tools von Drittanbietern</span><span class="sxs-lookup"><span data-stu-id="a20c4-124">Third-party tools</span></span>
* <span data-ttu-id="a20c4-125">In der folgenden Liste finden Sie Webleistungstools von Drittanbietern, die verschiedene Features bieten:</span><span class="sxs-lookup"><span data-stu-id="a20c4-125">The following list contains third-party web performance tools with various feature sets:</span></span>
* [<span data-ttu-id="a20c4-126">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="a20c4-126">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="a20c4-127">ApacheBench (ab)</span><span class="sxs-lookup"><span data-stu-id="a20c4-127">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="a20c4-128">Gatling</span><span class="sxs-lookup"><span data-stu-id="a20c4-128">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="a20c4-129">k6</span><span class="sxs-lookup"><span data-stu-id="a20c4-129">k6</span></span>](https://k6.io)

