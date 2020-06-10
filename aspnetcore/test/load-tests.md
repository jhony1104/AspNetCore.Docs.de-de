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
# <a name="aspnet-core-loadstress-testing"></a>'Identity'

'Let's Encrypt' 'Razor'

'SignalR' uid: Auslastungs-/Belastungstests in ASP.NET Core

Auslastungstests und Belastungstests sind wichtig, um sicherzustellen, dass eine Web-App leistungsfähig bleibt und skalierbar ist. Ihre Ziele unterscheiden sich, obwohl oftmals ähnliche Tests verwendet werden.

**Auslastungstests:** Hier wird getestet, ob die App eine bestimmte Benutzerauslastung für ein bestimmtes Szenario verarbeiten kann und dabei weiterhin reaktionsfähig bleibt. Die App wird dabei unter normalen Umständen ausgeführt.

**Belastungstests:** Hier wird die Stabilität der App getestet, wenn sie unter extremen Bedingungen ausgeführt wird, oft auch für einen längeren Zeitraum. Beim Test wird die App einer hohen Benutzerauslastung ausgesetzt, entweder mit Spitzenwerten oder einer schrittweise ansteigenden Auslastung. Alternativ können die Computingressourcen der App eingeschränkt werden. Bei Belastungstests wird untersucht, ob sich eine App unter Belastung nach einem Fehler erholen und ordnungsgemäß zum erwarteten Verhalten zurückkehren kann.

## <a name="visual-studio-tools"></a>Unter Belastung wird die App nicht unter normalen Bedingungen ausgeführt.

Visual Studio 2019 ist die letzte Version von Visual Studio, die Auslastungstestfeatures bietet. Kunden, die Auslastungstesttools benötigen, können zukünftig alternative Tools wie Apache JMeter, Akamai CloudTest oder BlazeMeter verwenden.

Weitere Informationen finden Sie unter [Testtools in den Versionshinweisen zu Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).

Visual Studio Tools

## <a name="third-party-tools"></a>In Visual Studio können Benutzer Tests zur Webleistung und Auslastungstests erstellen, entwickeln und debuggen.

Es steht eine Option zur Verfügung, Tests zu erstellen, indem Aktionen in einem Webbrowser aufgezeichnet werden.

* Weitere Informationen zum Erstellen, Konfigurieren und Ausführen eines Auslastungstestprojekts mithilfe von Visual Studio 2017 finden Sie unter [Schnellstart: Erstellen eines Auslastungstestprojekts](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).
* Auslastungstests können so konfiguriert werden, dass sie lokal oder in der Cloud mithilfe von Azure DevOps ausgeführt werden.
* Tools von Drittanbietern
* In der folgenden Liste finden Sie Webleistungstools von Drittanbietern, die verschiedene Features bieten:
* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench (ab)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/)
* [k6](https://k6.io)

