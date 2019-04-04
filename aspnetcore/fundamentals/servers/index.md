---
title: Webserverimplementierungen in ASP.NET Core
author: guardrex
description: Ermitteln Sie die Webserver Kestrel und HTTP.sys für ASP.NET Core. Erfahren Sie mehr über das Auswählen eines Servers und darüber, wann ein Reverseproxyserver zu verwenden ist.
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/14/2019
uid: fundamentals/servers/index
ms.openlocfilehash: 4fa32fb4fe1d482f1000cd803cd2d26cd9ff2341
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410533"
---
# <a name="web-server-implementations-in-aspnet-core"></a>Webserverimplementierungen in ASP.NET Core

Von [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73) und [Chris Ross](https://github.com/Tratcher)

Eine ASP.NET Core-App wird über eine In-Process-Implementierung eines HTTP-Servers ausgeführt. Die Serverimplementierung lauscht auf HTTP-Anforderungen und leitet diese als [Anforderungsfunktionen](xref:fundamentals/request-features), die in einer <xref:Microsoft.AspNetCore.Http.HttpContext>-Klasse zusammengefasst werden, an die App weiter.

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

ASP.NET Core wird mit folgendem Umfang ausgeliefert:

* Ein [Kestrel-Server](xref:fundamentals/servers/kestrel) stellt die plattformübergreifende Standardimplementierung von HTTP-Servern dar.
* IIS-HTTP-Server ist ein [In-Process-Server](#in-process-hosting-model) für IIS.
* [HTTP.sys Server](xref:fundamentals/servers/httpsys) ist ein nur für Windows verfügbarer HTTP-Server, der auf dem [Http.sys-Kerneltreiber und der HTTP Server API](/windows/desktop/Http/http-api-start-page) basiert.

Wenn Sie [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) oder [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) verwenden, wird die App auf zwei unterschiedliche Arten ausgeführt:

* Im gleichen Prozess wie im IIS-Workerprozess (das [In-Process-Hostingmodell](#in-process-hosting-model)) mit dem [IIS-HTTP-Server](#iis-http-server). *In-Process* ist die empfohlene Konfiguration.
* In einem anderen Prozess als im IIS-Workerprozess (das [Out-of-Process-Hostingmodell](#out-of-process-hosting-model)) mit dem [Kestrel-Server](#kestrel).

Das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) ist ein natives IIS-Modul, das native IIS-Anforderungen zwischen IIS und dem In-Process-IIS-HTTP-Server oder Kestrel verarbeitet. Weitere Informationen finden Sie unter <xref:host-and-deploy/aspnet-core-module>.

## <a name="hosting-models"></a>Hostingmodelle

### <a name="in-process-hosting-model"></a>In-Process-Hostingmodell

Beim Einsatz von In-Process-Hosting wird eine ASP.NET Core-App im gleichen Prozess wie ihr IIS-Arbeitsprozess ausgeführt. Durch das In-Process-Hosting wird die Leistung des Out-of-Process-Hosting verbessert, da Anforderungen nicht per Proxy über den Loopbackadapter weitergeleitet werden. Dabei handelt es sich um eine Netzwerkschnittstelle, die ausgehenden Netzwerkdatenverkehr zum selben Computer zurück leitet. IIS erledigt das Prozessmanagement mit dem [Windows-Prozessaktivierungsdienst (Process Activation Service, WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

Das ASP.NET Core-Modul:

* Führt die Initialisierung von Apps aus.
  * Lädt die [CoreCLR](/dotnet/standard/glossary#coreclr).
  * Ruft `Program.Main`.
* Behandelt die Lebensdauer der nativen IIS-Anforderung.

Das In-Process-Hostingmodell wird nicht für ASP.NET Core-Apps unterstützt, die auf .NET Framework abzielen.

Das folgende Diagramm zeigt die Beziehung zwischen IIS, dem ASP.NET Core-Modul und einer In-Process gehosteten App:

![ASP.NET Core-Modul](_static/ancm-inprocess.png)

Eine Anforderung geht aus dem Web beim HTTP.sys-Treiber im Kernelmodus ein. Der Treiber leitet die native Anforderung an IIS auf dem konfigurierten Port der Webseite weiter, normalerweise 80 (HTTP) oder 443 (HTTPS). Das Modul empfängt die native Anforderung und übergibt sie an den IIS-HTTP-Server (`IISHttpServer`). Der IIS-HTTP-Server ist eine prozessinterne Serverimplementierung für IIS, die die Anforderung vom nativen Modus in den verwalteten Modus konvertiert.

Nachdem der IIS-HTTP-Server die Anforderung verarbeitet hat, wird die Anforderung per Push an die Middlewarepipeline von ASP.NET Core übertragen. Die Middleware-Pipeline behandelt die Anforderung und gibt sie als `HttpContext`-Instanz an die App-Logik weiter. Die Antwort der App wird über den IIS-HTTP-Server zurück an IIS übergeben. IIS übermittelt die Antwort an den Client, der die Anforderung initiiert hat.

In-Process-Hosting ist eine wählbare Option für vorhandene Apps. Die [dotnet new](/dotnet/core/tools/dotnet-new)-Vorlagen sehen das In-Process-Hostingmodell aber als Standard für alle IIS- und IIS Express-Szenarios vor.

### <a name="out-of-process-hosting-model"></a>Out-of-Process-Hostingmodell

Da ASP.NET Core-Apps in einem Prozess getrennt vom IIS-Arbeitsprozess ausgeführt werden, führt das Modul die Prozessverwaltung durch. Das Modul startet den Prozess für die ASP.NET Core-App, wenn die erste Anforderung eingeht und startet die App neu, wenn sie heruntergefahren wird oder abstürzt. Dies ist im Prinzip das gleiche Verhalten wie bei Apps, die prozessintern ausgeführt und durch den [Windows-Prozessaktivierungsdienst (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) verwaltet werden.

Das folgende Diagramm zeigt die Beziehung zwischen IIS, dem ASP.NET Core-Modul und einer Out-of-Process gehosteten App:

![ASP.NET Core-Modul](_static/ancm-outofprocess.png)

Anforderungen gehen aus dem Internet an den Treiber „HTTP.sys“ ein, der im Kernelmodus betrieben wird. Der Treiber leitet die Anforderungen an IIS auf dem konfigurierten Port der Webseite weiter, normalerweise 80 (HTTP) oder 443 (HTTPS). Das Modul leitet die Anforderung an Kestrel auf einem zufälligen Port der App weiter, der nicht Port 80 oder 443 entspricht.

Das Modul gibt den Port über die Umgebungsvariable beim Start an. Die Middleware für die Integration von IIS konfiguriert den Server so, dass er auf `http://localhost:{PORT}` lauscht. Zusätzliche Überprüfungen werden durchgeführt. Anforderungen, die nicht vom Modul stammen, werden abgelehnt. Das Modul unterstützt die HTTPS-Weiterleitung nicht. Deshalb werden Anforderungen über HTTP weitergeleitet, selbst wenn sie von IIS über HTTPS empfangen wurden.

Nachdem Kestrel die Anforderung vom Modul erhalten hat, wird die Anforderung in die Middleware-Pipeline von ASP.NET Core eingestellt. Die Middleware-Pipeline behandelt die Anforderung und gibt sie als `HttpContext`-Instanz an die App-Logik weiter. Die durch IIS-Integration hinzugefügte Middleware aktualisiert das Schema, die Remote-IP und die Pfadbasis, um der Weiterleitung der Anforderung an Kestrel Rechnung zu tragen. Die Antwort der App wird dann an IIS zurückgegeben, wo sie per Push an den HTTP-Client zurückgegeben wird, der die Anforderung initiiert hat.

In den folgenden Artikeln finden Sie Konfigurationsrichtlinien für IIS und ein ASP.NET Core-Modul:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macostabmacos"></a>[macOS](#tab/macos)

ASP.NET Core wird mit [Kestrel Server](xref:fundamentals/servers/kestrel) ausgeliefert, der der plattformübergreifende HTTP-Standardserver ist.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

ASP.NET Core wird mit [Kestrel Server](xref:fundamentals/servers/kestrel) ausgeliefert, der der plattformübergreifende HTTP-Standardserver ist.

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

ASP.NET Core wird mit folgendem Umfang ausgeliefert:

* [Kestrel Server](xref:fundamentals/servers/kestrel) ist der plattformübergreifende HTTP-Standardserver.
* [HTTP.sys Server](xref:fundamentals/servers/httpsys) ist ein nur für Windows verfügbarer HTTP-Server, der auf dem [Http.sys-Kerneltreiber und der HTTP Server API](/windows/desktop/Http/http-api-start-page) basiert.

Wenn Sie [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) oder [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) verwenden, wird die App in einem anderen Prozess als im IIS-Workerprozess (*Out-of-Process*) mit dem [Kestrel-Server](#kestrel) ausgeführt.

Da ASP.NET Core-Apps in einem Prozess getrennt vom IIS-Arbeitsprozess ausgeführt werden, führt das Modul die Prozessverwaltung durch. Das Modul startet den Prozess für die ASP.NET Core-App, wenn die erste Anforderung eingeht und startet die App neu, wenn sie heruntergefahren wird oder abstürzt. Dies ist im Prinzip das gleiche Verhalten wie bei Apps, die prozessintern ausgeführt und durch den [Windows-Prozessaktivierungsdienst (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) verwaltet werden.

Das folgende Diagramm zeigt die Beziehung zwischen IIS, dem ASP.NET Core-Modul und einer Out-of-Process gehosteten App:

![ASP.NET Core-Modul](_static/ancm-outofprocess.png)

Anforderungen gehen aus dem Internet an den Treiber „HTTP.sys“ ein, der im Kernelmodus betrieben wird. Der Treiber leitet die Anforderungen an IIS auf dem konfigurierten Port der Webseite weiter, normalerweise 80 (HTTP) oder 443 (HTTPS). Das Modul leitet die Anforderung an Kestrel auf einem zufälligen Port der App weiter, der nicht Port 80 oder 443 entspricht.

Das Modul gibt den Port über die Umgebungsvariable beim Start an. Die Middleware für die Integration von IIS konfiguriert den Server so, dass er auf `http://localhost:{port}` lauscht. Zusätzliche Überprüfungen werden durchgeführt. Anforderungen, die nicht vom Modul stammen, werden abgelehnt. Das Modul unterstützt die HTTPS-Weiterleitung nicht. Deshalb werden Anforderungen über HTTP weitergeleitet, selbst wenn sie von IIS über HTTPS empfangen wurden.

Nachdem Kestrel die Anforderung vom Modul erhalten hat, wird die Anforderung in die Middleware-Pipeline von ASP.NET Core eingestellt. Die Middleware-Pipeline behandelt die Anforderung und gibt sie als `HttpContext`-Instanz an die App-Logik weiter. Die durch IIS-Integration hinzugefügte Middleware aktualisiert das Schema, die Remote-IP und die Pfadbasis, um der Weiterleitung der Anforderung an Kestrel Rechnung zu tragen. Die Antwort der App wird dann an IIS zurückgegeben, wo sie per Push an den HTTP-Client zurückgegeben wird, der die Anforderung initiiert hat.

In den folgenden Artikeln finden Sie Konfigurationsrichtlinien für IIS und ein ASP.NET Core-Modul:

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macostabmacos"></a>[macOS](#tab/macos)

ASP.NET Core wird mit [Kestrel Server](xref:fundamentals/servers/kestrel) ausgeliefert, der der plattformübergreifende HTTP-Standardserver ist.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

ASP.NET Core wird mit [Kestrel Server](xref:fundamentals/servers/kestrel) ausgeliefert, der der plattformübergreifende HTTP-Standardserver ist.

---

::: moniker-end

## <a name="kestrel"></a>Kestrel

Kestrel ist der Standardwebserver, der in ASP.NET Core-Projektvorlagen enthalten ist.

::: moniker range=">= aspnetcore-2.0"

Kestrel eignet sich für folgende Zwecke:

* Eigenständig als Edge-Server zur Verarbeitung von Anforderungen, die direkt aus einem Netzwerk, auch über das Internet, kommen.

  ![Kestrel kommuniziert direkt und ohne Reverseproxyserver mit dem Internet](kestrel/_static/kestrel-to-internet2.png)

* Mit einem *Reverseproxyserver* wie [IIS (Internetinformationsdienste)](https://www.iis.net/), [Nginx](http://nginx.org) oder [Apache](https://httpd.apache.org/). Ein Reverseproxyserver empfängt HTTP-Anforderungen aus dem Internet und leitet diese an Kestrel weiter.

  ![Kestrel kommuniziert indirekt mit dem Internet über einen Reverseproxyserver wie IIS, Nginx oder Apache](kestrel/_static/kestrel-to-internet.png)

Jede der beiden Hostingkonfigurationen &mdash;mit oder ohne einen Reverseproxyserver&mdash; wird für ASP.NET Core 2.1 oder neuere Apps unterstützt.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Wenn die App nur Anforderungen aus einem internen Netzwerk akzeptiert, kann Kestrel eigenständig verwendet werden.

![Kestrel kommuniziert direkt mit dem internen Netzwerk.](kestrel/_static/kestrel-to-internal.png)

Wenn die App für das Internet verfügbar gemacht ist, muss Kestrel einen *Reverseproxyserver* wie [IIS (Internetinformationsdienste)](https://www.iis.net/), [Nginx](http://nginx.org) oder [Apache](https://httpd.apache.org/) verwenden. Ein Reverseproxyserver empfängt HTTP-Anforderungen aus dem Internet und leitet diese an Kestrel weiter.

![Kestrel kommuniziert indirekt mit dem Internet über einen Reverseproxyserver wie IIS, Nginx oder Apache](kestrel/_static/kestrel-to-internet.png)

Der wichtigste Grund für die Verwendung eines Reverseproxys für öffentlich zugängliche Edge-Server-Bereitstellungen, die direkt im Internet verfügbar gemacht werden, ist die Sicherheit. Die 1.x-Versionen von Kestrel enthalten keine wesentlichen Sicherheitsfeatures zum Schutz vor Angriffen aus dem Internet. So sind z. B. keine geeigneten Timeouts, Größenlimits für Anforderungen sowie Beschränkungen der Anzahl gleichzeitiger Verbindungen vorhanden.

::: moniker-end

Leitfäden zur Kestrel-Konfiguration und Informationen darüber, wann Kestrel in einer Reverseproxykonfiguration verwendet wird, finden Sie unter <xref:fundamentals/servers/kestrel>.

### <a name="nginx-with-kestrel"></a>Nginx und Kestrel

Informationen dazu, wie Nginx unter Linux als Reverseproxyserver für Kestrel verwendet wird, finden Sie unter <xref:host-and-deploy/linux-nginx>.

### <a name="apache-with-kestrel"></a>Apache und Kestrel

Informationen dazu, wie Apache unter Linux als Reverseproxyserver für Kestrel verwendet wird, finden Sie unter <xref:host-and-deploy/linux-apache>.

::: moniker range=">= aspnetcore-2.2"

## <a name="iis-http-server"></a>IIS-HTTP-Server

IIS-HTTP-Server ist ein [In-Process-Server](#in-process-hosting-model) für IIS, der für In-Process-Bereitstellungen benötigt wird. Das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) verarbeitet native IIS-Anforderungen zwischen IIS und dem IIS-HTTP-Server. Weitere Informationen finden Sie unter <xref:host-and-deploy/aspnet-core-module>.

::: moniker-end

## <a name="httpsys"></a>HTTP.sys

Wenn ASP.NET Core-Apps unter Windows ausgeführt werden, ist HTTP.sys eine Alternative zu Kestrel. Grundsätzlich empfiehlt sich Kestrel, um die beste Leistung zu erzielen. HTTP.sys kann in Szenarien verwendet werden, in denen die App mit dem Internet verbunden ist und erforderliche Funktionen von HTTP.sys, aber nicht von Kestrel unterstützt werden. Weitere Informationen finden Sie unter <xref:fundamentals/servers/httpsys>.

![HTTP.sys kommuniziert direkt mit dem Internet](httpsys/_static/httpsys-to-internet.png)

Außerdem kann HTTP.sys auch bei Apss zum Einsatz kommen, die nur in einem internen Netzwerk verfügbar gemacht werden.

![HTTP.sys kommuniziert direkt mit dem internen Netzwerk](httpsys/_static/httpsys-to-internal.png)

Den Leitfaden für die HTTP.sys-Konfiguration finden Sie unter <xref:fundamentals/servers/httpsys>.

## <a name="aspnet-core-server-infrastructure"></a>ASP.NET Core-Serverinfrastruktur

Die Schnittstelle <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>, die in der `Startup.Configure`-Methode vorhanden ist, macht die Eigenschaft <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> vom Typ <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection> verfügbar. Kestrel und HTTP.sys machen nur jeweils eine einzelne Funktion verfügbar, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>. Andere Serverimplementierungen machen jedoch möglicherweise weitere Funktionalitäten verfügbar.

Mit `IServerAddressesFeature` kann ermittelt werden, an welchen Port die Serverimplementierung zur Laufzeit gebunden ist.

## <a name="custom-servers"></a>Benutzerdefinierte Server

Wenn die integrierten Server nicht den Anforderungen der App entsprechen, kann eine benutzerdefinierte Serverimplementierung erstellt werden. In der [Anleitung zu Open Web Interface for .NET (OWIN)](xref:fundamentals/owin) wird erläutert, wie eine auf [Nowin](https://github.com/Bobris/Nowin) basierende <xref:Microsoft.AspNetCore.Hosting.Server.IServer>-Implementierung erstellt wird. Nur die Schnittstellen von Funktionen, die von der App verwendet werden, erfordern Implementierung, wobei mindestens <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> und <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> unterstützt werden müssen.

## <a name="server-startup"></a>Serverstart

Der Server wird gestartet, wenn die integrierte Entwicklungsumgebung (IDE) oder der Editor die App startet:

* [Visual Studio:](https://www.visualstudio.com/vs/) Die Startprofile können verwendet werden, um die App und den Server mit [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) oder mit der Konsole zu starten.
* [Visual Studio Code:](https://code.visualstudio.com/) Die App und der Server werden durch [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode) gestartet, das den CoreCLR-Debugger aktiviert.
* [Visual Studio für Mac:](https://www.visualstudio.com/vs/mac/) Die App und der Server werden durch den [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/) gestartet.

Wird die App über eine Eingabeaufforderung im Ordner des Projekts gestartet, startet [dotnet run](/dotnet/core/tools/dotnet-run) die App und den Server (nur Kestrel und HTTP.sys). Die Konfiguration wird durch die Option `-c|--configuration` angegeben, die entweder auf `Debug` (Standardwert) oder `Release` festgelegt ist. Sind in der Datei *launchSettings.json* Startprofile vorhanden, verwenden Sie die Option `--launch-profile <NAME>`, um das Startprofil festzulegen (z. B. `Development` oder `Production`). Weitere Informationen hierzu finden Sie unter [dotnet run](/dotnet/core/tools/dotnet-run) und unter [Verpacken einer Verteilung von .NET Core](/dotnet/core/build/distribution-packaging).

## <a name="http2-support"></a>HTTP/2-Unterstützung

[HTTP/2](https://httpwg.org/specs/rfc7540.html) wird mit ASP.NET Core in den folgenden Bereitstellungsszenarien unterstützt:

::: moniker range=">= aspnetcore-2.2"

* [Kestrel](xref:fundamentals/servers/kestrel#http2-support)
  * Betriebssystem
    * Windows Server 2016/Windows 10 oder höher&dagger;
    * Linux mit OpenSSL 1.0.2 oder höher (z.B. Ubuntu 16.04 oder höher)
    * HTTP/2 wird unter macOS in einem zukünftigen Release unterstützt.
  * Zielframework: .NET Core 2.2 oder höher
* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 oder höher
  * Zielframework: Gilt nicht für HTTP.sys-Bereitstellungen.
* [IIS (In-Process)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 oder höher, IIS 10 oder höher
  * Zielframework: .NET Core 2.2 oder höher
* [IIS (Out-of-Process)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 oder höher, IIS 10 oder höher
  * Öffentlich zugängliche Edge-Server-Verbindungen verwenden HTTP/2, aber die Reverseproxyverbindung mit Kestrel verwendet HTTP/1.1.
  * Zielframework: Gilt nicht für Out-of-Process-Bereitstellungen von IIS.

&dagger;Kestrel bietet eingeschränkte Unterstützung für HTTP/2 unter Windows Server 2012 R2 und Windows 8.1. Die Unterstützung ist eingeschränkt, weil die Liste der unterstützten TLS-Verschlüsselungssammlungen unter diesen Betriebssystemen begrenzt ist. Zum Sichern von TLS-Verbindungen ist möglicherweise ein durch einen Elliptic Curve Digital Signature Algorithm (ECDSA) generiertes Zertifikat erforderlich.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [HTTP.sys](xref:fundamentals/servers/httpsys#http2-support)
  * Windows Server 2016/Windows 10 oder höher
  * Zielframework: Gilt nicht für HTTP.sys-Bereitstellungen.
* [IIS (Out-of-Process)](xref:host-and-deploy/iis/index#http2-support)
  * Windows Server 2016/Windows 10 oder höher, IIS 10 oder höher
  * Öffentlich zugängliche Edge-Server-Verbindungen verwenden HTTP/2, aber die Reverseproxyverbindung mit Kestrel verwendet HTTP/1.1.
  * Zielframework: Gilt nicht für Out-of-Process-Bereitstellungen von IIS.

::: moniker-end

Eine HTTP/2-Verbindung muss [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) und TLS 1.2 oder höher verwenden. Weitere Informationen finden Sie in den Themen, die Ihre Serverbereitstellungsszenarien betreffen.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
