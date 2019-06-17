---
title: ASP.NET Core – Grundlagen
author: rick-anderson
description: Lernen Sie die grundlegenden Konzepte zum Erstellen von ASP.NET Core-Apps kennen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: fundamentals/index
ms.openlocfilehash: 3cf311f8e6be4ed12c79ceecc15ccc1babfb0117
ms.sourcegitcommit: 335a88c1b6e7f0caa8a3a27db57c56664d676d34
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/12/2019
ms.locfileid: "67034857"
---
# <a name="aspnet-core-fundamentals"></a>ASP.NET Core – Grundlagen

In diesem Artikel finden Sie eine Übersicht über die wichtigsten Themen zum Entwickeln von ASP.NET Core-Apps.

## <a name="the-startup-class"></a>Die Startup-Klasse

In der `Startup`-Klasse:

* werden die von der App erforderlichen Dienste konfiguriert.
* wird die Pipeline zur Anforderungsverarbeitung definiert.

*Dienste* sind Komponenten, die von der App verwendet werden. Eine Protokollierungskomponente stellt beispielsweise einen Dienst dar. wird Code, der Dienste konfiguriert (oder *registriert*) der `Startup.ConfigureServices`-Methode hinzugefügt.

Die Pipeline zur Anforderungsverarbeitung besteht aus mehreren *Middlewarekomponenten*. Eine Middleware kann beispielsweise Anforderungen für statische Dateien verarbeiten oder HTTP-Anforderungen zu HTTPS umleiten. Jede Middleware führt asynchrone Operationen in einem `HttpContext` aus und ruft anschließend entweder die nächste Middleware in der Pipeline auf oder beendet die Anforderung. Code zum Konfigurieren der Pipeline zur Anforderungsverarbeitung wird der `Startup.Configure`-Methode hinzugefügt.

Beispiel für eine `Startup`-Klasse:

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

Weitere Informationen finden Sie unter <xref:fundamentals/startup>.

## <a name="dependency-injection-services"></a>Abhängigkeitsinjektion (Dienste)

ASP.NET Core verfügt über ein integriertes DI-Framework (Dependency Injection), durch das konfigurierte Dienste für die Klassen einer App bereitgestellt werden. Eine Möglichkeit zum Abrufen einer Instanz eines Diensts in einer Klasse ist das Erstellen eines Konstruktors mit einem Parameter des erforderlichen Typs. Der Parameter kann der Diensttyp oder eine Schnittstelle sein. Das DI-System stellt den Dienst zur Laufzeit bereit.

Hier ist eine Klasse, in der Dependency Injection verwendet wird, um ein Entity Framework Core-Kontextobjekt abzurufen. Die hervorgehobene Zeile ist ein Beispiel für die Konstruktorinjektion:

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

Obwohl die Dependency Injection integriert ist, können Sie hier, falls gewünscht, einen IoC-Container eines Drittanbieters einbinden.

Weitere Informationen finden Sie unter <xref:fundamentals/dependency-injection>.

## <a name="middleware"></a>Middleware

Die Pipeline zur Anforderungsverarbeitung besteht aus mehreren Middlewarekomponenten. Jede Komponente führt asynchrone Operationen in einem `HttpContext` aus und ruft anschließend entweder die nächste Middleware in der Pipeline auf oder beendet die Anforderung.

Gemäß Konvention wird eine Middlewarekomponente der Pipeline durch Aufrufen ihrer `Use...`-Erweiterungsmethode in der `Startup.Configure`-Methode hinzugefügt. Um beispielsweise das Rendering statischer Dateien zu aktivieren, rufen Sie `UseStaticFiles` auf.

Der hervorgehobene Code in dem folgenden Beispiel konfiguriert die Pipeline zur Anforderungsverarbeitung:

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

ASP.NET Core enthält eine Reihe umfangreicher integrierter Middleware. Außerdem können Sie benutzerdefinierte Middleware erstellen.

Weitere Informationen finden Sie unter <xref:fundamentals/middleware/index>.

## <a name="host"></a>Host

Beim Starten erstellt eine ASP.NET Core-App einen *Host*. Der Host ist ein Objekt, das alle Ressourcen der App kapselt, z. B.:

* eine HTTP-Serverimplementierung
* Middlewarekomponenten
* Protokollierung
* DI
* Konfiguration

