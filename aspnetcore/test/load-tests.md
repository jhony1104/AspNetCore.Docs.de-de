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
ms.openlocfilehash: 0ec69ad783a4e545ea95ddcb928d03ba6a2e0050
ms.sourcegitcommit: 4437f4c149f1ef6c28796dcfaa2863b4c088169c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/19/2020
ms.locfileid: "85074377"
---
# <a name="aspnet-core-loadstress-testing"></a>Auslastungs-/Belastungstests in ASP.NET Core

Auslastungstests und Belastungstests sind wichtig, um sicherzustellen, dass eine Web-App leistungsfähig bleibt und skalierbar ist. Ihre Ziele unterscheiden sich, obwohl oftmals ähnliche Tests verwendet werden.

**Auslastungstests:** Hier wird getestet, ob die App eine bestimmte Benutzerauslastung für ein bestimmtes Szenario verarbeiten kann und dabei weiterhin reaktionsfähig bleibt. Die App wird dabei unter normalen Umständen ausgeführt.

**Belastungstests:** Hier wird die Stabilität der App getestet, wenn sie unter extremen Bedingungen ausgeführt wird, oft auch für einen längeren Zeitraum. Beim Test wird die App einer hohen Benutzerauslastung ausgesetzt, entweder mit Spitzenwerten oder einer schrittweise ansteigenden Auslastung. Alternativ können die Computingressourcen der App eingeschränkt werden.

Bei Belastungstests wird untersucht, ob sich eine App unter Belastung nach einem Fehler erholen und ordnungsgemäß zum erwarteten Verhalten zurückkehren kann. Unter Belastung wird die App nicht unter normalen Bedingungen ausgeführt.

Es wurden Pläne angekündigt, [den Auslastungstest in Visual Studio 2019 einzustellen](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/). Der entsprechende cloudbasierte Azure DevOps-Auslastungstestdienst wurde geschlossen.

## <a name="third-party-tools"></a>Tools von Drittanbietern

In der folgenden Liste finden Sie Webleistungstools von Drittanbietern, die verschiedene Features bieten:

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench (ab)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/)
* [k6](https://k6.io)
* [Locust](https://locust.io/)
* [West Wind WebSurge](https://websurge.west-wind.com/)
* [Netling](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)