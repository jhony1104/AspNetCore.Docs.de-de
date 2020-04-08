---
title: Auslastungs-/Belastungstests in ASP.NET Core
author: Jeremy-Meng
description: Hier erhalten Sie weitere Informationen zu hilfreichen Tools und Ansätzen für Auslastungstests und Belastungstests für ASP.NET Core-Apps.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: test/loadtests
ms.openlocfilehash: 1fd77a767fb53b9276081dd712e13108094a0382
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2020
ms.locfileid: "78649639"
---
# <a name="aspnet-core-loadstress-testing"></a>Auslastungs-/Belastungstests in ASP.NET Core

Auslastungstests und Belastungstests sind wichtig, um sicherzustellen, dass eine Web-App leistungsfähig bleibt und skalierbar ist. Ihre Ziele unterscheiden sich, obwohl oftmals ähnliche Tests verwendet werden.

**Auslastungstests:** Hier wird getestet, ob die App eine bestimmte Benutzerauslastung für ein bestimmtes Szenario verarbeiten kann, und dabei weiterhin reaktionsfähig bleibt. Die App wird dabei unter normalen Umständen ausgeführt.

**Belastungstests:** Hier wird die Stabilität der App getestet, wenn sie unter extremen Bedingungen ausgeführt wird, oft auch für einen längeren Zeitraum. Beim Test wird die App einer hohen Benutzerauslastung ausgesetzt, entweder mit Spitzenwerten oder einer schrittweise ansteigenden Auslastung. Alternativ können die Computingressourcen der App eingeschränkt werden.

Bei Belastungstests wird untersucht, ob sich eine App unter Belastung nach einem Fehler erholen und ordnungsgemäß zum erwarteten Verhalten zurückkehren kann. Unter Belastung wird die App nicht unter normalen Bedingungen ausgeführt.

Visual Studio 2019 ist die letzte Version von Visual Studio, die Auslastungstestfeatures bietet. Kunden, die Auslastungstesttools benötigen, können zukünftig alternative Tools wie Apache JMeter, Akamai CloudTest oder BlazeMeter verwenden. Weitere Informationen finden Sie unter [Testtools in den Versionshinweisen zu Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).

## <a name="visual-studio-tools"></a>Visual Studio Tools

In Visual Studio können Benutzer Tests zur Webleistung und Auslastungstests erstellen, entwickeln und debuggen. Es steht eine Option zur Verfügung, Tests zu erstellen, indem Aktionen in einem Webbrowser aufgezeichnet werden.

Weitere Informationen zum Erstellen, Konfigurieren und Ausführen eines Auslastungstestprojekts mithilfe von Visual Studio 2017 finden Sie unter [Schnellstart: Erstellen eines Auslastungstestprojekts](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).

Auslastungstests können so konfiguriert werden, dass sie lokal oder in der Cloud mithilfe von Azure DevOps ausgeführt werden.

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

