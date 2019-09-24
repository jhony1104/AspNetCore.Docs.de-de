---
title: Debuggen ASP.net Core blazor
author: guardrex
description: Informationen zum Debuggen von blazor-apps.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/debug
ms.openlocfilehash: 3519479d8058f013de23cc9cfa0f5574cd158053
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71207210"
---
# <a name="debug-aspnet-core-blazor"></a>Debuggen ASP.net Core blazor

[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

*Frühe* Unterstützung für das Debuggen von blazor Webassembly-apps, die auf Webassembly in Chrome ausgeführt werden

Die Debugger-Funktionen sind eingeschränkt. Folgende Szenarien sind verfügbar:

* Festlegen und Entfernen von Breakpoints.
* Einzelschritt (`F10`) durch den Code oder die Fortsetzung`F8`()-Codeausführung.
* Beachten Sie in der *lokalen Anzeige die* Werte aller lokalen Variablen vom Typ `int`, `string`und `bool`.
* Weitere Informationen finden Sie in der-Aufrufkette, einschließlich der von JavaScript in .net und von .net bis JavaScript aus.

Folgendes ist *nicht*möglich:

* Beachten Sie die Werte aller lokalen Variablen, die `int`nicht `string`, oder `bool`sind.
* Beachten Sie die Werte aller Klasseneigenschaften oder-Felder.
* Zeigen Sie auf Variablen, um deren Werte anzuzeigen.
* Auswerten von Ausdrücken in der Konsole.
* Schrittweises Ausführen von asynchronen Aufrufen.
* Führen Sie die meisten anderen normalen Debugszenarien aus.

Die Entwicklung weiterer debuggingszenarien ist ein Fortschritt des Engineering-Teams.

## <a name="prerequisites"></a>Erforderliche Komponenten

Das Debuggen erfordert einen der folgenden Browser:

* Google Chrome (Version 70 oder höher)
* Microsoft Edge-Vorschau ([Edge dev-Channel](https://www.microsoftedgeinsider.com))

## <a name="procedure"></a>Prozedur

1. Führen Sie eine blazor Webassembly- `Debug` app in der Konfiguration aus. Übergeben Sie `--configuration Debug` die Option an den Befehl [dotnet Run](/dotnet/core/tools/dotnet-run) : `dotnet run --configuration Debug`.
1. Greifen Sie im Browser auf die APP zu.
1. Platzieren Sie den Tastaturfokus auf die APP, nicht auf den Bereich Entwicklertools. Der Bereich Entwicklertools kann geschlossen werden, wenn das Debuggen initiiert wird.
1. Wählen Sie die folgende blazor-spezifische Tastenkombination aus:
   * `Shift+Alt+D`unter Windows/Linux
   * `Shift+Cmd+D`unter macOS
1. Führen Sie die auf dem Bildschirm aufgeführten Schritte aus, um den Browser mit aktiviertem Remote Debugging neu zu starten.
1. Wählen Sie die folgende blazor-spezifische Tastenkombination erneut aus, um die Debugsitzung zu starten:
   * `Shift+Alt+D`unter Windows/Linux
   * `Shift+Cmd+D`unter macOS

## <a name="enable-remote-debugging"></a>Remote Debuggen aktivieren

Wenn das Remote Debuggen deaktiviert ist, wird die Fehlerseite " **Debugfähige Browser Registerkarte nicht gefunden** " von Chrome generiert. Die Fehlerseite enthält Anweisungen zum Ausführen von Chrome mit dem geöffneten debugsport, sodass der blazor-debugproxy eine Verbindung mit der App herstellen kann. *Schließen Sie alle Chrome-Instanzen* , und starten Sie Chrome wie beschrieben neu.

## <a name="debug-the-app"></a>Debuggen der App

Nachdem Chrome mit aktiviertem Remote Debugging ausgeführt wurde, öffnet die Tastenkombination Debuggen eine neue Registerkarte Debugger. Nach kurzer Zeit wird auf der Registerkarte **Quellen** eine Liste der .NET-Assemblys in der App angezeigt. Erweitern Sie jede Assembly, und suchen Sie nach den zum Debuggen verfügbaren *CS*/ *. Razor* -Quelldateien. Legen Sie Breakpoints fest, wechseln Sie zurück zur Registerkarte der APP, und die Breakpoints werden bei der Ausführung des Codes angezeigt. Nach dem Erreichen eines halte Punkts wird ein Einzelschritt (`F10`) durch den Code oder die fort`F8`Setzung ()-Codeausführung normal ausgeführt.

Blazor bietet einen debugproxy, der das [Chrome devtools-Protokoll](https://chromedevtools.github.io/devtools-protocol/) implementiert und das Protokoll mit erweitert. Netzwerk spezifische Informationen. Wenn das Debuggen der Tastenkombination gedrückt wird, zeigt blazor die Chrome devtools auf dem Proxy an. Der Proxy stellt eine Verbindung mit dem Browserfenster her, das Sie debuggen möchten (daher muss das Remote Debugging aktiviert werden).

## <a name="browser-source-maps"></a>Browser-Quell Zuordnungen

Browser-Quell Zuordnungen ermöglichen es dem Browser, kompilierte Dateien wieder den ursprünglichen Quelldateien zuzuordnen, und werden häufig für das Client seitige Debuggen verwendet. Allerdings ist blazor derzeit nicht C# direkt auf JavaScript/WASM zuordnet. Stattdessen führt blazor eine Il-Interpretation innerhalb des Browsers durch, sodass die Quell Zuordnungen nicht relevant sind.

## <a name="troubleshooting-tip"></a>Tipps zur Problembehandlung

Wenn Fehler auftreten, hilft Ihnen der folgende Tipp möglicherweise:

Öffnen Sie auf der Registerkarte **Debugger** die Entwicklertools in Ihrem Browser. Führen `localStorage.clear()` Sie in der-Konsole aus, um Breakpoints zu entfernen.
