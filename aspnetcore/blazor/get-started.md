---
title: Erste Schritte mit ASP.NET Core Blazor
author: guardrex
description: Erste Schritte mit Blazor durch Erstellen einer Blazor-App mit den Tools Ihrer Wahl.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 63fee0b6a3152640a5483c2a682eec7d04742145
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243602"
---
# <a name="get-started-with-aspnet-core-blazor"></a>Erste Schritte mit ASP.NET Core Blazor

Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

Befolgen Sie für Ihre ersten Schritte mit Blazor die Anweisungen für Ihre bevorzugten Tools:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Installieren Sie die neueste Version von [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) mit der Workload **ASP.NET und Webentwicklung**.

1. Erstellen Sie ein neues Projekt.

1. Klicken Sie auf **Blazor-App**. Klicken Sie auf **Weiter**.

1. Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen. Vergewissern Sie sich, dass der Eintrag für den **Speicherort** korrekt ist, oder geben Sie einen Speicherort für das Projekt an. Wählen Sie **Erstellen** aus.

1. Wählen Sie für eine Blazor-WebAssembly-Benutzeroberfläche die Vorlage **Blazor-WebAssembly-App** aus. Wählen Sie für eine Blazor-Server-Benutzeroberfläche die Vorlage **Blazor-Server-App** aus. Wählen Sie **Erstellen** aus.

   Informationen zu den zwei Blazor-Hostingmodellen ( *Blazor-WebAssembly* und *Blazor-Server*) finden Sie unter <xref:blazor/hosting-models>.

1. Drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App auszuführen.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Installieren Sie die neueste Version des [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). Wenn Sie das SDK zuvor installiert haben, können Sie die installierte Version ermitteln, indem Sie den folgenden Befehl in einer Befehlsshell ausführen:

   ```dotnetcli
   dotnet --version
   ```

