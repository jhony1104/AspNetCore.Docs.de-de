---
title: ASP.NET Core laden/Belastungstests
author: Jeremy-Meng
description: Lernen Sie einige wichtige Tools und Ansätze für Auslastungstests und Belastungstests in ASP.NET Core-apps.
ms.author: riande
ms.custom: mvc
ms.date: 04/05/2019
uid: test/loadtests
ms.openlocfilehash: 3c21da6c799bc3080a1a16cb62ae4535b8890a1b
ms.sourcegitcommit: bee530454ae2b3c25dc7ffebf93536f479a14460
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67724491"
---
# <a name="aspnet-core-loadstress-testing"></a>ASP.NET Core laden/Belastungstests

Auslastungstests und Belastungstests sind wichtig, sicherzustellen, dass eine Web-app leistungsfähig ist und skalierbar. Ihre Ziele unterscheiden, obwohl sie häufig ähnliche Tests freigeben.

**Auslastungstests** &ndash; testen, ob die app auf eine angegebene Last von Benutzern für eine bestimmte Szenario behandeln kann, und dabei trotzdem das Antwortziel. Die app wird unter normalen Bedingungen ausgeführt.

**Belastungstests** &ndash; Stabilität der app zu testen, wenn unter extremen Bedingungen häufig für längere Zeit ausgeführt wird. Die Tests hohe benutzerauslastung, entweder "Spitzen" oder "schrittweise zunehmenden Auslastung, platzieren Sie in der app, oder sie der app-Compute-Ressourcen beschränken.

Belastungstests bestimmen, ob eine app unter Last Wiederherstellen nach einem Fehler und erwarteten Verhalten zurück. Bei hoher Belastung ist nicht die app unter normalen Bedingungen ausgeführt.

Visual Studio-2019 ist die letzte Version von Visual Studio mit Funktionen für Auslastungstests. Für Kunden Auslastungstesttools in der Zukunft empfehlen wir die anderen Tools wie Apache JMeter Akamai CloudTest und BlazeMeter. Weitere Informationen finden Sie unter den [Anmerkungen zu Visual Studio 2019](/visualstudio/releases/2019/release-notes#test-tools).

Die Dienst-Auslastungstests in Azure DevOps endet in 2020. Weitere Informationen finden Sie unter [cloudbasierte Auslastungstests Service Ende ihrer Lebensdauer](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).

## <a name="visual-studio-tools"></a>Visual Studio Tools

Visual Studio ermöglicht das Erstellen, entwickeln und Debuggen von webleistungs-und Auslastungstest. Eine Option ist verfügbar, um Tests zu erstellen, durch Aufzeichnen der Aktionen in einem Webbrowser.

Informationen zum Erstellen, konfigurieren und Ausführen eines Auslastungstests Projekte mit Visual Studio 2017 finden Sie unter [Schnellstart: Erstellen eines Auslastungstestprojekts](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017). Weitere Informationen finden Sie unter den [Zusatzressourcen](#additional-resources) Abschnitt.

Auslastungstests können konfiguriert werden, führen Sie lokal oder Ausführung in der Cloud mit Azure DevOps.

## <a name="azure-devops"></a>Azure DevOps

Auslastungstestläufe können gestartet werden, mithilfe der [Azure DevOps Testplänen](/azure/devops/test/load-test/index?view=vsts) Service.

![Azure-Angebotsseite der DevOps-Auslastungstests](./load-tests/_static/azure-devops-load-test.png)

Der Dienst unterstützt die folgenden Test-Formate:

* Visual Studio &ndash; Webtest in Visual Studio erstellt.
* HTTP-Archiv &ndash; erfassten HTTP-Datenverkehr in das Archiv wird wiedergegeben, während der Tests.
* [URL-basierte](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) &ndash; ermöglicht das Angeben von URLs, um den Test, Anforderungstypen, Header und Abfragezeichenfolgen zu laden. Festlegen von Parametern, z. B. Dauer ausgeführt werden, kann Auslastungsmuster und Anzahl von Benutzern konfiguriert werden.
* [Apache JMeter](https://jmeter.apache.org/).

## <a name="azure-portal"></a>Azure-Portal

[Azure-Portal ermöglicht das Einrichten und Ausführen von Auslastungstests von Web-apps](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) direkt aus der **Leistung** App Service im Azure-Portal auf der Registerkarte.

![Azure App Service im Azure-portal](./load-tests/_static/azure-appservice-perf-test.png)

Der Test kann einen manuellen Test mit einer angegebenen URL oder eine Visual Studio-Webtest-Datei, die mehrere URLs zu testen sein.

![Neue Leistung-Testseite im Azure-portal](./load-tests/_static/azure-appservice-perf-test-config.png)

Am Ende des Tests zeigen die generierten Berichte die Leistungsmerkmale der app. Beispiel-Statistiken umfassen:

* Durchschnittliche Antwortzeit
* Maximaler Durchsatz: Anforderungen pro Sekunde
* Ausführungsfehler in Prozent

## <a name="third-party-tools"></a>Drittanbieter-tools

Die folgende Liste enthält die Drittanbieter-Web-Leistungstools mit verschiedenen Featuresätze:

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench (ab)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/)
* [Locust](https://locust.io/)
* [West Wind WebSurge](http://websurge.west-wind.com/)
* [Netling](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Laden Sie die Test-Blog-Reihe](https://blogs.msdn.microsoft.com/charles_sterling/2015/06/01/load-test-series-part-i-creating-web-performance-tests-for-a-load-test/)
