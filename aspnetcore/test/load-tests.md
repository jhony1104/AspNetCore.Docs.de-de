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
# <a name="aspnet-core-loadstress-testing"></a>ASP.net Core laden/Belastungstest

Auslastungs Tests und Belastungstests sind wichtig, um sicherzustellen, dass eine Web-App Performance und skalierbar ist. Ihre Ziele sind unterschiedlich, obwohl Sie häufig ähnliche Tests gemeinsam nutzen.

**Auslastungs Tests** &ndash; testen, ob die APP eine bestimmte Last von Benutzern für ein bestimmtes Szenario verarbeiten kann, während gleichzeitig das Antwort Ziel erfüllt ist. Die APP wird unter normalen Bedingungen ausgeführt.

**Belastungstests** &ndash; Test-App-Stabilität, wenn Sie unter Extrembedingungen ausgeführt wird, häufig für einen längeren Zeitraum. Die Tests platzieren hohe Benutzer Auslastung, entweder Spitzen oder allmählich höhere Auslastung, in der APP oder beschränken die computingressourcen der app.

Belastungstests bestimmen, ob eine zu Belastungs Ende App nach einem Fehler wieder hergestellt werden kann und ordnungsgemäß zum erwarteten Verhalten zurückkehren kann. Unter Belastung wird die APP nicht unter normalen Bedingungen ausgeführt.

Visual Studio 2019 ist die letzte Version von Visual Studio mit Auslastungs Test Features. Für Kunden, die zukünftig Auslastungs TestTools benötigen, empfehlen wir alternative Tools, wie z. b. Apache JMeter, Akamai cloudtest und blazemeter. Weitere Informationen finden Sie in den [Anmerkungen zu dieser Version von Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).

## <a name="visual-studio-tools"></a>Visual Studio Tools

Visual Studio ermöglicht Benutzern das Erstellen, entwickeln und Debuggen von webleistungs-und Auslastungs Tests. Eine Option ist verfügbar, um Tests zu erstellen, indem Aktionen in einem Webbrowser aufgezeichnet werden.

Informationen zum Erstellen, konfigurieren und Ausführen von Auslastungs Testprojekten mit Visual Studio 2017 finden Sie unter [Schnellstart: Erstellen eines Auslastungs Testprojekts](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).

Auslastungs Tests können so konfiguriert werden, dass Sie lokal oder mithilfe von Azure devops in der Cloud ausgeführt werden.

## <a name="third-party-tools"></a>Drittanbieter Tools

Die folgende Liste enthält webleistungs Tools von Drittanbietern mit verschiedenen Funktionsgruppen:

* [Apache JMeter](https://jmeter.apache.org/)
* [Apachebench (ab)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gator](https://gatling.io/)
* [Locust](https://locust.io/)
* [Webanstieg für Westwind](https://websurge.west-wind.com/)
* [Wird netult](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)
