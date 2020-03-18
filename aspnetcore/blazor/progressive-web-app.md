---
title: Erstellen progressiver Webanwendungen mit der Blazor WebAssembly für ASP.NET Core
author: guardrex
description: Erfahren Sie, wie Sie Blazor-basierte progressive Webanwendungen (PWAs) erstellen. Dies sind Web-Apps, die mithilfe moderner Browserfeatures über das gleiche Verhalten wie Desktop-Apps verfügen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: blazor/progressive-web-app
ms.openlocfilehash: f1c1ce50f20bf579e67e1d4eb02cc7d9d681e354
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083560"
---
# <a name="build-progressive-web-applications-with-aspnet-core-opno-locblazor-webassembly"></a>Erstellen progressiver Webanwendungen mit der Blazor WebAssembly für ASP.NET Core

Von [Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Eine progressive Webanwendung (PWA) ist eine webbasierte Anwendung, die mithilfe moderner Browser-APIs und -Funktionen das gleiche Verhalten wie eine Desktopanwendung aufweist. Dabei kann es sich um die folgenden Funktionen handeln:

* Die Fähigkeit, offline zu arbeiten, und sofortiges Laden, unabhängig von der Netzwerkgeschwindigkeit
* Ausführung in einem eigenen Anwendungsfenster statt lediglich in einem Browserfenster
* Starten über das Startmenü, das Dock oder den Startbildschirm des Hostbetriebssystems
* Empfangen von Pushbenachrichtigungen von einem Back-End-Server, auch wenn der Benutzer die Anwendung nicht verwendet
* Automatisches Aktualisieren im Hintergrund

Benutzer können die Anwendung zunächst wie jede andere Single-Page-Anwendung (Single-Page Application, SPA) im eigenen Webbrowser entdecken und verwenden und später im Betriebssystem installieren und Pushbenachrichtigungen aktivieren. Daher wird für sie der Begriff *progressiv* verwendet.

Die Blazor WebAssembly ist eine auf Standards basierende clientseitige Webanwendungsplattform. Sie kann daher jede beliebige Browser-API verwenden, einschließlich der PWA-APIs, die für die oben aufgeführten Funktionen erforderlich sind. Sie kann wie jede andere clientseitige Webtechnologie offline ausgeführt werden.

## <a name="pwa-template"></a>PWA-Vorlage

Wenn Sie eine neue Blazor WebAssembly-Anwendung erstellen, haben Sie die Möglichkeit, PWA-Features hinzuzufügen. In Visual Studio ist für diese Option ein Kontrollkästchen im Dialogfeld für die Projekterstellung vorhanden:

![Bild](https://user-images.githubusercontent.com/1101362/76207411-a6b54200-61f5-11ea-9dfc-6acd87a91428.png)

Wenn Sie das Projekt in der Befehlszeile erstellen, können Sie das `--pwa`-Flag verwenden. Ein auf ein Objekt angewendeter

```dotnetcli
dotnet new blazorwasm --pwa -o MyNewProject
```

In beiden Fällen können Sie diese Option bei Bedarf mit der Option „Von ASP.NET Core gehostet“ kombinieren. PWA-Features sind unabhängig vom Hostingmodell.

## <a name="installation-and-app-manifest"></a>Installation und App-Manifest

Wenn Benutzer eine Anwendung aufrufen, die mithilfe der PWA-Vorlagenoption erstellt wurde, können sie die Anwendung im Startmenü, Dock oder Startbildschirm des Betriebssystems installieren.

Die Darstellung dieser Option hängt vom Browser des Benutzers ab. Wenn z. B. Chromium-basierte Desktopbrowser wie Edge oder Chrome verwendet werden, wird in der URL-Leiste eine Schaltfläche zum *Hinzufügen* angezeigt:

![Bild](https://user-images.githubusercontent.com/1101362/76208127-f7796a80-61f6-11ea-8aea-7fba704be787.png)

Unter iOS können Besucher die PWA mithilfe der Schaltfläche *Teilen* von Safari und der zugehörigen Option *Zum Home-Bildschirm* installieren. In Chrome für Android sollten Benutzer auf die Schaltfläche *Menü* in der oberen rechten Ecke tippen und dann *Zum Startbildschirm hinzufügen* auswählen.

Nach der Installation wird die Anwendung in einem eigenen Fenster ohne Adressleiste angezeigt.

![Bild](https://user-images.githubusercontent.com/1101362/76208588-e2e9a200-61f7-11ea-85e1-8c3fc849b252.png)

Informationen zum Anpassen des Titels, des Farbschemas, des Symbols oder anderer Details des Fensters finden Sie in der Datei `manifest.json` im Verzeichnis *wwwroot* des Projekts. Das Schema dieser Datei wird durch Webstandards definiert. Eine ausführliche Dokumentation finden Sie unter https://developer.mozilla.org/docs/Web/Manifest.

## <a name="offline-support"></a>Offlineunterstützung

Die Offlineausführung von Anwendungen, die mit der PWA-Vorlagenoption erstellt wurden, wird standardmäßig unterstützt. Der Benutzer muss zunächst die Anwendung öffnen, während er offline ist. Anschließend werden alle Ressourcen, die für die Offlineausführung erforderlich sind, automatisch vom Browser heruntergeladen und zwischengespeichert.

> [!IMPORTANT]
> Die Offlineunterstützung ist nur für *veröffentlichte* Anwendungen aktiviert. Während der Entwicklung ist sie nicht aktiviert. Der Grund dafür ist, dass die Offlineausführung den üblichen Entwicklungszyklus, der das Vornehmen und Testen von Änderungen umfasst, beeinträchtigen würde.

> [!WARNING]
> Wenn Sie eine offlinefähige PWA liefern möchten, müssen Sie [einige wichtige Warnungen und Einschränkungen](#caveats-for-offline-pwas) beachten. Diese gelten allgemein für Offline-PWAs und sind nicht auf Blazor beschränkt. Sie müssen diese Einschränkungen lesen und verstehen, bevor Sie Vermutungen über die Funktionsweise der offlinefähigen Anwendung anstellen.

Um die Funktionsweise der Offlineunterstützung zu ermitteln, [veröffentlichen Sie zunächst die Anwendung](https://docs.microsoft.com/aspnet/core/host-and-deploy/blazor/?view=aspnetcore-3.1&tabs=visual-studio#publish-the-app), und hosten Sie sie auf einem Server, der HTTPS unterstützt. Wenn Sie die Anwendung besuchen, können Sie die Entwicklertools des Browsers öffnen und überprüfen, ob ein *Service Worker* für den Host registriert ist:

![Bild](https://user-images.githubusercontent.com/1101362/76210294-bd5e9780-61fb-11ea-9869-65c55c62803d.png)

Wenn Sie die Seite erneut laden, können Sie zudem auf der Registerkarte *Netzwerk* erkennen, dass alle zum Laden der Seite erforderlichen Ressourcen aus dem *Service Worker* oder *Cache* abgerufen werden:

![Bild](https://user-images.githubusercontent.com/1101362/76210472-1d553e00-61fc-11ea-84c6-291644df709e.png)

Daraus geht hervor, dass zum Laden der Anwendung durch den Browser kein Netzwerkzugriff erforderlich ist. Um dies zu überprüfen, können Sie entweder den Webserver herunterfahren oder den Browser anweisen, den Offlinemodus zu simulieren:

![Bild](https://user-images.githubusercontent.com/1101362/76210556-47a6fb80-61fc-11ea-9d12-20a8f6528744.png)

Sie können die Seite jetzt auch ohne Zugriff auf den Webserver erneut laden und erkennen, dass die Anwendung weiterhin geladen und ausgeführt wird. Auch wenn Sie eine sehr langsame Netzwerkverbindung simulieren, wird die Seite trotzdem sofort geladen, da sie unabhängig vom Netzwerk geladen wird.

### <a name="service-worker"></a>Service Worker

Die Offlineunterstützung erfolgt mit einem Service Worker. Dies ist ein Webstandard, der nicht auf Blazor beschränkt ist. Eine Dokumentation zu Service Workers finden Sie unter https://developer.mozilla.org/docs/Web/API/Service_Worker_API. Weitere Informationen zu häufigen Verwendungsmustern für Service Worker finden Sie im Artikel [The Service Worker Lifecycle](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle) (Der Lebenszyklus der Service Worker, in englischer Sprache).

Die PWA-Vorlage von Blazor erzeugt zwei Service-Worker-Dateien:

* *wwwroot/service-worker.js*; diese wird während der Entwicklung verwendet.
* *wwwroot/service-worker.published.js*; diese wird nach dem Veröffentlichen der Anwendung verwendet.

Wenn Sie die Logik für diesen beiden Dateien gemeinsam nutzen möchten, können Sie eine dritte JavaScript-Datei hinzufügen, in der die gemeinsame Logik enthalten ist, und mithilfe von [`self.importScripts`](https://developer.mozilla.org/docs/Web/API/WorkerGlobalScope/importScripts) diese Logik in beide Dateien laden.

#### <a name="cache-first-fetch-strategy"></a>Cache-First-Abrufstrategie

Der integrierte Service Worker *service-worker.published.js* löst Anforderungen mit einer *Cache-First*-Strategie auf. Dies bedeutet, dass er immer zwischengespeicherte Inhalte zurückgibt, sofern verfügbar, unabhängig davon, ob der Benutzer über Netzwerkzugriff verfügt oder auf dem Server neuere Inhalte verfügbar sind.

Dies ist aus zwei Gründen vorteilhaft:

* **Die Zuverlässigkeit wird sichergestellt.** Der Netzwerkzugriff ist kein boolescher Zustand. Ein Benutzer ist nicht einfach online oder offline. In der Praxis wird auf dem Gerät des Benutzers möglicherweise angezeigt, dass es online ist, die Netzwerkverbindung ist jedoch eventuell so langsam, dass sie nicht zu verwenden ist. Oder das Netzwerk gibt möglicherweise für bestimmte URLs ungültige Ergebnisse zurück, z. B. wenn ein Captive-WLAN-Portal derzeit bestimmte Anforderungen blockiert oder umleitet. Aus diesem Grund ist die `navigator.onLine`-API des Browsers nicht zuverlässig, und Sie sollten nicht auf sie vertrauen.
* **Die Richtigkeit wird sichergestellt.** Beim Erstellen eines Caches für Offlineressourcen stellt der Service Worker mithilfe von Inhaltshashs sicher, dass er zu einem bestimmten Zeitpunkt eine vollständige und in sich konsistente Momentaufnahme der Ressourcen abgerufen hat. Dieser Cache wird dann als atomische Einheit verwendet. Deshalb macht es keinen Sinn, vom Netzwerk neuere Ressourcen anzufordern, da Sie nur die Versionen wünschen, die bereits zwischengespeichert sind. Alle anderen Strategien sind mit dem Risiko von Inkonsistenz und Inkompatibilität behaftet (wenn z. B. versucht wird, Versionen von .NET-Assemblys zu verwenden, die nicht gemeinsam kompiliert wurden).

#### <a name="background-updates"></a>Hintergrundupdates

Sie können sich das Verhalten einer Offline-First-PWA wie das einer mobilen App vorstellen, die installiert werden kann. Sie wird unabhängig von der Netzwerkkonnektivität immer sofort gestartet, die installierte Anwendungslogik stammt jedoch aus einer Zeitpunkt-Momentaufnahme, die möglicherweise nicht die neueste Version ist.

Mit der Blazor-PWA-Vorlage werden Anwendungen erstellt, die immer automatisch versuchen, sich im Hintergrund zu aktualisieren, wenn sie vom Benutzer besucht werden und eine funktionsfähige Netzwerkverbindung besteht. Dies geschieht auf folgende Weise:

* Das Projekt generiert während der Kompilierung ein *Service-Worker-Assetmanifest*. Sein Standardname lautet *service-worker-assets.js*. In ihm werden alle statischen Ressourcen aufgelistet, die von der Anwendung für die Offlineausführung benötigt werden, z. B. .NET-Assemblys, JavaScript-Dateien, CSS usw., einschließlich ihrer Inhaltshashs. Diese Liste wird vom Service Worker geladen, damit er weiß, welche Ressourcen zwischengespeichert werden sollen.
* Jedes Mal, wenn der Benutzer die Anwendung besucht, fordert der Browser *service-worker.js* und *service-worker-assets.js* im Hintergrund erneut an. Wenn der Server geänderte Inhalte für eine dieser Dateien zurückgibt (die Byte für Byte mit dem vorhandenen installierten Service Worker verglichen wurden), versucht der Service Worker, eine neue Version von sich selbst zu installieren.
* Wenn der Service Worker eine neue Version von sich selbst installiert, erstellt er einen neuen, eigenen Cache für Offlineressourcen und füllt ihn mit Ressourcen auf, die in *service-worker-Assets.js* aufgeführt sind. Diese Logik ist in der Funktion `onInstall` in *service-worker.published.js* implementiert.
* Wenn der Prozess erfolgreich abgeschlossen wird (d. h., alle Ressourcen werden ohne Fehler geladen, und alle Inhaltshashs stimmen überein), wechselt der neue Service Worker in den Status „Warten auf Aktivierung“. Sobald der Benutzer die Anwendung schließt (d. h., es sind keine Registerkarten oder Fenster mehr vorhanden, in denen die Anwendung angezeigt wird), wird der neue Service Worker aktiviert und für nachfolgende Besuche der Anwendung verwendet. Der alte Service Worker und der zugehörige Cache werden gelöscht.
* Wenn der Prozess nicht erfolgreich abgeschlossen wird, wird die neue Service-Worker-Instanz verworfen. Die Durchführung des Updates wird beim nächsten Besuch des Benutzers erneut versucht, und wenn eine bessere Netzwerkverbindung besteht, können die Anforderungen abgeschlossen kann.

Sie können jeden Aspekt dieses Prozesses anpassen, indem Sie die Service-Worker-Logik bearbeiten. Keine der obigen Erläuterungen ist auf Blazor beschränkt. Es handelt sich lediglich um eine empfohlene Vorgehensweise bei Verwendung der PWA-Vorlagenoption. Weitere Informationen finden Sie in der [Service-Worker-Dokumentation](https://developer.mozilla.org/docs/Web/API/Service_Worker_API.).

#### <a name="how-requests-are-resolved"></a>Auflösen von Anforderungen

Wie oben beschrieben, verwendet der Standard-Service-Worker eine *Cache-First*-Strategie, d. h., er versucht, zwischengespeicherte Inhalte bereitzustellen, sofern verfügbar. Wenn für eine bestimmte URL kein Inhalt zwischengespeichert ist, z. B. beim Anfordern von Daten aus einer Back-End-API, greift der Service Worker auf eine reguläre Netzwerkanforderung zurück, die nur erfolgreich ausgeführt werden kann, wenn der Server erreichbar ist. Diese Logik ist in `onFetch` in *service-worker.published.js* implementiert.

Wenn die Blazor-Komponenten Daten aus Back-End-APIs anfordern müssen und Sie ein benutzerfreundliches Verfahren für den Fall bereitstellen möchten, dass solche Anforderungen aufgrund der Nichtverfügbarkeit des Netzwerks fehlschlagen, müssen Sie die Logik in den Komponenten implementieren. Schließen Sie z. B. `HttpClient`-Anforderungen in `try/catch` ein.

#### <a name="support-server-rendered-pages"></a>Unterstützung von auf dem Server gerenderten Seiten

Überlegen Sie, was geschieht, wenn der Benutzer zum ersten Mal zu einer URL, z. B. `/counter`, oder zu einem anderen Deep-Link zu Ihrer Anwendung navigiert. In diesen Fällen sollen keine Inhalte zurückgegeben werden, die als `/counter` zwischengespeichert sind. Stattdessen muss der Browser als `/index.html` zwischengespeicherte Inhalte laden, um die Blazor WebAssembly-Anwendung zu starten. Diese anfänglichen Anforderungen werden als *Navigationsanforderungen* bezeichnet (im Gegensatz zu *Unterressourcenanforderungen* für Bilder/CSS/usw. oder *Fetch-/XHR-Anforderungen* für API-Daten).

Der Standard-Service-Worker enthält eine spezielle Logik für Navigationsanforderungen. Diese löst sie durch die Rückgabe des zwischengespeicherten Inhalts für `/index.html` auf, unabhängig von der angeforderten URL. Diese Logik ist in der Funktion `onFetch` in *service-worker.published.js* implementiert.

Wenn die Anwendung über bestimmte URLs verfügt, die auf dem Server gerendertes HTML zurückgeben müssen (ohne `/index.html` aus dem Cache bereitzustellen), müssen Sie die Logik im Service Worker bearbeiten. Wenn beispielsweise alle URLs, die `/Identity/` enthalten, als reguläre Onlineanforderungen an den Server behandelt werden müssen, ändern Sie die in *service-worker.published.js* `onFetch` vorhandene Logik. Suchen Sie den folgenden Code:

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate';
```

Ändern Sie den Code wie folgt:

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate'
    && !event.request.url.includes('/Identity/');
```

Wenn Sie dies nicht tun, fängt der Service Worker unabhängig von der Netzwerkkonnektivität Anforderungen für diese URLs ab und löst sie mithilfe von `/index.html` auf.

#### <a name="control-asset-caching"></a>Steuern der Zwischenspeicherung von Ressourcen

Wenn im Projekt eine MSBuild-Eigenschaft mit dem Namen `ServiceWorkerAssetsManifest` definiert ist, generieren die Buildtools von Blazor ein Service-Worker-Assetmanifest mit dem angegebenen Namen. Die PWA-Standardvorlage erzeugt eine Projektdatei, die Folgendes enthält:

```xml
<ServiceWorkerAssetsManifest>service-worker-assets.js</ServiceWorkerAssetsManifest>
```

Die Datei wird in das Ausgabeverzeichnis *wwwroot* eingefügt, sodass sie vom Browser durch Anfordern von `/service-worker-assets.js` abgerufen werden kann. Um die Inhalte anzuzeigen, öffnen Sie *YourProject\bin\Debug\netstandard2.1\wwwroot\service-worker-assets.js* in einem Text-Editor. Bearbeiten Sie die Datei jedoch nicht, da sie bei jedem Build erneut generiert wird.

Standardmäßig wird in diesem Manifest Folgendes aufgeführt:

* Alle von Blazor verwalteten Ressourcen, z. B. .NET-Assemblys und die .NET-WebAssembly-Laufzeitdateien, die offline ausgeführt werden müssen.
* Alle Ressourcen, die im Verzeichnis *wwwroot* veröffentlicht werden, z. B. Bilder, CSS-Dateien und JavaScript-Dateien. Hierzu zählen auch statische Webressourcen, die von externen Projekten und NuGet-Paketen bereitgestellt werden.

Sie können steuern, welche dieser Ressourcen vom Service Worker abgerufen und zwischengespeichert werden, indem Sie die Logik in `onInstall` in *service-worker.published.js* bearbeiten. Standardmäßig werden Dateien abgerufen und zwischengespeichert, die typische Webdateierweiterungen aufweisen, z. B. *.html*, *.css*, *.js*, *.wasm* u. a., sowie spezielle Dateitypen für die Blazor WebAssembly ( *.dll*, *.pdb*).

Wenn Sie weitere Ressourcen einschließen möchten, die nicht im Verzeichnis *wwwroot* enthalten sind, können Sie zusätzliche MSBuild-ItemGroup-Einträge definieren. Fügen Sie z. B. in der Projektdatei Folgendes hinzu:

```xml
<ItemGroup>
    <ServiceWorkerAssetsManifestItem
        Include="MyDirectory\AnotherFile.json"
        RelativePath="MyDirectory\AnotherFile.json"
        AssetUrl="files/AnotherFile.json" />
</ItemGroup>
```

Die `AssetUrl`-Metadaten geben die zur Basis relative URL an, die der Browser beim Abrufen der zwischenzuspeichernden Ressource verwenden soll. Dieser Wert kann unabhängig vom ursprünglichen Quelldateinamen auf dem Datenträger sein.

> [!IMPORTANT]
> Das Hinzufügen eines `ServiceWorkerAssetsManifestItem` bewirkt nicht, dass die Datei im Verzeichnis *wwwroot* veröffentlicht wird. Sie können die Veröffentlichungsausgabe separat steuern. Das `ServiceWorkerAssetsManifestItem` bewirkt lediglich, dass im Service-Worker-Assetmanifest ein zusätzlicher Eintrag angezeigt wird.

## <a name="push-notifications"></a>Pushbenachrichtigungen

Eine Blazor WebAssembly-PWA kann wie jede andere PWA Pushbenachrichtigungen von einem Back-End-Server empfangen. Der Server kann diese jederzeit senden, auch wenn der Benutzer die Anwendung nicht aktiv verwendet (wenn z. B. ein anderer Benutzer eine Aktion ausführt, die möglicherweise relevant ist).

Der Mechanismus zum Senden einer Pushbenachrichtigung ist vollständig unabhängig von der Blazor WebAssembly, da er vom Back-End-Server implementiert wird, und dieser kann eine beliebige Technologie verwenden. Wenn Sie Pushbenachrichtigungen von einem ASP.NET Core-Server senden möchten, sollten Sie [ein ähnliches Verfahren wie das im Blazing Pizza-Workshop](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#sending-push-notifications) verwenden.

Der Mechanismus zum Empfangen und Anzeigen einer Pushbenachrichtigung auf dem Client hängt auch von der Blazor WebAssembly ab, da er im Service Worker implementiert ist, bei dem es sich um eine JavaScript-Datei handelt. Ein Beispiel hierfür finden Sie in dem [im Blazing Pizza-Workshop verwendeten Verfahren](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#displaying-notifications).

## <a name="caveats-for-offline-pwas"></a>Einschränkungen für Offline-PWAs

Nicht alle Anwendungen sollten versuchen, die Offlineverwendung zu unterstützen. Diese erhöht die Komplexität beträchtlich und ist nicht immer relevant.

Die Offlineunterstützung ist in der Regel nur in folgenden Situationen relevant:

* Wenn der primäre Datenspeicher für den Browser lokal ist. Dies ist beispielsweise der Fall, wenn Sie eine Benutzeroberfläche für ein [IoT](https://en.wikipedia.org/wiki/Internet_of_things)-Gerät entwickeln, das Daten in `localStorage` oder [IndexedDB](https://developer.mozilla.org/docs/Web/API/IndexedDB_API) speichert.

* Wenn Sie mit beträchtlichem Aufwand die Back-End-API-Daten abrufen und zwischenspeichern, die für die einzelnen Benutzer relevant sind, damit sie offline in ihnen navigieren können. Wenn Sie die Bearbeitung unterstützen, müssen Sie auch ein System zum Nachverfolgen von Änderungen und Synchronisieren der Änderungen mit dem Back-End erstellen.

* Wenn Sie sicherstellen möchten, dass die Anwendung unabhängig von den Netzwerkbedingungen sofort geladen wird. Sie müssen dann ein geeignetes Benutzererlebnis für die Back-End-API-Anforderungen implementieren, damit der Fortschritt der Anforderungen angezeigt wird und diese ein ordnungsgemäßes Verhalten aufweisen, wenn sie aufgrund der Nichtverfügbarkeit des Netzwerks fehlschlagen.

Offlinefähige PWAs müssen zudem eine Reihe zusätzlicher Komplikationen bewältigen. Entwickler sollten sich sorgfältig mit den folgenden Einschränkungen vertraut machen.

### <a name="offline-support-only-when-published"></a>Offlineunterstützung nur bei Veröffentlichung

Die PWA-Vorlage von Blazor aktiviert die Offlineunterstützung nur, wenn die Anwendung veröffentlicht wird. Der Grund hierfür ist, dass während der Entwicklung in der Regel jede Änderung sofort im Browser wiedergegeben werden soll, ohne dass ein Update im Hintergrund erfolgen muss.

Daher reicht es beim Erstellen einer offlinefähigen Anwendung nicht aus, die Anwendung im Entwicklungsmodus zu testen. Sie müssen die Anwendung im veröffentlichten Zustand testen, um zu verstehen, wie sie unter unterschiedlichen Netzwerkbedingungen reagiert.

### <a name="update-completion-after-user-navigation-away-from-app"></a>Abschluss des Updates, nachdem der Benutzer die App verlassen hat

Updates werden erst abgeschlossen, wenn der Benutzer die Anwendung auf allen Registerkarten verlassen hat. Wie in [Hintergrundupdates](#background-updates) erläutert, ruft der Browser die aktualisierten Service-Worker-Dateien ab und beginnt einen Updatevorgang, nachdem Sie ein Update der Anwendung bereitgestellt haben.

Viele Entwickler wissen nicht, dass das Update nach seinem Abschluss **erst dann** wirksam wird, wenn die Anwendung auf allen Registerkarten vom Benutzer verlassen wurde. Es reicht **nicht** aus, die Registerkarte zu aktualisieren, auf der die Anwendung angezeigt wird, selbst wenn dies die einzige Registerkarte ist, auf der sie angezeigt wird. Solange die Anwendung nicht vollständig geschlossen ist, bleibt der neue Service Worker im Status „Warten auf Aktivierung“. **Dies ist nicht auf Blazor beschränkt, sondern ein Standardverhalten von Webplattformen.**

Dies bereitet häufig Entwicklern Probleme, die versuchen, Updates des Service Worker oder von offline zwischengespeicherten Ressourcen zu testen. Wenn Sie die Entwicklertools des Browsers einchecken, wird möglicherweise Folgendes angezeigt:

![Bild](https://user-images.githubusercontent.com/1101362/76226394-b93f7380-6215-11ea-8572-7d52afee2dd8.png)

Solange die Liste der „Clients“ (d. h. Registerkarten oder Fenster, in denen die Anwendung angezeigt wird) nicht leer ist, wartet der Worker auf die Aktivierung. Durch dieses Verhalten der Service Worker soll Konsistenz sichergestellt werden, sodass alle Ressourcen aus demselben atomischen Cache abgerufen werden.

Wenn Sie Änderungen testen, können Sie einfach auf den im obigen Screenshot gezeigten Link „skipWaiting“ klicken und dann die Seite erneut laden. Sie können dies ggf. auch für alle Benutzer automatisieren, indem Sie den Service Worker so codieren, dass [bei einem Update die Wartephase übersprungen und der Service Worker sofort aktiviert wird](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#skip_the_waiting_phase). Hierdurch wird jedoch nicht mehr sichergestellt, dass Ressourcen immer konsistent aus derselben Cacheinstanz abgerufen werden.

### <a name="users-may-run-any-historical-version-of-the-app"></a>Benutzer können jede frühere Version der App ausführen.

Webentwickler erwarten gewohnheitsmäßig, dass Benutzer nur die zuletzt bereitgestellte Version ihrer Webanwendung ausführen, da dies im herkömmlichen Webverteilungsmodell üblich ist. Eine Offline-First-PWA ähnelt jedoch mehr einer nativen mobilen App, bei der die Benutzer nicht unbedingt die neueste Version ausführen.

Wie in [Hintergrundupdates](#background-updates) erläutert, **verwendet nach dem Bereitstellen des Updates einer Anwendung jeder vorhandene Benutzer mindestens noch während des nächsten Besuchs der Anwendung eine frühere Version** (da das Update im Hintergrund ausgeführt wird und erst aktiviert wird, wenn der Benutzer die Anwendung verlässt). Außerdem ist die verwendete frühere Version nicht immer die frühere Version, die Sie bereitgestellt haben. Es kann sich um eine *beliebige* frühere Version handeln, abhängig davon, wann der Benutzer zuletzt ein Update durchgeführt hat.

Dies kann ein Problem darstellen, wenn die Front-End- und Back-End-Teile der Anwendung eine Vereinbarung über das Schema für API-Anforderungen erfordern. Sie dürfen keine API-Schemaänderungen bereitstellen, die nicht abwärtskompatibel sind, solange Sie sich nicht vergewissert haben, dass alle Benutzer ein Upgrade durchgeführt haben. Oder Sie müssen zumindest die Verwendung inkompatibler Versionen der App durch Benutzer verhindern. Dasselbe gilt für eine native mobile App. Wenn Sie eine Breaking Change in Server-APIs bereitstellen, wird die Client-App für Personen, die noch kein Update durchgeführt haben, beschädigt.

Stellen Sie nach Möglichkeit keine Breaking Changes für die Back-End-APIs bereit. Wenn sich dies jedoch nicht vermeiden lässt, sollten Sie mit [Standard-Service-Worker-APIs, z. B. `ServiceWorkerRegistration`](https://developer.mozilla.org/docs/Web/API/ServiceWorkerRegistration), bestimmen, ob die Anwendung auf dem neuesten Stand ist, und andernfalls ihre Verwendung verhindern.

### <a name="interference-with-server-rendered-pages"></a>Störungen bei auf dem Server gerenderten Seiten

[Wie oben beschrieben](#support-server-rendered-pages), müssen Sie die Logik im Service Worker bearbeiten, wenn Sie vermeiden möchten, dass dieser für alle Navigationsanforderungen `/index.html`-Inhalte zurückgibt.

### <a name="all-service-worker-asset-manifest-contents-are-cached-by-default"></a>Alle Inhalte des Service-Worker-Assetmanifests werden standardmäßig zwischengespeichert.

[Wie oben beschrieben](#control-asset-caching), wird während des Builds die Datei *service-worker-assets.js* generiert, in der alle Ressourcen aufgelistet sind, die der Service Worker abrufen und zwischenspeichern soll.

Da in dieser Liste standardmäßig alles enthalten ist, was an *wwwroot* ausgegeben wurde (einschließlich Inhalte, die durch externe Pakete und Projekte bereitgestellt wurden), müssen Sie darauf achten, dass Sie nicht zu viele Inhalte einfügen. Wenn z. B. das Verzeichnis *wwwroot* Millionen von Bildern enthält, versucht der Service Worker, sie alle abzurufen und zwischenzuspeichern. Dabei wird eine übermäßige Bandbreite beansprucht, und der Vorgang wird wahrscheinlich nicht erfolgreich abgeschlossen.

Sie können beliebige Logik implementieren, um zu steuern, welche Teilmenge der Inhalte des Manifests abgerufen und zwischengespeichert werden, indem Sie die Funktion `onInstall` in *service-worker.published.js* bearbeiten.

### <a name="interaction-with-authentication"></a>Interaktion mit Authentifizierung

Sie können die PWA-Vorlagenoption zusammen mit den Authentifizierungsoptionen verwenden. Eine offlinefähige PWA kann auch die Authentifizierung unterstützen, wenn der Benutzer über Netzwerkkonnektivität verfügt.

Wenn ein Benutzer jedoch über keine Netzwerkkonnektivität verfügt, kann er sich nicht authentifizieren und keine Zugriffstoken abrufen. Beim Aufruf der Anmeldungsseite wird standardmäßig die Meldung „Netzwerkfehler“ angezeigt.

Daher ist es Ihre Aufgabe, einen Benutzeroberflächenflow zu entwerfen, mit dem der Benutzer Aktionen offline ausführen kann, ohne sich authentifizieren oder Zugriffstoken abrufen zu müssen. Oder Sie müssen zumindest sicherstellen, dass beim Fehlschlagen der Authentifizierung oder des Zugriffstokenabrufs dies auf ordnungsgemäße Weise geschieht. Wenn dies in der Anwendung nicht möglich ist, sollten Sie die Offlineunterstützung nicht aktivieren.
