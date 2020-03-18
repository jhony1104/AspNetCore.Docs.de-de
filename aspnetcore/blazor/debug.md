---
title: Debuggen von ASP.NET Core Blazor
author: guardrex
description: Erfahren Sie, wie Sie Blazor-Apps debuggen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 1b0035af48b82807a6ae14835a41a1ecbef06bb6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78648313"
---
# <a name="debug-aspnet-core-blazor"></a>Debuggen von ASP.NET Core Blazor

[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Für das Debuggen von Blazor WebAssembly unter Verwendung der Entwicklertools des Browsers in Chromium-basierten Browsern (Chrome/Edge) gibt es *frühzeitige* Unterstützung. Daran wird derzeit gearbeitet:

* Vollständige Aktivierung des Debuggens in Visual Studio.
* Aktivieren des Debuggens in Visual Studio Code.

Debuggerfunktionen werden eingeschränkt. Verfügbare Szenarien:

* Festlegen und Entfernen von Breakpoints.
* Einzelschritt (`F10`) durch den Code oder Wiederaufnahme (`F8`) der Codeausführung.
* Beobachten Sie in der Anzeige für *lokale Variablen* die Werte beliebiger lokaler Variablen vom Typ `int`, `string` und `bool`.
* Betrachten Sie die Aufrufliste, einschließlich der Aufrufketten, die von JavaScript zu .NET und von .NET zu JavaScript verlaufen.

Folgendes ist *nicht* möglich:

* Beobachten der Werte beliebiger lokaler Variablen, die nicht den Typ `int`, `string` oder `bool` aufweisen.
* Beobachten der Werte beliebiger Klasseneigenschaften oder Felder.
* Bewegen des Mauszeigers über Variablen, um deren Werte anzuzeigen.
* Auswerten von Ausdrücken in der Konsole.
* Wechseln zwischen asynchronen Aufrufen.
* Durchführen der meisten anderen normalen Debugszenarien.

Die Entwicklung weiterer Debugszenarien ist ein ständiger Schwerpunkt des Entwicklungsteams.

## <a name="prerequisites"></a>Voraussetzungen

Zum Debuggen ist einer der folgenden Browser erforderlich:

* Google Chrome (Version 70 oder höher)
* Microsoft Edge Preview ([Edge Dev Channel](https://www.microsoftedgeinsider.com))

## <a name="procedure"></a>Prozedur

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

> [!WARNING]
> Die Debugunterstützung in Visual Studio befindet sich in einem frühen Stadium der Entwicklung. **F5**-Debuggen wird derzeit nicht unterstützt.

1. Führen Sie eine Blazor WebAssembly-App in der `Debug`-Konfiguration ohne Debuggen aus (**STRG** + **F5** statt **F5**).
1. Öffnen Sie die Debugeigenschaften der App (letzter Eintrag im Menü **Debuggen**), und kopieren Sie die HTTP **App-URL**. Navigieren Sie mit einem Chromium-basierten Browser (Edge Beta oder Chrome) zur HTTP-Adresse (nicht zur HTTPS-Adresse) der App.
1. Platzieren Sie den Tastaturfokus auf die App im Browserfenster, nicht auf den Bereich der Entwicklertools. Für dieses Verfahren ist es am besten, den Bereich der Entwicklertools geschlossen zu halten. Nachdem das Debuggen begonnen hat, können Sie den Bereich der Entwicklertools wieder öffnen.
1. Wählen Sie die folgende Blazor-spezifische Tastenkombination:

   * `Shift+Alt+D` unter Windows
   * `Shift+Cmd+D` unter macOS

   Wenn Sie die Meldung darüber erhalten, dass keine **debugfähige Browserregisterkarte gefunden werden konnte**, finden Sie weitere Informationen unter [Aktivieren des Remotedebuggens](#enable-remote-debugging).
   
   Nachdem Sie das Remotedebuggen aktiviert haben:
   
   1\. Ein neues Browserfenster wird geöffnet. Schließen Sie das vorherige Fenster.

   2\. Platzieren Sie den Tastaturfokus auf die App im Browserfenster.

   3\. Wählen Sie die Blazor-spezifische Tastenkombination im neuen Browserfenster aus: `Shift+Alt+D` unter Windows oder `Shift+Cmd+D` unter macOS.

   4\. Die Registerkarte **DevTools** wird im Browser geöffnet. **Wählen Sie die Registerkarte der App im Browserfenster erneut aus.**

   Informationen zum Anhängen der App an Visual Studio finden Sie im Abschnitt [Anhängen an den Prozess in Visual Studio](#attach-to-process-in-visual-studio).

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli/)

1. Führen Sie eine Blazor WebAssembly-App in der `Debug`-Konfiguration aus, indem Sie die Option `--configuration Debug` an den Befehl [dotnet run](/dotnet/core/tools/dotnet-run) übergeben: `dotnet run --configuration Debug`.
1. Navigieren Sie zu der App an der im Fenster der Shell angezeigten HTTP-URL.
1. Platzieren Sie den Tastaturfokus auf die App, nicht auf den Bereich der Entwicklertools. Für dieses Verfahren ist es am besten, den Bereich der Entwicklertools geschlossen zu halten. Nachdem das Debuggen begonnen hat, können Sie den Bereich der Entwicklertools wieder öffnen.
1. Wählen Sie die folgende Blazor-spezifische Tastenkombination:

   * `Shift+Alt+D` unter Windows
   * `Shift+Cmd+D` unter macOS

   Wenn Sie die Meldung darüber erhalten, dass keine **debugfähige Browserregisterkarte gefunden werden konnte**, finden Sie weitere Informationen unter [Aktivieren des Remotedebuggens](#enable-remote-debugging).
   
   Nachdem Sie das Remotedebuggen aktiviert haben:
   
   1\. Ein neues Browserfenster wird geöffnet. Schließen Sie das vorherige Fenster.

   2\. Platzieren Sie den Tastaturfokus auf die App im Browserfenster, nicht auf den Bereich der Entwicklertools.

   3\. Wählen Sie die Blazor-spezifische Tastenkombination im neuen Browserfenster aus: `Shift+Alt+D` unter Windows oder `Shift+Cmd+D` unter macOS.

---

## <a name="enable-remote-debugging"></a>Aktivieren des Remotedebuggens

Wenn das Remote-Debuggen deaktiviert ist, wird von Chrome die Fehlerseite mit der Aussage generiert, dass eine **debugfähige Browserregisterkarte nicht gefunden werden konnte**. Die Fehlerseite enthält Anweisungen zum Ausführen von Chrome bei geöffnetem Debugport, sodass der Blazor-Debugproxy eine Verbindung mit der App herstellen kann. *Schließen Sie alle Chrome-Instanzen*, und starten Sie Chrome wie angewiesen neu.

## <a name="debug-the-app"></a>Debuggen der App

Sobald Chrome mit aktiviertem Remotedebuggen ausgeführt wird, öffnet die Tastenkombination für das Debuggen eine neue Debuggerregisterkarte. Nach einem Moment zeigt die Registerkarte **Quellen** eine Liste der .NET-Assemblys in der App an. Erweitern Sie jede Assembly, und suchen Sie die *CS*/*RAZOR*-Quelldateien, die zum Debuggen zur Verfügung stehen. Legen Sie Breakpoints fest, wechseln Sie zurück zur Registerkarte der App, und die Breakpoints werden beim Ausführen des Codes getroffen. Nachdem ein Breakpoint getroffen wurde, durchlaufen Sie den Code per Einzelschritt (`F10`), oder setzen Sie die Codeausführung normal fort (`F8`).

Blazor bietet einen Debugproxy, der das [Chrome DevTools-Protokoll](https://chromedevtools.github.io/devtools-protocol/) implementiert und das Protokoll um .NET-spezifische Informationen erweitert. Wenn die Tastenkombination zum Debuggen gedrückt wird, verweist Blazor die Chrome DevTools auf den Proxy. Der Proxy stellt eine Verbindung mit dem Browserfenster her, das Sie debuggen möchten (daher die Notwendigkeit, das Remotedebuggen zu aktivieren).

## <a name="attach-to-process-in-visual-studio"></a>Anhängen an den Prozess in Visual Studio

Das Anhängen an den Prozess der App in Visual Studio ist ein *temporäres* Debugszenario für Blazor WebAssembly, während sich das **F5**-Debuggen in der Entwicklung befindet.

So fügen Sie den Prozess der aktiven App an Visual Studio an

1. Klicken Sie in Visual Studio auf **Debuggen** > **An den Prozess anhängen**.
1. Wählen Sie für den **Verbindungstyp** die Option **WebSocket mit Chrome DevTools-Protokoll (keine Authentifizierung)** aus.
1. Für das **Verbindungsziel** fügen Sie die HTTP-Adresse (nicht die HTTPS-Adresse) der App ein.
1. Wählen Sie **Aktualisieren** aus, um die Einträge unter **Verfügbare Prozesse** zu aktualisieren.
1. Wählen Sie den zu debuggenden Browserprozess und dann **Anfügen** aus.
1. Wählen Sie im Dialogfeld **Codetyp auswählen** den Codetyp für den spezifischen Browser aus, den Sie anfügen (Edge oder Chrome), und wählen Sie dann **OK**.

## <a name="browser-source-maps"></a>Browserquellzuordnungen

Browserquellzuordnungen ermöglichen es dem Browser, kompilierte Dateien zu ihren ursprünglichen Quelldateien zurück zuzuordnen und sie werden häufig für clientseitiges Debuggen verwendet. Allerdings ordnet Blazor C# derzeit nicht direkt zu JavaScript/WASM zu. Stattdessen übernimmt Blazor die IL-Interpretation innerhalb des Browsers, sodass Quellzuordnungen nicht relevant sind.

## <a name="troubleshoot"></a>Problembehandlung

Wenn Sie auf Fehler stoßen, könnte der folgende Tipp helfen:

Öffnen Sie auf der Registerkarte **Debugger** die Entwicklertools in Ihrem Browser. Führen Sie in der Konsole `localStorage.clear()` aus, um alle Breakpoints zu entfernen.