1. Installieren Sie die neueste Version von [Visual Studio Code](https://code.visualstudio.com/).

1. Installieren Sie die neueste [C# für Visual Studio Code-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) und die Erweiterung [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly), und legen Sie für `debug.javascript.usePreview` den Wert `true` fest.

  Öffnen Sie **Datei** > **Einstellungen** > **Einstellungen**, und suchen Sie nach `debug javascript use preview`, um `debug.javascript.usePreview` mithilfe der VS Code-Benutzeroberfläche auf `true` festzulegen. Aktivieren Sie das Kontrollkästchen für die Option **Use the new in-preview JavaScript debugger for Node.js and Chrome** (Neuen JavaScript-Debugger in der Vorschauversion für Node.js und Chrome verwenden).

1. Für eine Blazor-WebAssembly-Benutzeroberfläche führen Sie den folgenden Befehl in einer Befehlsshell aus:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Für eine Blazor-Server-Benutzeroberfläche führen Sie den folgenden Befehl in einer Befehlsshell aus:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Informationen zu den zwei Blazor-Hostingmodellen ( *Blazor-WebAssembly* und *Blazor-Server*) finden Sie unter <xref:blazor/hosting-models>.

1. Öffnen Sie in Visual Studio Code den Ordner `WebApplication1`.

1. Die IDE fordert an, dass Sie Ressourcen zum Erstellen und Debuggen des Projekts hinzufügen. Wählen Sie **Ja**.

1. Drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App auszuführen.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

1. Installieren Sie [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Wählen Sie **Datei** > **Neue Projektmappe** aus, oder erstellen Sie im **Startfenster** ein **Neues Projekt**.

1. Klicken Sie auf der Randleiste auf **Web and Console** > **App** (Web und Konsole).

   Wählen Sie für eine Blazor-WebAssembly-Benutzeroberfläche die Vorlage **Blazor-WebAssembly-App** aus. Wählen Sie für eine Blazor-Server-Benutzeroberfläche die Vorlage **Blazor-Server-App** aus. Klicken Sie auf **Weiter**.

   Informationen zu den zwei Blazor-Hostingmodellen ( *Blazor-WebAssembly* und *Blazor-Server*) finden Sie unter <xref:blazor/hosting-models>.

1. Bestätigen Sie die folgenden Konfigurationen:

   * **Zielframework** auf **.NET Core 3.1** festgelegt
   * **Authentifizierung** auf **Keine Authentifizierung** festgelegt
   
   Klicken Sie auf **Weiter**.

1. Benennen Sie im Feld **Projektname** die App `WebApplication1`. Wählen Sie **Erstellen** aus.

1. Wählen Sie **Ausführen** > **Ohne Debuggen starten** aus, um die App *ohne Debugger* auszuführen. Führen Sie die App entweder über **Ausführen** > **Debuggen starten** oder über die Schaltfläche „Ausführen“ (&#9654;) *mit dem Debugger aus*.

Wenn eine Eingabeaufforderung dem Entwicklungszertifikat zu vertrauen scheint, vertrauen Sie dem Zertifikat und fahren Sie fort. Das Benutzer- und Keychainkennwort sind erforderlich, um dem Zertifikat zu vertrauen.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli/)

1. Installieren Sie die neueste Version des [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). Wenn Sie das SDK zuvor installiert haben, können Sie die installierte Version ermitteln, indem Sie den folgenden Befehl in einer Befehlsshell ausführen:

   ```dotnetcli
   dotnet --version
   ```

1. Für eine Blazor-WebAssembly-Benutzeroberfläche führen Sie die folgenden Befehle in einer Befehlsshell aus:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Für eine Blazor-Server-Benutzeroberfläche führen Sie die folgenden Befehle in einer Befehlsshell aus:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Informationen zu den zwei Blazor-Hostingmodellen ( *Blazor-WebAssembly* und *Blazor-Server*) finden Sie unter <xref:blazor/hosting-models>.

1. Navigieren Sie in einem Browser zu `https://localhost:5001`.

---

Mehrere Seiten sind über Registerkarten in der Randleiste verfügbar:

* Startseite
* Zähler
* Abrufen von Daten

Klicken Sie auf der Seite der Zähler auf die Schaltfläche, um den Zähler ohne Seitenaktualisierung heraufzusetzen. Für das Heraufsetzen eines Zählers auf einer Webseite müssen Sie JavaScript-Code schreiben, aber mit Blazor können Sie C# verwenden.

`Pages/Counter.razor`:

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

Eine Anforderung für `/counter` im Browser, wie durch die `@page`-Direktive oben angegeben, veranlasst die `Counter`-Komponente, ihren Inhalt zu rendern. Die Komponenten werden in einer In-Memory-Darstellung der Renderstruktur gerendert, die anschließend verwendet werden kann, um die Benutzeroberfläche auf flexible und effiziente Weise zu aktualisieren.

Jedes Mal, wenn die Schaltfläche ausgewählt wird:

* Das `onclick`-Ereignis wird ausgelöst.
* Die `IncrementCount` -Methode wird aufgerufen.
* Der `currentCount` wird inkrementiert.
* Die Komponente wird erneut gerendert.

Die Laufzeit vergleicht den neuen Inhalt mit dem bisherigen Inhalt und wendet nur den geänderten Inhalt auf das Dokumentobjektmodell (DOM) an.

Fügen Sie eine Komponente mithilfe der HTML-Syntax zu einer anderen Komponente hinzu. Fügen Sie z. B. die `Counter`-Komponente zur Startseite der App hinzu, indem Sie der `Index`-Komponente ein `<Counter />`-Element hinzufügen.

`Pages/Index.razor`:

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Führen Sie die App aus. Die Startseite verfügt über einen eigenen Zähler, der von der `Counter`-Komponente bereitgestellt wird.

Komponentenparameter werden mithilfe von Attributen oder [untergeordneten Inhalten](xref:blazor/components/index#child-content) angegeben, die es Ihnen ermöglichen, Eigenschaften für die untergeordnete Komponente festzulegen. Um der `Counter`-Komponente einen Parameter hinzuzufügen, aktualisieren Sie den `@code`-Block der Komponente:

* Fügen Sie eine öffentliche Eigenschaft für `IncrementAmount` mit einem [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute)-Attribut hinzu.
* Ändern Sie die `IncrementCount`-Methode, um `IncrementAmount` beim Heraufsetzen des Werts von `currentCount` zu verwenden.

`Pages/Counter.razor`:

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

Geben Sie `IncrementAmount` im `<Counter>`-Element der `Index`-Komponente mit einem Attribut an.

`Pages/Index.razor`:

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Führen Sie die App aus. Die Komponente `Index` verfügt über einen eigenen Zähler, der bei jeder Auswahl der Schaltfläche um zehn erhöht wird. Die Komponente `Counter` (`Pages/Counter.razor`) bei `/counter` wird weiterhin um eins erhöht.

## <a name="next-steps"></a>Nächste Schritte

Schrittweises Erstellen einer Blazor-App:

> [!div class="nextstepaction"]
> <xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:blazor/templates>
* <xref:signalr/introduction>