Der wichtigste Grund für das Einschließen aller unabhängigen Ressourcen der App in einem Objekt ist die Verwaltung der Lebensdauer: steuern von Start und ordnungsgemäßem Herunterfahren der App.

::: moniker range=">= aspnetcore-3.0"

Es stehen zwei Hosts zur Verfügung: der generische Host und der Webhost. Der generische Host wird empfohlen. Der Webhost ist nur für die Abwärtskompatibilität verfügbar.

Der Code zum Erstellen eines Hosts befindet sich in `Program.Main`:

[!code-csharp[](index/snapshots/3.x/Program1.cs)]

Die Methoden `CreateDefaultBuilder` und `ConfigureWebHostDefaults` konfigurieren einen Host mit häufig verwendeten Optionen wie den folgenden:

* Verwenden von [Kestrel](#servers) als Webserver, und aktivieren der Integration von Internetinformationsdiensten.
* Laden der Konfiguration aus *appsettings.json*, *appsettings.[EnvironmentName].json*, Umgebungsvariablen, Befehlszeilenargumenten und anderen Konfigurationsquellen.
* Senden von Protokollausgaben an die Konsole und Debuggen von Anbietern.

Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Es stehen zwei Hosts zur Verfügung: der Webhost und der generische Host. In ASP.NET Core 2.x ist der generische Host nur für nicht webbasierte Szenarios verfügbar.

Der Code zum Erstellen eines Hosts befindet sich in `Program.Main`:

[!code-csharp[](index/snapshots/2.x/Program1.cs)]

Die Methode `CreateDefaultBuilder` konfiguriert einen Host mit häufig verwendeten Optionen wie den folgenden:

* Verwenden von [Kestrel](#servers) als Webserver, und aktivieren der Integration von Internetinformationsdiensten.
* Laden der Konfiguration aus *appsettings.json*, *appsettings.[EnvironmentName].json*, Umgebungsvariablen, Befehlszeilenargumenten und anderen Konfigurationsquellen.
* Senden von Protokollausgaben an die Konsole und Debuggen von Anbietern.

Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host>.

::: moniker-end

### <a name="non-web-scenarios"></a>Nicht webbasierte Szenarios

Mit dem generischen Host können andere App-Typen querschnittliche Frameworkerweiterungen wie Protokollierung, Dependency Injection (DI), Konfiguration und Lebensdauerverwaltung der App verwenden. Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host> und <xref:fundamentals/host/hosted-services>.

## <a name="servers"></a>Server

Eine ASP.NET Core-App verwendet eine HTTP-Serverimplementierung zum Lauschen auf HTTP-Anforderungen. Der Server sendet Anforderungen an die App in Form von mehreren [Anforderungsfunktionen](xref:fundamentals/request-features) in einem `HttpContext`.

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Die folgenden Serverimplementierungen werden von ASP.NET Core bereitgestellt:

* *Kestrel* ist ein plattformübergreifender Webserver. Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [IIS](https://www.iis.net/) ausgeführt. In ASP.NET Core 2.0 oder höher kann Kestrel als öffentlich zugänglicher Edge-Server ausgeführt werden, der direkt mit dem Internet verknüpft ist.
* Der *IIS-HTTP-Server* ist ein Server für Windows, der IIS verwendet. Mit diesem Server werden die ASP.NET Core-App und IIS im gleichen Prozess ausgeführt.
* *HTTP.sys* ist ein Server für Windows, der nicht mit IIS verwendet wird.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

ASP.NET Core bietet die plattformübergreifende Serverimplementierung von *Kestrel*. In ASP.NET Core 2.0 oder höher kann Kestrel als öffentlich zugänglicher Edge-Server ausgeführt werden, der direkt mit dem Internet verknüpft ist. Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

ASP.NET Core bietet die plattformübergreifende Serverimplementierung von *Kestrel*. In ASP.NET Core 2.0 oder höher kann Kestrel als öffentlich zugänglicher Edge-Server ausgeführt werden, der direkt mit dem Internet verknüpft ist. Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Die folgenden Serverimplementierungen werden von ASP.NET Core bereitgestellt:

* *Kestrel* ist ein plattformübergreifender Webserver. Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [IIS](https://www.iis.net/) ausgeführt. In ASP.NET Core 2.0 oder höher kann Kestrel als öffentlich zugänglicher Edge-Server ausgeführt werden, der direkt mit dem Internet verknüpft ist.
* *HTTP.sys* ist ein Server für Windows, der nicht mit IIS verwendet wird.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

ASP.NET Core bietet die plattformübergreifende Serverimplementierung von *Kestrel*. In ASP.NET Core 2.0 oder höher kann Kestrel als öffentlich zugänglicher Edge-Server ausgeführt werden, der direkt mit dem Internet verknüpft ist. Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

ASP.NET Core bietet die plattformübergreifende Serverimplementierung von *Kestrel*. In ASP.NET Core 2.0 oder höher kann Kestrel als öffentlich zugänglicher Edge-Server ausgeführt werden, der direkt mit dem Internet verknüpft ist. Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](http://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.

---

::: moniker-end

Weitere Informationen finden Sie unter <xref:fundamentals/servers/index>.

## <a name="configuration"></a>Konfiguration

ASP.NET Core bietet ein Konfigurationsframework, das Einstellungen als Name/Wert-Paare aus einer geordneten Menge von Konfigurationsanbietern abruft. Integrierte Konfigurationsanbieter gibt es für zahlreiche Quellen wie *.json*-Dateien, *.xml*-Dateien, Umgebungsvariablen und Befehlszeilenargumente. Sie können auch benutzerdefinierte Konfigurationsanbieter schreiben.

Zum Beispiel könnten Sie angeben, dass die Konfiguration aus *appsettings.json* und Umgebungsvariablen stammt. Wenn dann der *ConnectionString*-Wert angefordert wird, sucht das Framework zuerst in der *appsettings.json*-Datei. Wenn der Wert sowohl dort als auch in einer Umgebungsvariablen gefunden wird, hat der Wert aus der Umgebungsvariablen Vorrang.

Zum Verwalten von vertraulichen Konfigurationsdaten wie Passwörtern bietet ASP.NET Core ein [Geheimnisverwaltungstool](xref:security/app-secrets). Für Produktionsgeheimnisse empfehlen wir [Azure Key Vault](xref:security/key-vault-configuration).

Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.

## <a name="options"></a>Optionen

Wo möglich, folgt ASP.NET Core dem *Optionsmuster* zum Speichern und Abrufen von Konfigurationswerten. Das Optionsmuster verwendet Klassen, um Gruppen von zusammengehörigen Einstellungen darzustellen.

Im folgenden Codebeispiel werden WebSockets-Optionen festgelegt:

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

Weitere Informationen finden Sie unter <xref:fundamentals/configuration/options>.

## <a name="environments"></a>Umgebungen

Ausführungsumgebungen wie *Entwicklung*, *Staging* und *Produktion* sind in ASP.NET Core von besonderer Bedeutung. Um die Umgebung anzugeben, in der eine App ausgeführt wird, stellen Sie die `ASPNETCORE_ENVIRONMENT`-Umgebungsvariable ein. ASP.NET Core liest diese Umgebungsvariable beim Start der App und speichert den Wert in einer `IHostingEnvironment`-Implementierung. Das Umgebungsobjekt ist in der gesamten App mithilfe der Dependency Injection verfügbar.

Im folgenden Codebeispiel aus der `Startup`-Klasse wird die App so konfiguriert, dass detaillierte Fehlerinformationen nur bereitgestellt werden, wenn die App in der Entwicklung ausgeführt wird:

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

Weitere Informationen finden Sie unter <xref:fundamentals/environments>.

## <a name="logging"></a>Protokollierung

ASP.NET Core unterstützt eine Protokollierungs-API, die mit einer Vielzahl von integrierten Protokollierungsanbietern und Drittanbieter-Protokollierungslösungen zusammenarbeitet. Zu den verfügbaren Anbietern gehören folgende:

* Konsole
* Debug
* Ereignisablaufverfolgung unter Windows
* Windows-Ereignisprotokoll
* TraceSource
* Azure App Service
* Azure Application Insights

Schreiben Sie Protokolle aus jeder Stelle im Code einer App, indem Sie ein `ILogger`-Objekt aus Dependency Injection abrufen und Protokollmethoden aufrufen.

Hier ist ein Beispielcode, in dem ein `ILogger`-Objekt mit Konstruktorinjektion und den hervorgehobenen Aufrufen der Protokollierungsmethode verwendet wird.

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

Mithilfe der `ILogger`-Schnittstelle können Sie eine beliebige Anzahl von Feldern an den Protokollierungsanbieter übergeben. Die Felder werden häufig dazu verwendet, eine Meldungszeichenfolge zu erstellen, der Anbieter kann sie aber auch als einzelne Felder an einen Datenspeicher senden. Dieses Funktion ermöglicht Protokollierungsanbietern das Implementieren von [semantischer Protokollierung, auch bezeichnet als strukturierte Protokollierung](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Weitere Informationen finden Sie unter <xref:fundamentals/logging/index>.

## <a name="routing"></a>Routing

Eine *Route* ist ein URL-Muster, das einem Handler zugeordnet ist. Der Handler ist normalerweise eine Razor-Seite, eine Aktionsmethode in einem MVC-Controller oder einer Middleware. Mit ASP.NET Core-Routing können Sie steuern, welche URLs von Ihrer App verwendet werden.

Weitere Informationen finden Sie unter <xref:fundamentals/routing>.

## <a name="error-handling"></a>Fehlerbehandlung

ASP.NET Core verfügt über integrierte Funktionen zur Fehlerbehandlung wie beispielsweise:

* eine Seite mit Ausnahmen für Entwickler
* benutzerdefinierte Fehlerseiten
* statische Statuscodeseiten
* Fehlerbehandlung während des Starts

Weitere Informationen finden Sie unter <xref:fundamentals/error-handling>.

## <a name="make-http-requests"></a>Übermitteln von HTTP-Anforderungen

Eine Implementierung von `IHttpClientFactory` ist verfügbar zum Erstellen von `HttpClient`-Instanzen. Die Factory:

* Ein zentraler Ort für das Benennen und Konfigurieren logischer `HttpClient`-Instanzen wird damit geboten. Zum Beispiel kann ein *github*-Client für den Zugriff auf GitHub registriert und konfiguriert werden. Ein Standard-Client kann für andere Zwecke registriert werden.
* Unterstützt die Registrierung und Verkettung von mehreren delegierenden Handlern, um eine Pipeline für die Middleware für ausgehende Anforderungen zu erstellen. Dieses Muster ähnelt der eingehenden Pipeline für Middleware in ASP.NET Core. Das Muster bietet einen Mechanismus zum Verwalten von übergreifenden Belangen bezüglich HTTP-Anforderungen, einschließlich der Zwischenspeicherung, Fehlerbehandlung, Serialisierung und Protokollierung.
* Integrierbar in *Polly*, eine beliebte Drittanbieter-Bibliothek zur Behandlung vorübergehender Fehler.
* Das Pooling und die Lebensdauer von zugrunde liegenden `HttpClientMessageHandler`-Instanzen werden verwaltet, um gängige DNS-Probleme zu vermeiden, die bei der manuellen Verwaltung der `HttpClient`-Lebensdauer auftreten.
* Eine konfigurierbare Protokollierungsfunktion wird (über `ILogger`) für alle Anforderungen hinzugefügt, die über Clients gesendet werden, die von der Factory erstellt wurden.

Weitere Informationen finden Sie unter <xref:fundamentals/http-requests>.

## <a name="content-root"></a>Inhaltsstammverzeichnis

Der Inhaltsstamm ist der Basispfad zu einem beliebigen von der App verwendeten privaten Inhalt wie beispielsweise ihren Razor-Dateien. Standardmäßig entspricht der Inhaltsstamm dem Basispfad der ausführbaren Datei, mit der die Anwendung gehostet wird. Ein alternativer Speicherort kann beim [Erstellen des Hosts](#host) angegeben werden.

::: moniker range=">= aspnetcore-3.0"

Weitere Informationen finden Sie unter [Inhaltsstamm](xref:fundamentals/host/generic-host#content-root).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Weitere Informationen finden Sie unter [Inhaltsstamm](xref:fundamentals/host/web-host#content-root).

::: moniker-end

## <a name="web-root"></a>Webstammverzeichnis

Der Webstamm (auch bekannt als *webroot*) ist der Basispfad zu öffentlichen statischen Ressourcen wie CSS, JavaScript und Bilddateien. Die Middleware für statische Dateien stellt standardmäßig nur Dateien aus dem Webstammverzeichnis (und Unterverzeichnissen) bereit. Der Pfad für den Webstamm ist standardmäßig auf *{Content Root}/wwwroot* festgelegt, beim [Erstellen des Hosts](#host) kann jedoch ein anderer Speicherort angegeben werden.

In Razor-Dateien ( *.cshtml*) zeigen die Tilde und der Schrägstrich `~/` auf den Webstamm. Pfade, die mit `~/` beginnen, werden als virtuelle Pfade bezeichnet.

Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.
