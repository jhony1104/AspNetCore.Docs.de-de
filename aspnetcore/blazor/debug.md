---
title: ASP.net Core Debuggen Blazor
author: guardrex
description: Erfahren Sie, wie Sie Blazor-apps Debuggen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 1b0035af48b82807a6ae14835a41a1ecbef06bb6
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/17/2020
ms.locfileid: "76159988"
---
# <a name="debug-aspnet-core-opno-locblazor"></a>ASP.net Core Debuggen [!OP.NO-LOC(Blazor)]

[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

*Frühe* Unterstützung für das Debuggen [!OP.NO-LOC(Blazor)] Webassembly mithilfe der Browser Entwicklertools in Chrom basierten Browsern (Chrome/Microsoft Edge). Die Arbeit wird in folgenden Aufgaben ausgeführt:

* Aktivieren Sie das Debuggen in Visual Studio vollständig.
* Aktivieren Sie das Debugging in Visual Studio Code.

Die Debugger-Funktionen sind eingeschränkt. Folgende Szenarien sind verfügbar:

* Festlegen und Entfernen von Breakpoints.
* Einzelschritt (`F10`) durch den Code oder die Fortsetzung (`F8`)-Codeausführung.
* Beachten Sie in der *lokalen Anzeige die* Werte aller lokalen Variablen vom Typ `int`, `string`und `bool`.
* Weitere Informationen finden Sie in der-Aufrufkette, einschließlich der von JavaScript in .net und von .net bis JavaScript aus.

Folgendes ist *nicht*möglich:

* Beachten Sie die Werte aller lokalen Variablen, die keine `int`, `string`oder `bool`sind.
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

## <a name="procedure"></a>-Prozedur

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

> [!WARNING]
> Die Debugunterstützung in Visual Studio ist in einer frühen Phase der Entwicklung. **F5** -Debugging wird derzeit nicht unterstützt.

1. Führen Sie eine [!OP.NO-LOC(Blazor)] Webassembly-app in `Debug` Konfiguration ohne Debuggen aus (**STRG**+**F5** anstelle von **F5**).
1. Öffnen Sie die Debugeigenschaften der APP (Letzter Eintrag im Menü **Debuggen** ), und kopieren Sie die URL der HTTP- **App**. Navigieren Sie mit einem Chrom basierten Browser (Microsoft Edge Beta oder Chrome) zur http-Adresse (nicht zur HTTPS-Adresse) der app.
1. Platzieren Sie den Tastaturfokus in der APP im Browserfenster, nicht im Bereich Entwicklertools. Es ist am besten, den Entwicklertools-Panel für dieses Verfahren geschlossen zu halten. Nachdem das Debuggen gestartet wurde, können Sie den Bereich Entwicklertools erneut öffnen.
1. Wählen Sie die folgende [!OP.NO-LOC(Blazor)]spezifische Tastenkombination aus:

   * `Shift+Alt+D` unter Windows
   * `Shift+Cmd+D` unter macOS

   Wenn Sie die **Registerkarte "Debugfähige Browser nicht gefunden**" erhalten, finden Sie weitere Informationen unter [Aktivieren von Remote Debugging](#enable-remote-debugging).
   
   Nach dem Aktivieren von Remote Debugging:
   
   1 \. Ein neues Browserfenster wird geöffnet. Schließen Sie das vorherige Fenster.

   2 \. Platzieren Sie den Tastaturfokus in der APP im Browserfenster.

   3 \. Wählen Sie im neuen Browserfenster die [!OP.NO-LOC(Blazor)]spezifische Tastenkombination aus: `Shift+Alt+D` unter Windows oder `Shift+Cmd+D` unter macOS.

   4 \. Die Registerkarte **devtools** wird im Browser geöffnet. **Wählen Sie die Registerkarte der APP im Browserfenster erneut aus.**

   Informationen zum Anfügen der APP an Visual Studio finden Sie [im Abschnitt Anfügen an den Prozess in Visual Studio](#attach-to-process-in-visual-studio) .

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli/)

1. Führen Sie eine [!OP.NO-LOC(Blazor)] Webassembly-app in `Debug` Konfiguration aus, indem Sie die `--configuration Debug`-Option an den [dotnet Run](/dotnet/core/tools/dotnet-run) -Befehl übergeben: `dotnet run --configuration Debug`.
1. Navigieren Sie zu der APP unter der http-URL, die im Fenster der Shell angezeigt wird.
1. Platzieren Sie den Tastaturfokus auf die APP, nicht auf den Bereich Entwicklertools. Es ist am besten, den Entwicklertools-Panel für dieses Verfahren geschlossen zu halten. Nachdem das Debuggen gestartet wurde, können Sie den Bereich Entwicklertools erneut öffnen.
1. Wählen Sie die folgende [!OP.NO-LOC(Blazor)]spezifische Tastenkombination aus:

   * `Shift+Alt+D` unter Windows
   * `Shift+Cmd+D` unter macOS

   Wenn Sie die **Registerkarte "Debugfähige Browser nicht gefunden**" erhalten, finden Sie weitere Informationen unter [Aktivieren von Remote Debugging](#enable-remote-debugging).
   
   Nach dem Aktivieren von Remote Debugging:
   
   1 \. Ein neues Browserfenster wird geöffnet. Schließen Sie das vorherige Fenster.

   2 \. Platzieren Sie den Tastaturfokus in der APP im Browserfenster, nicht im Bereich Entwicklertools.

   3 \. Wählen Sie im neuen Browserfenster die [!OP.NO-LOC(Blazor)]spezifische Tastenkombination aus: `Shift+Alt+D` unter Windows oder `Shift+Cmd+D` unter macOS.

---

## <a name="enable-remote-debugging"></a>Aktivieren des Remotedebuggens

Wenn das Remote Debuggen deaktiviert ist, wird die Fehlerseite " **Debugfähige Browser Registerkarte nicht gefunden** " von Chrome generiert. Die Fehlerseite enthält Anweisungen zum Ausführen von Chrome mit dem geöffneten debugsport, sodass der Blazor debugerproxy eine Verbindung mit der App herstellen kann. *Schließen Sie alle Chrome-Instanzen* , und starten Sie Chrome wie beschrieben neu.

## <a name="debug-the-app"></a>Debuggen der App

Nachdem Chrome mit aktiviertem Remote Debugging ausgeführt wurde, öffnet die Tastenkombination Debuggen eine neue Registerkarte Debugger. Nach kurzer Zeit wird auf der Registerkarte **Quellen** eine Liste der .NET-Assemblys in der App angezeigt. Erweitern Sie jede Assembly, und suchen Sie die *. cs* -/ *. Razor* -Quelldateien, die zum Debuggen Legen Sie Breakpoints fest, wechseln Sie zurück zur Registerkarte der APP, und die Breakpoints werden bei der Ausführung des Codes angezeigt. Nach dem Erreichen eines halte Punkts wird ein Einzelschritt (`F10`) durch den Code ausgeführt, oder die Codeausführung (`F8`) wird normal fortgesetzt.

Blazor stellt einen debugproxy bereit, der das [Chrome devtools-Protokoll](https://chromedevtools.github.io/devtools-protocol/) implementiert und das Protokoll mit erweitert. Netzwerk spezifische Informationen. Wenn das Debuggen der Tastenkombination gedrückt wird, zeigt Blazor die Chrome devtools auf dem Proxy an. Der Proxy stellt eine Verbindung mit dem Browserfenster her, das Sie debuggen möchten (daher muss das Remote Debugging aktiviert werden).

## <a name="attach-to-process-in-visual-studio"></a>Anfügen an den Prozess in Visual Studio

Das Anfügen an den Prozess der app in Visual Studio ist ein *temporäres* Debugszenario für Blazor Webassembly, während das **F5** -Debugging in der Entwicklung ist.

So fügen Sie den Prozess der laufenden app an Visual Studio an:

1. Wählen Sie in Visual Studio die Option **Debuggen** > **an den Prozess anhängen**.
1. Wählen Sie für **Verbindungstyp**die Option **Chrome devtools Protocol WebSocket (keine Authentifizierung)** aus.
1. Fügen Sie für das **Verbindungs Ziel**die http-Adresse (nicht die HTTPS-Adresse) der APP ein.
1. Wählen Sie **Aktualisieren** aus, um die Einträge unter **Verfügbare Prozesse**zu aktualisieren.
1. Wählen Sie den zu debuggenden Browser Prozess und dann **Anfügen**aus.
1. Wählen Sie im Dialogfeld **Codetyp auswählen** den Codetyp für den jeweiligen Browser aus, an den Sie anhängen (Microsoft Edge oder Chrome), und klicken Sie dann auf **OK**.

## <a name="browser-source-maps"></a>Browser-Quell Zuordnungen

Browser-Quell Zuordnungen ermöglichen es dem Browser, kompilierte Dateien wieder den ursprünglichen Quelldateien zuzuordnen, und werden häufig für das Client seitige Debuggen verwendet. Blazor wird jedoch derzeit nicht C# direkt zu JavaScript/WASM zugeordnet. Stattdessen Blazor die Il-Interpretation innerhalb des Browsers durchführt, sodass die Quell Zuordnungen nicht relevant sind.

## <a name="troubleshoot"></a>Problembehandlung

Wenn Fehler auftreten, hilft Ihnen der folgende Tipp möglicherweise:

Öffnen Sie auf der Registerkarte **Debugger** die Entwicklertools in Ihrem Browser. Führen Sie in der-Konsole `localStorage.clear()` aus, um Breakpoints zu entfernen.
