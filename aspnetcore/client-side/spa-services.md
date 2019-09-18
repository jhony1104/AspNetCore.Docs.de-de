---
title: Verwenden Sie JavaScript-Dienste zum Erstellen von Single-Page-Anwendungen in ASP.net Core
author: scottaddie
description: Erfahren Sie mehr über die Vorteile der Verwendung von JavaScript-Diensten zum Erstellen einer Single-Page-Anwendung (Spa), die ASP.net Core unterstützt wird
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017
ms.date: 09/06/2019
uid: client-side/spa-services
ms.openlocfilehash: 7aff46f739239246191763e0590046b2d9995922
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080507"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a>Verwenden Sie JavaScript-Dienste zum Erstellen von Single-Page-Anwendungen in ASP.net Core

Durch [Scott Addie](https://github.com/scottaddie) und [Fiyaz Hasan](https://fiyazhasan.me/)

Eine Single-Page-Anwendung (SPA) ist ein Webanwendungstyp, der aufgrund seiner hohen Servicequalität und Leistungsstärke sehr beliebt ist. Das Integrieren von Client seitigen Spa-Frameworks oder-Bibliotheken, z. b. [Angular](https://angular.io/) oder [reagieren](https://facebook.github.io/react/), mit serverseitigen Frameworks wie ASP.net Core kann schwierig sein. JavaScript-Dienste wurden entwickelt, um die reibungslose Integration des Integrationsprozesses zu verringern. Hierdurch wird ein reibungsloser Betrieb verschiedener Client- und Servertechnologiestapel möglich.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> Die in diesem Artikel beschriebenen Funktionen sind ab ASP.net Core 3,0 veraltet. Im nuget-Paket " [Microsoft. aspnetcore. Spaservices. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) " steht ein einfacherer Integrationsmechanismus für Spa-Frameworks zur Verfügung. Weitere Informationen finden Sie unter [[Ankündigung] Obsoleting Microsoft. aspnetcore. Spaservices und Microsoft. aspnetcore. nodeservices](https://github.com/aspnet/AspNetCore/issues/12890).

::: moniker-end

## <a name="what-is-javascript-services"></a>Was sind JavaScript-Dienste?

JavaScript-Dienste sind eine Sammlung von Client seitigen Technologien für ASP.net Core. Das Ziel ist, die ASP.NET Core als Entwickler bevorzugte serverseitige Plattform zum Erstellen von SPAs zu positionieren.

JavaScript-Dienste bestehen aus zwei unterschiedlichen nuget-Paketen:

* [Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)
* [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)

Diese Pakete sind in den folgenden Szenarien nützlich:

* JavaScript auf dem Server ausführen.
* Ein SPA-Framework oder eine Bibliothek verwenden möchten
* Clientseitige Assets mit Webpack erstellen wollen

Der Schwerpunkt dieses Artikels liegt auf der Zuhilfenahme des SpaServices-Pakets.

## <a name="what-is-spaservices"></a>Was ist SpaServices

SpaServices wurde erstellt, damit Entwickler ASP.NET Core bevorzugt als serverseitige Plattform zum Erstellen von SPAs zu verwenden. Spaservices ist nicht für die Entwicklung von Spas mit ASP.net Core erforderlich und sperrt Entwickler nicht in ein bestimmtes Client Framework.

SpaServices bietet nützliche Infrastruktur wie z.B.:

* [Serverseitige prerendering](#server-side-prerendering)
* [Webpack-Dev-Middleware](#webpack-dev-middleware)
* [Der Austausch eines "Hot"](#hot-module-replacement)
* [Routing-Hilfsprogramme](#routing-helpers)

Diese Infrastrukturkomponenten beschleunigen sowohl den Entwicklungsworkflow als auch die Zusammenarbeit mit der Common Language Runtime-Umgebung. Die Komponenten können einzeln verwendet werden.

## <a name="prerequisites-for-using-spaservices"></a>Voraussetzungen für die Verwendung von SpaServices

Führen Sie die folgenden Schritte aus, um mit SpaServices zu arbeiten:

* [Node.js](https://nodejs.org/) (Version 6 oder höher) mit Npm

  * Führen Sie den folgenden Befehl über die Befehlszeile aus, um zu überprüfen, ob diese Komponenten installiert sind und gefunden werden können.

    ```console
    node -v && npm -v
    ```

  * Wenn die Bereitstellung auf einer Azure-Website erfolgt, ist&mdash;keine Aktion erforderlich, da Node. js installiert ist und in den Serverumgebungen verfügbar ist.

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * Unter Windows mit Visual Studio 2017 wird das SDK installiert, indem die Arbeitsauslastung für die Platt **Form übergreifende .net Core-Entwicklung** ausgewählt wird.

* [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet-Paket

## <a name="server-side-prerendering"></a>Serverseitige prerendering

Eine universelle Anwendung (auch bekannt als isomorphe Anwendung) ist eine JavaScript-Anwendung, die sowohl auf dem Server als auch auf dem Client ausgeführt werden kann. Angular, React und andere beliebte Frameworks bieten eine universelle Plattform für diesen Anwendungsentwicklungsstil. Die Idee dahinter ist, zunächst die Framework-Komponenten über Node.js auf dem Server zu rendern und dann weitere Ausführungen an den Client zu delegieren.

ASP.NET Core-[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro), bereitgestellt durch SpaServices, vereinfachen die Implementierung von serverseitigem Prerendering durch Bereitstellen und Aufrufen der JavaScript-Funktionen auf dem Server.

### <a name="server-side-prerendering-prerequisites"></a>Voraussetzungen für die Server seitige vorab Ausführung

Installieren Sie das NPM-Paket " [ASPNET-prerendering](https://www.npmjs.com/package/aspnet-prerendering) ":

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a>Konfiguration der Server seitigen Vorab Anmeldung

Die Taghilfsprogramme werden im Projekt über die Namespace-Registrierung in der *_ViewImports.cshtml*-Datei erkennbar gemacht:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

Diese Taghilfsprogramme abstrahieren die Feinheiten der direkten Kommunikation mit Low-Level-APIs durch die Nutzung einer HTML-ähnlichen Syntax in der Razor-Ansicht:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a>ASP-PreRender-Module-taghilfsprogramm

Das`asp-prerender-module`-Taghilfsprogramm aus dem vorherigen Beispiel führt *ClientApp/dist/main-server.js* auf dem Server über Node.js aus. Für die Verständlichkeit: Die *main-server.js*-Datei ist ein Artefakt der TypeScript-zu-JavaScript-Transpilation aus dem [Webpack](https://webpack.github.io/)-Buildprozess. Webpack definiert einen Einstiegspunktalias für `main-server`, dessen Abhängigkeitsdiagramm und Durchlauf bei der *ClientApp/boot-server.ts*-Datei beginnt:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

Im folgenden Angular-Beispiel nutzt die *ClientApp/boot-server.ts*-Datei die `createServerRenderer`-Funktion und den `RenderResult`-Typ des `aspnet-prerendering`-Npm-Pakets, um das Serverrendering über Node.js zu konfigurieren. Das für das Server-Prerendering bestimmte HTML-Markup wird an eine Auflösungsfunktion übergeben. die von einem stark typisierten `Promise`-Objekt von JavaScript umgeben ist. Die Wichtigkeit des `Promise`-Objekts liegt darin, dass es asynchron HTML-Markup zur Injektion auf der Seite in das DOM-Platzhalterelement bereitstellt.

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a>ASP-PreRender-Data-taghilfsprogramm

In Kombination mit dem `asp-prerender-module`-Taghilfsprogramm kann das `asp-prerender-data`-Taghilfsprogramm dazu verwendet werden, um die Kontextinformationen an das serverseitige JavaScript von der Razor-Ansicht zu übergeben. Das folgende Markup übergibt beispielsweise Benutzerdaten an das `main-server`-Modul:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

Das empfangene `UserName`-Argument wird mit dem integrierten JSON-Serialisierungsprogramm serialisiert und befindet sich im `params.data`-Objekt. Im folgenden Angular-Beispiel werden diese Daten verwendet, um eine persönliche Begrüßung mittels eines `h1`-Elementes zu erstellen:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

Eigenschaften Namen, die in taghilfsprogramme übertragen werden, werden mit der **PascalCase** -Notation dargestellt Im Gegensatz dazu stehen die gleichen Eigenschaftennamen, die im Javascript mit **CamelCase** dargestellt sind. Die Standardkonfiguration der JSON-Serialisierung ist für diesen Unterschied verantwortlich.

Aufbauend auf das vorherige Codebeispiel, können Daten vom Server an die Ansicht per Hydration der `globals`-Eigenschaft bereitgestellt werden, um diese an die `resolve`-Funktion weiterzureichen:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

Das im `globals`-Objekt definierte `postList`-Array wird an das globale `window`-Javascript-Objekt gebunden. Das Hoisting dieser Variablen in den globalen Bereich führt zur Deduplizierung von Aufwand, vor allem mit Bezug des Ladevorgangs der Daten einmal auf dem Server und erneut auf dem Client.

![Globale postList Variable Window-Objekt angefügt](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a>Webpack-Dev-Middleware

[Webpack-Dev-Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) bietet einen optimierten Entwicklungsworkflow, bei dem Webpack die Ressourcen bei Bedarf erstellt. Die Middleware kompiliert und stellt clientseitig benötigte Ressourcen automatisch bereit, wenn eine Seite im Browser geladen wird. Der alternative Ansatz ist Webpack über das Npm-Buildskript des Projektes manuell aufzurufen, wenn eine Drittanbieterabhängigkeit oder der benutzerdefinierte Code geändert wird. Ein Npm-Buildskript aus der *"Package.JSON"* -Datei ist im folgenden Beispiel dargestellt:

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a>Voraussetzungen für WebPack-Entwicklungs Middleware

Installieren Sie das NPM [-Paket ASPNET-WebPack](https://www.npmjs.com/package/aspnet-webpack) :

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a>WebPack dev-Middleware-Konfiguration

Die Webpack-Dev-Middleware wird registriert und in die HTTP-Aufrufpipeline über den folgenden Code in der `Configure`-Methode der *"Startup.cs"* -Datei implementiert:

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

Die `UseWebpackDevMiddleware`-Erweiterungsmethode muss über die `UseStaticFiles`-Erweiterungsmethode vor dem [Registrieren des Hostings statischer Dateien](xref:fundamentals/static-files) aufgerufen werden. Registrieren Sie die Middleware aus Sicherheitsgründen nur, wenn die app im Entwicklungsmodus ausgeführt wird.

Die `output.publicPath`-Eigenschaft der *webpack.config.js*-Datei schreibt der Middleware vor, das `dist`-Verzeichnis auf Änderungen zu überwachen:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a>Hot Module Replacement

Stellen Sie sich das Feature [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) von Webpack als Weiterentwicklung von [Webpack Dev Middleware](#webpack-dev-middleware) vor. HMR verfügt über die gleichen Vorteile, optimiert den Entwicklungsworkflow allerdings noch weiter, indem die automatische Aktualisierung der Seiteninhalte nach dem Kompilieren der Änderungen erfolgt. Verwechseln Sie dies nicht mit einer Aktualisierung des Browsers, die den aktuellen In-Memory-Status und die Debugsitzung der SPA beeinträchtigen würde. Es gibt einen Live-Link zwischen dem Webpack Dev Middleware-Dienst und dem Browser, was bedeutet, dass Änderungen an den Browser mithilfe von Push übertragen werden.

### <a name="hot-module-replacement-prerequisites"></a>Erforderliche Komponenten für das Hot Module

Installieren Sie das NPM-Paket " [WebPack-Hot-Middleware](https://www.npmjs.com/package/webpack-hot-middleware) ":

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a>Konfiguration des Hot Module-Austauschs

Die Komponente HMR muss in der MVC-HTTP-Aufrufpipeline in der `Configure`-Methode registriert werden:

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

Genau wie bei [Webpack Dev Middleware](#webpack-dev-middleware) muss die `UseWebpackDevMiddleware`-Erweiterungsmethode vor der `UseStaticFiles`-Erweiterungsmethode aufgerufen werden. Registrieren Sie die Middleware aus Sicherheitsgründen nur, wenn die app im Entwicklungsmodus ausgeführt wird.

Die *webpack.config.js*-Datei muss ein `plugins`-Array definieren, auch wenn es leer gelassen wird:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

Nach dem Laden der App im Browser zeigt die Registerkarte für die Konsole der Entwicklertools im Browser die Bestätigung der HMR-Aktivierung an:

!["Hot" verbundene Austausch eines Controllermoduls-Nachricht](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a>Routing-Hilfsprogramme

In den meisten ASP.net Core basierten Spas ist das Client seitige Routing neben Server seitigem Routing oft erwünscht. Die SPA- und MVC-Routingsysteme können ohne Störung unabhängig voneinander arbeiten. Es gibt jedoch einen Grenzfall, bei dem die Groß-/Kleinschreibung zur Herausforderung wird, und zwar beim Identifizieren von 404-HTTP-Antworten.

Betrachten Sie das Szenario, in dem eine Route ohne Erweiterung, `/some/page`, verwendet wird. Angenommen, die Anforderung stimmt nicht mit dem Muster eines serverseitigen Pfades überein, jedoch mit einer clientseitigen Route. Betrachten Sie nun eine eingehende Anforderung für `/images/user-512.png`, die in der Regel davon ausgeht, eine Bilddatei auf dem Server zu finden. Wenn der angeforderte Ressourcen Pfad keiner serverseitigen Route oder statischen Datei entspricht, ist es unwahrscheinlich, dass die Client seitige Anwendung diese verarbeitet, wenn&mdash;Sie in der Regel den HTTP-Statuscode 404 zurückgibt.

### <a name="routing-helpers-prerequisites"></a>Voraussetzungen für Routing Hilfsprogramme

Installieren Sie das NPM-Paket Client seitiges Routing. Verwenden Sie Angular als Beispiel:

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a>Konfiguration der Routing Hilfsprogramme

Eine Erweiterungsmethode namens `MapSpaFallbackRoute` wird in der `Configure`-Methode verwendet:

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

Routen werden in der Reihenfolge ausgewertet, in der Sie konfiguriert sind. Daher wird die `default`-Route im obigen Codebeispiel zuerst für den Musterabgleich verwendet.

## <a name="create-a-new-project"></a>Erstellt ein neues Projekt

JavaScript-Dienste stellen vorkonfigurierte Anwendungs Vorlagen bereit. Spaservices wird in diesen Vorlagen zusammen mit verschiedenen Frameworks und Bibliotheken wie Angular, reagieren und Redux verwendet.

Diese Vorlagen können über die .NET Core-CLI mithilfe des folgenden Befehls installiert werden:

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

Eine Liste der verfügbaren SPA-Vorlagen wird angezeigt:

| Vorlagen                                 | Kurzname | Sprache | Tags        |
| ------------------------------------------| :--------: | :------: | :---------: |
| MVC, ASP.NET Core mit Angular             | angular    | [C#]     | MVC/Web/SPA |
| MVC, ASP.NET Core mit React.js            | react      | [C#]     | MVC/Web/SPA |
| MVC, ASP.NET Core mit React.js und Redux  | reactredux | [C#]     | MVC/Web/SPA |

Schließen Sie zum Erstellen eines neuen Projekts mithilfe einer der SPA-Vorlagen den **Kurznamen** der Vorlage in den Befehl [dotnet new](/dotnet/core/tools/dotnet-new) ein. Der folgende Befehl erstellt eine Angular-Anwendung mit ASP.NET Core MVC, die für die serverseitige Verwendung konfiguriert ist:

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a>Festlegen des Laufzeitkonfigurationsmodus

Es sind zwei Modi der primären Laufzeitkonfiguration vorhanden:

* **Entwicklung**:
  * Enthält Quellzuordnungen zur Erleichterung des Debuggings.
  * Der clientseitige Code wird nicht für Leistung optimiert.
* **Produktion**:
  * Schließt Quellzuordnungen aus.
  * Optimiert den Client seitigen Code durch Bündelung und Minimierung.

ASP.NET Core verwendet eine Umgebungsvariable namens `ASPNETCORE_ENVIRONMENT` zum Speichern des Konfigurationsmodus. Weitere Informationen finden Sie unter [Festlegen der Umgebung](xref:fundamentals/environments#set-the-environment).

### <a name="run-with-net-core-cli"></a>Mit .net Core-CLI ausführen

Stellen Sie die erforderlichen NuGet und Npm-Pakete mithilfe des folgenden Befehls auf das Stammverzeichnis des Projekts wieder her:

```dotnetcli
dotnet restore && npm i
```

Erstellen und Ausführen der Anwendung:

```dotnetcli
dotnet run
```

Die Anwendung wird auf dem Localhost gemäß des [Laufzeitkonfigurationsmodus](#set-the-runtime-configuration-mode) gestartet. Navigieren Sie zu `http://localhost:5000` im Browser, wo die Landing Page angezeigt wird.

### <a name="run-with-visual-studio-2017"></a>Ausführen mit Visual Studio 2017

Öffnen Sie die *csproj*-Datei, die mittels des [dotnet new](/dotnet/core/tools/dotnet-new)-Befehls erzeugt wurde. Die erforderlichen NuGet- und npm-Pakete werden automatisch beim Projektstart wiederhergestellt. Dieser Wiederherstellungsprozess kann einige Minuten dauern, und die Anwendung kann ausgeführt werden, wenn der Vorgang abgeschlossen ist. Klicken Sie auf die grüne Schaltfläche "Ausführen", oder drücken Sie `Ctrl + F5`, und der Browser zur Landing Page der Anwendung wird geöffnet . Die Anwendung wird auf dem Localhost gemäß des [Laufzeitkonfigurationsmodus](#set-the-runtime-configuration-mode) ausgeführt.

## <a name="test-the-app"></a>Testen der App

SpaServices-Vorlagen sind so vorkonfiguriert, dass sie clientseitige Tests mithilfe von [Karma](https://karma-runner.github.io/1.0/index.html) und [Jasmine](https://jasmine.github.io/) entwickeln können. Jasmine ist ein beliebtes Komponententest-Framework für JavaScript, während Karma ein Test Runner für diese Tests ist. Karma ist so konfiguriert, dass mit der [Webpack Dev Middleware](#webpack-dev-middleware) so zusammen gearbeitet wird, dass der Entwickler Tests nicht jedes Mal anhalten und wieder ausführen muss, wenn Änderungen vorgenommen werden. Der Test wird automatisch ausgeführt, egal ob der Code für den Testfall ausgeführt wird oder der Testfall selbst.

Verwenden Sie als Beispiel die Angular-Anwendung, in der bereits zwei Jasmine-Testfälle für `CounterComponent` in der *counter.component.spec.ts*-Datei bereit stehen:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

Öffnen Sie die Eingabeaufforderung im *ClientApp*-Verzeichnis. Führen Sie den folgenden Befehl aus:

```console
npm test
```

Das Skript startet den Karma-Test Runner, der die in der *karma.conf.js*-Datei definierten Einstellungen liest. Neben anderen Einstellungen identifiziert die *karma.conf.js*-Datei die Testdateien, die über das `files`-Array ausgeführt werden sollen:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a>Veröffentlichen der App

Es kann aufwendig sein, die generierten clientseitigen Ressourcen und die veröffentlichten ASP.NET Core-Artefakte in einem Paket, das bereitgestellt werden kann, zu kombinieren. Glücklicherweise orchestriert SpaServices diesen gesamten Veröffentlichungsprozess mit einem benutzerdefinierten MSBuild-Ziel mit dem Namen `RunWebpack`:

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

Das MSBuild-Ziel hat die folgenden Verantwortungen:

1. Stellen Sie die NPM-Pakete wieder her.
1. Erstellen Sie einen buildbasierten Build der Client seitigen Ressourcen von Drittanbietern.
1. Erstellen Sie einen Build der benutzerdefinierten Client seitigen Assets in der Produktionsumgebung.
1. Kopieren Sie die vom Webpaket generierten Assets in den Ordner Publish.

Das MSBuild-Ziel wird aufgerufen, wenn Folgendes ausgeführt wird:

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Angular-Docs](https://angular.io/docs)
