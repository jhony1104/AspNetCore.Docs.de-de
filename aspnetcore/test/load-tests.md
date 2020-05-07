---
title: Auslastungs-/Belastungstests in ASP.NET Core
author: Jeremy-Meng
description: Hier erhalten Sie weitere Informationen zu hilfreichen Tools und Ansätzen für Auslastungstests und Belastungstests für ASP.NET Core-Apps.
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
ms.openlocfilehash: cf99eaa71846ea705a312b0fb773605fc77b0d97
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775257"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="7b556-103">Auslastungs-/Belastungstests in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7b556-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="7b556-104">Auslastungstests und Belastungstests sind wichtig, um sicherzustellen, dass eine Web-App leistungsfähig bleibt und skalierbar ist.</span><span class="sxs-lookup"><span data-stu-id="7b556-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="7b556-105">Ihre Ziele unterscheiden sich, obwohl oftmals ähnliche Tests verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="7b556-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="7b556-106">**Auslastungstests:** Hier wird getestet, ob die App eine bestimmte Benutzerauslastung für ein bestimmtes Szenario verarbeiten kann, und dabei weiterhin reaktionsfähig bleibt.</span><span class="sxs-lookup"><span data-stu-id="7b556-106">**Load tests** &ndash; Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="7b556-107">Die App wird dabei unter normalen Umständen ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="7b556-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="7b556-108">**Belastungstests:** Hier wird die Stabilität der App getestet, wenn sie unter extremen Bedingungen ausgeführt wird, oft auch für einen längeren Zeitraum.</span><span class="sxs-lookup"><span data-stu-id="7b556-108">**Stress tests** &ndash; Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="7b556-109">Beim Test wird die App einer hohen Benutzerauslastung ausgesetzt, entweder mit Spitzenwerten oder einer schrittweise ansteigenden Auslastung. Alternativ können die Computingressourcen der App eingeschränkt werden.</span><span class="sxs-lookup"><span data-stu-id="7b556-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="7b556-110">Bei Belastungstests wird untersucht, ob sich eine App unter Belastung nach einem Fehler erholen und ordnungsgemäß zum erwarteten Verhalten zurückkehren kann.</span><span class="sxs-lookup"><span data-stu-id="7b556-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="7b556-111">Unter Belastung wird die App nicht unter normalen Bedingungen ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="7b556-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="7b556-112">Visual Studio 2019 ist die letzte Version von Visual Studio, die Auslastungstestfeatures bietet.</span><span class="sxs-lookup"><span data-stu-id="7b556-112">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="7b556-113">Kunden, die Auslastungstesttools benötigen, können zukünftig alternative Tools wie Apache JMeter, Akamai CloudTest oder BlazeMeter verwenden.</span><span class="sxs-lookup"><span data-stu-id="7b556-113">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="7b556-114">Weitere Informationen finden Sie unter [Testtools in den Versionshinweisen zu Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span><span class="sxs-lookup"><span data-stu-id="7b556-114">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="7b556-115">Visual Studio Tools</span><span class="sxs-lookup"><span data-stu-id="7b556-115">Visual Studio tools</span></span>

<span data-ttu-id="7b556-116">In Visual Studio können Benutzer Tests zur Webleistung und Auslastungstests erstellen, entwickeln und debuggen.</span><span class="sxs-lookup"><span data-stu-id="7b556-116">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="7b556-117">Es steht eine Option zur Verfügung, Tests zu erstellen, indem Aktionen in einem Webbrowser aufgezeichnet werden.</span><span class="sxs-lookup"><span data-stu-id="7b556-117">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="7b556-118">Weitere Informationen zum Erstellen, Konfigurieren und Ausführen eines Auslastungstestprojekts mithilfe von Visual Studio 2017 finden Sie unter [Schnellstart: Erstellen eines Auslastungstestprojekts](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span><span class="sxs-lookup"><span data-stu-id="7b556-118">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="7b556-119">Auslastungstests können so konfiguriert werden, dass sie lokal oder in der Cloud mithilfe von Azure DevOps ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="7b556-119">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="7b556-120">Tools von Drittanbietern</span><span class="sxs-lookup"><span data-stu-id="7b556-120">Third-party tools</span></span>

<span data-ttu-id="7b556-121">In der folgenden Liste finden Sie Webleistungstools von Drittanbietern, die verschiedene Features bieten:</span><span class="sxs-lookup"><span data-stu-id="7b556-121">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="7b556-122">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="7b556-122">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="7b556-123">ApacheBench (ab)</span><span class="sxs-lookup"><span data-stu-id="7b556-123">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="7b556-124">Gatling</span><span class="sxs-lookup"><span data-stu-id="7b556-124">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="7b556-125">k6</span><span class="sxs-lookup"><span data-stu-id="7b556-125">k6</span></span>](https://k6.io)
* [<span data-ttu-id="7b556-126">Locust</span><span class="sxs-lookup"><span data-stu-id="7b556-126">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="7b556-127">West Wind WebSurge</span><span class="sxs-lookup"><span data-stu-id="7b556-127">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="7b556-128">Netling</span><span class="sxs-lookup"><span data-stu-id="7b556-128">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="7b556-129">Vegeta</span><span class="sxs-lookup"><span data-stu-id="7b556-129">Vegeta</span></span>](https://github.com/tsenart/vegeta)

