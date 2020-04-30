---
title: Erste Schritte mit ASP.NET Core Blazor
author: guardrex
description: Erste Schritte mit Blazor durch Erstellen einer Blazor-App mit den Tools Ihrer Wahl.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 2f10b00adce31c020d46d107c087159c17341beb
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111070"
---
# <a name="get-started-with-aspnet-core-blazor"></a>Erste Schritte mit ASP.NET Core Blazor

Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Führen Sie für Ihre ersten Schritte mit Blazor die Anleitung für Ihre bevorzugten Tools durch:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Installieren Sie die neueste Version von [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) mit der Workload **ASP.NET und Webentwicklung**, um Blazor Server-Apps zu erstellen.

   Installieren Sie die neueste Preview von [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) mit der Workload **ASP.NET und Webentwicklung**, um Blazor Server- und Blazor WebAssembly-Apps zu erstellen.

   Informationen zu den zwei Blazor-Hostingmodellen, *Blazor WebAssembly* und *Blazor Server*, finden Sie unter <xref:blazor/hosting-models>.

1. Installieren Sie die Vorschauvorlage [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly), indem Sie den folgenden Befehl ausführen:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

1. Erstellen Sie ein neues Projekt.

1. Wählen Sie **Blazor-App** aus. Klicken Sie auf **Weiter**.

1. Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen. Vergewissern Sie sich, dass der Eintrag für den **Speicherort** korrekt ist, oder geben Sie einen Speicherort für das Projekt an. Wählen Sie **Erstellen** aus.

1. Für eine Blazor WebAssembly-Benutzeroberfläche (ab Visual Studio 16.6 Preview 2) wählen Sie die Vorlage **Blazor WebAssembly-App** aus. Für eine Blazor Server-Benutzeroberfläche (ab Visual Studio 16.4) wählen Sie die Vorlage **Blazor Server-App** aus. Wählen Sie **Erstellen** aus.

1. Drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App auszuführen.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Installieren Sie das [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Installieren Sie optional die Vorschauvorlage [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly), indem Sie den folgenden Befehl ausführen:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

   > [!NOTE]
   > Das [.NET Core SDK 3.1.201 oder höher](https://dotnet.microsoft.com/download/dotnet-core/3.1) ist für die Verwendung der Vorlage für Blazor WebAssembly 3.2 Preview 4 **erforderlich**. Überprüfen Sie die installierte Version des .NET Core-SDK, indem Sie `dotnet --version` in einer Befehlsshell ausführen.

1. Installieren Sie [Visual Studio Code](https://code.visualstudio.com/).

1. Installieren Sie die neueste [C# für Visual Studio Code-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) und die Erweiterung [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly), und legen Sie für `debug.javascript.usePreview` den Wert `true` fest.

1. Für eine Blazor Server-Benutzeroberfläche führen Sie den folgenden Befehl in einer Befehlsshell aus:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Für eine Blazor WebAssembly-Benutzeroberfläche führen Sie den folgenden Befehl in einer Befehlsshell aus:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Informationen zu den beiden Blazor-Hostingmodellen, *Blazor Server* und *Blazor WebAssembly*, finden Sie unter <xref:blazor/hosting-models>.

1. Öffnen Sie in Visual Studio Code den Ordner *WebApplication1*.

1. Die IDE fordert an, dass Sie Ressourcen zum Erstellen und Debuggen des Projekts hinzufügen. Wählen Sie **Ja**.

1. Bei Blazor Server führen Sie die App mit dem Visual Studio Code-Debugger aus.

   Bei Blazor App starten Sie die App mithilfe der Startkonfiguration **.NET Core Launch (Blazor Standalone)** und starten dann den Browser mithilfe der Startkonfiguration **.NET Core Debug Blazor Web Assembly in Chrome** (Chrome erforderlich). Weitere Informationen finden Sie unter <xref:blazor/debug#visual-studio-code>.

1. Navigieren Sie in einem Browser zu `https://localhost:5001`.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

Blazor Server wird in Visual Studio für Mac unterstützt. Blazor WebAssembly wird derzeit nicht unterstützt. Befolgen Sie die Anweisungen auf der Registerkarte **.NET Core-CLI**, um Blazor WebAssembly-Apps unter macOS zu erstellen.

1. Installieren Sie [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Wählen Sie **Datei** > **Neue Projektmappe** aus, oder erstellen Sie ein **Neues Projekt**.

1. Wählen Sie in der Randleiste **.NET Core** > **App** aus.

1. Wählen Sie die Vorlage **Blazor Server App** aus. Wählen Sie **Erstellen** aus.

   Weitere Informationen zum Blazor Server-Hostingmodell finden Sie unter <xref:blazor/hosting-models>.

1. Legen Sie das **Zielframework** auf **.NET Core 3.1** fest, und wählen Sie **Weiter** aus.

1. Benennen Sie im Feld **Projektname** die App `WebApplication1`. Wählen Sie **Erstellen** aus.

1. Wählen Sie **Ausführen** > **Ohne Debuggen ausführen**, um die App *ohne Debugger* auszuführen. Führen Sie die App mit **Debugging starten** aus, um die App *mit dem Debugger* auszuführen.

Wenn eine Eingabeaufforderung dem Entwicklungszertifikat zu vertrauen scheint, vertrauen Sie dem Zertifikat und fahren Sie fort.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli/)

1. Installieren Sie das [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Installieren Sie optional die Vorschauvorlage [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly), indem Sie den folgenden Befehl ausführen:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

   > [!NOTE]
   > Das [.NET Core SDK 3.1.201 oder höher](https://dotnet.microsoft.com/download/dotnet-core/3.1) ist für die Verwendung der Vorlage für Blazor WebAssembly 3.2 Preview 4 **erforderlich**. Überprüfen Sie die installierte Version des .NET Core-SDK, indem Sie `dotnet --version` in einer Befehlsshell ausführen.

1. Für eine Blazor Server-Benutzeroberfläche führen Sie die folgenden Befehle in einer Befehlsshell aus:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Für eine Blazor WebAssembly-Benutzeroberfläche führen Sie die folgenden Befehle in einer Befehlsshell aus:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Informationen zu den beiden Blazor-Hostingmodellen, *Blazor Server* und *Blazor WebAssembly*, finden Sie unter <xref:blazor/hosting-models>.

1. Navigieren Sie in einem Browser zu `https://localhost:5001`.

---

Mehrere Seiten sind über Registerkarten in der Randleiste verfügbar:

* Startseite
* Zähler
* Abrufen von Daten

Wählen Sie auf der Seite „Counter“ die Schaltfläche **Hier klicken** aus, um den Zähler ohne Seitenaktualisierung heraufzusetzen. Für das Heraufsetzen eines Zählers auf einer Webseite müssen Sie JavaScript-Code schreiben, aber mit Blazor können Sie C# verwenden.

*Pages/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

Eine Anforderung für `/counter` im Browser, wie durch die `@page`-Direktive oben angegeben, veranlasst die `Counter`-Komponente, ihren Inhalt zu rendern. Die Komponenten werden in einer In-Memory-Darstellung der Renderstruktur gerendert, die anschließend verwendet werden kann, um die Benutzeroberfläche auf flexible und effiziente Weise zu aktualisieren.

Jedes Mal, wenn die **Hier klicken**-Schaltfläche ausgewählt wird:

* Das `onclick`-Ereignis wird ausgelöst.
* Die `IncrementCount` -Methode wird aufgerufen.
* Der `currentCount` wird inkrementiert.
* Die Komponente wird erneut gerendert.

Die Laufzeit vergleicht den neuen Inhalt mit dem bisherigen Inhalt und wendet nur den geänderten Inhalt auf das Dokumentobjektmodell (DOM) an.

Fügen Sie eine Komponente mithilfe der HTML-Syntax zu einer anderen Komponente hinzu. Fügen Sie z. B. die `Counter`-Komponente zur Startseite der App hinzu, indem Sie der `Index`-Komponente ein `<Counter />`-Element hinzufügen.

*Pages/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Führen Sie die App aus. Die Startseite verfügt über einen eigenen Zähler, der von der `Counter`-Komponente bereitgestellt wird.

Komponentenparameter werden mithilfe von Attributen oder [untergeordneten Inhalten](xref:blazor/components#child-content) angegeben, die es Ihnen ermöglichen, Eigenschaften für die untergeordnete Komponente festzulegen. Um der `Counter`-Komponente einen Parameter hinzuzufügen, aktualisieren Sie den `@code`-Block der Komponente:

* Fügen Sie eine öffentliche Eigenschaft für `IncrementAmount` mit einem `[Parameter]`-Attribut hinzu.
* Ändern Sie die `IncrementCount`-Methode, um `IncrementAmount` beim Heraufsetzen des Werts von `currentCount` zu verwenden.

*Pages/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

Geben Sie `IncrementAmount` im `<Counter>`-Element der `Index`-Komponente mit einem Attribut an.

*Pages/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Führen Sie die App aus. Die Komponente `Index` verfügt über einen eigenen Zähler, der bei jeder Auswahl der Schaltfläche **Hier klicken** um zehn erhöht wird. Die `Counter`-Komponente (*Counter.razor*) bei `/counter` wird weiterhin um eins erhöht.

## <a name="next-steps"></a>Nächste Schritte

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:blazor/templates>
* <xref:signalr/introduction>
