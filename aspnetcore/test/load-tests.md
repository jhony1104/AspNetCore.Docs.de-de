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
# <a name="aspnet-core-loadstress-testing"></a>ASP.net Core laden/Belastungstest

Auslastungs Tests und Belastungstests sind wichtig, um sicherzustellen, dass eine Web-App Performance und skalierbar ist. Ihre Ziele sind unterschiedlich, obwohl Sie häufig ähnliche Tests gemeinsam nutzen.

**Auslastungs Tests** &ndash; Testen Sie, ob die APP eine bestimmte Last von Benutzern für ein bestimmtes Szenario verarbeiten kann, während das Antwort Ziel weiterhin erfüllt wird. Die APP wird unter normalen Bedingungen ausgeführt.

**Belastungstests** &ndash; Testen Sie die APP-Stabilität, wenn Sie unter Extrembedingungen ausgeführt wird, häufig für längere Zeit. Die Tests platzieren hohe Benutzer Auslastung, entweder Spitzen oder allmählich höhere Auslastung, in der APP oder beschränken die computingressourcen der app.

Belastungstests bestimmen, ob eine zu Belastungs Ende App nach einem Fehler wieder hergestellt werden kann und ordnungsgemäß zum erwarteten Verhalten zurückkehren kann. Unter Belastung wird die APP nicht unter normalen Bedingungen ausgeführt.

Visual Studio 2019 ist die letzte Version von Visual Studio mit Auslastungs Test Features. Für Kunden, die zukünftig Auslastungs TestTools benötigen, empfehlen wir alternative Tools, wie z. b. Apache JMeter, Akamai cloudtest und blazemeter. Weitere Informationen finden Sie in den [Anmerkungen zu dieser Version von Visual Studio 2019](/visualstudio/releases/2019/release-notes-v16.0#test-tools).

Der Auslastungs Test Dienst in Azure devops endet in 2020. Weitere Informationen finden Sie unter [Dienst Ende des cloudbasierten Auslastungs Tests](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).

## <a name="visual-studio-tools"></a>Visual Studio Tools

Visual Studio ermöglicht Benutzern das Erstellen, entwickeln und Debuggen von webleistungs-und Auslastungs Tests. Eine Option ist verfügbar, um Tests zu erstellen, indem Aktionen in einem Webbrowser aufgezeichnet werden.

Informationen zum Erstellen, konfigurieren und Ausführen eines Auslastungs Testprojekts mit Visual Studio 2017 finden [Sie unter Schnellstart: Erstellen eines Auslastungstestprojekts](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).

Auslastungs Tests können so konfiguriert werden, dass Sie lokal oder mithilfe von Azure devops in der Cloud ausgeführt werden.

## <a name="azure-devops"></a>Azure DevOps

Auslastungs Testläufe können mithilfe des [Azure devops-Test Plans](/azure/devops/test/load-test/index?view=vsts) Dienstanbieter gestartet werden.

![Startseite für Azure devops-Auslastungs Tests](./load-tests/_static/azure-devops-load-test.png)

Der Dienst unterstützt die folgenden Testformate:

* Visual Studio &ndash; -Webtest in Visual Studio erstellt.
* Http- &ndash; Archiv erfasste HTTP-Datenverkehr innerhalb des Archivs wird während des Tests wiedergegeben.
* [URL-basiert](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) &ndash; Ermöglicht das Angeben von URLs für Auslastungs Tests, Anforderungs Typen, Header und Abfrage Zeichenfolgen. Es können Lauf Zeit Einstellungsparameter wie Dauer, Auslastungs Muster und Anzahl von Benutzern konfiguriert werden.
* [Apache JMeter](https://jmeter.apache.org/).

## <a name="azure-portal"></a>Azure-Portal

[Azure-Portal ermöglicht das Einrichten und Ausführen von Auslastungs Tests von Web-Apps](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) direkt über die Registerkarte **Leistung** der APP Service in Azure-Portal.

![Azure App Service in Azure-Portal](./load-tests/_static/azure-appservice-perf-test.png)

Der Test kann ein manueller Test mit einer angegebenen URL oder einer Visual Studio-Webtest Datei sein, die mehrere URLs testen kann.

![Neue Seite "Leistungs Test" auf Azure-Portal](./load-tests/_static/azure-appservice-perf-test-config.png)

Am Ende des Tests zeigen generierte Berichte die Leistungsmerkmale der APP an. Beispiel Statistiken:

* Durchschnittliche Reaktionszeit
* Maximaler Durchsatz: Anforderungen pro Sekunde
* Fehler Prozentsatz

## <a name="third-party-tools"></a>Drittanbieter Tools

Die folgende Liste enthält webleistungs Tools von Drittanbietern mit verschiedenen Funktionsgruppen:

* [Apache JMeter](https://jmeter.apache.org/)
* [Apachebench (ab)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gator](https://gatling.io/)
* [Locust](https://locust.io/)
* [Webanstieg für Westwind](https://websurge.west-wind.com/)
* [Wird netult](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)
