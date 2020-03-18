---
title: Erste Schritte mit ASP.NET Core Blazor
author: guardrex
description: Erste Schritte mit Blazor durch Erstellen einer Blazor-App mit den Tools Ihrer Wahl.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/28/2019
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: bd33d874b3d6122f2ab820e9b147b0e62ba03a58
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78648631"
---
# <a name="get-started-with-aspnet-core-blazor"></a>Erste Schritte mit ASP.NET Core Blazor

Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Erste Schritte mit Blazor:

1. Installieren Sie das [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Installieren Sie optional die Vorlage [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly):
   * Installieren Sie das [SDK für .NET Core 3.1 oder höher (Vorschau)](https://dotnet.microsoft.com/download/dotnet-core/3.1).
   * Führen Sie den folgenden Befehl in einer Befehlsshell aus. Das Paket [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) enthält eine Vorschauversion, solange sich die Blazor WebAssembly in der Vorschauphase befindet.

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.2.0-preview1.20073.1
   ```

1. Befolgen Sie die Anleitungen für die Auswahl der Tools:

   # <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

   1\. Installieren Sie [Version 16.4 von Visual Studio 2019 oder höher](https://visualstudio.microsoft.com/vs/preview/) mit der Workload **ASP.NET und Webentwicklung**.

   2\. Erstellen Sie ein neues Projekt.

   3\. Wählen Sie **Blazor-App** aus. Klicken Sie auf **Weiter**.

   4\. Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen. Vergewissern Sie sich, dass der Eintrag für den **Speicherort** korrekt ist, oder geben Sie einen Speicherort für das Projekt an. Wählen Sie **Erstellen** aus.

   5\. Für eine Blazor WebAssembly-Benutzeroberfläche wählen Sie die **Blazor WebAssembly App**-Vorlage aus. Für eine Blazor Server-Benutzeroberfläche wählen Sie die **Blazor Server App**-Vorlage aus. Wählen Sie **Erstellen** aus. Informationen zu den beiden Blazor-Hostingmodellen, *Blazor Server* und *Blazor WebAssembly*, finden Sie unter <xref:blazor/hosting-models>.

   6\. Drücken Sie **STRG**+**F5**, um die App auszuführen.

   > [!NOTE]
   > Wenn Sie die Blazor Visual Studio-Erweiterung für eine frühere Vorschauversion von ASP.NET Core Blazor (Vorschauversion 6 oder früher) installiert haben, können Sie die Erweiterung deinstallieren. Die Installation der Blazor-Vorlagen in einer Befehlsshell ist jetzt dafür geeignet, um die Vorlagen in Visual Studio zu bearbeiten.

   # <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

   1\. Installieren Sie [Visual Studio Code](https://code.visualstudio.com/).

   2\. Installieren Sie die aktuellste [C# für Visual Studio Code-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

   3\. Für eine Blazor WebAssembly-Benutzeroberfläche führen Sie den folgenden Befehl in einer Befehlsshell aus:

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      Für eine Blazor Server-Benutzeroberfläche führen Sie den folgenden Befehl in einer Befehlsshell aus:

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      Informationen zu den beiden Blazor-Hostingmodellen, *Blazor Server* und *Blazor WebAssembly*, finden Sie unter <xref:blazor/hosting-models>.

   4\. Öffnen Sie in Visual Studio Code den Ordner *WebApplication1*.

   5\. Bei einem Blazor Server-Projekt werden Sie von der IDE aufgefordert, Objekte hinzuzufügen, um das Projekt zu erstellen und zu debuggen. Wählen Sie **Ja**.

   6\. Wenn Sie eine Blazor Server-App verwenden, führen Sie die App mit dem Visual Studio Code-Debugger aus. Wenn Sie eine Blazor WebAssembly-App verwenden, führen Sie `dotnet run` über den Projektordner der App aus.

   7\. Navigieren Sie in einem Browser zu `https://localhost:5001`.

   # <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

   1\. Installieren Sie [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/).

   2\. Wählen Sie **Datei** > **Neue Projektmappe** aus, oder erstellen Sie ein **Neues Projekt**.

   3\. Wählen Sie in der Randleiste **.NET Core** > **App** aus.

   4\. Wählen Sie die Vorlage **Blazor Server App** aus. Derzeit ist nur die Blazor Server-Vorlage in Visual Studio für Mac verfügbar. Für eine Blazor WebAssembly-Benutzeroberfläche folgen Sie den Anweisungen auf der Registerkarte **.NET Core CLI**. Nach Auswahl der Blazor Server-Vorlage wählen Sie **Weiter** aus. Informationen zu den beiden Blazor-Hostingmodellen, *Blazor Server* und *Blazor WebAssembly*, finden Sie unter <xref:blazor/hosting-models>.

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   5\. Legen Sie das **Zielframework** auf **.NET Core 3.1** fest, und wählen Sie **Weiter** aus.

   6\. Benennen Sie im Feld **Projektname** die App `WebApplication1`. Wählen Sie **Erstellen** aus.

   7\. Wählen Sie **Ausführen** > **Ohne Debuggen ausführen**, um die App *ohne Debugger* auszuführen. Führen Sie die App mit **Debugging starten** aus, um die App *mit dem Debugger* auszuführen.

   Wenn eine Eingabeaufforderung dem Entwicklungszertifikat zu vertrauen scheint, vertrauen Sie dem Zertifikat und fahren Sie fort.

   # <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli/)

   Für eine Blazor WebAssembly-Benutzeroberfläche führen Sie die folgenden Befehle in einer Befehlsshell aus:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Für eine Blazor Server-Benutzeroberfläche führen Sie die folgenden Befehle in einer Befehlsshell aus:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Informationen zu den beiden Blazor-Hostingmodellen, *Blazor Server* und *Blazor WebAssembly*, finden Sie unter <xref:blazor/hosting-models>.

   Navigieren Sie in einem Browser zu `https://localhost:5001`.

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
