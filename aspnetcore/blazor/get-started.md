---
title: Beginnen Sie mit ASP.net Core Blazor
author: guardrex
description: Beginnen Sie mit der Blazor, indem Sie eine Blazor-App mit den Tools Ihrer Wahl entwickeln.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
no-loc:
- Blazor
uid: blazor/get-started
ms.openlocfilehash: 9b4aee0be30568f098c756e9ab4cb5298e9a049b
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73962998"
---
# <a name="get-started-with-aspnet-core-opno-locblazor"></a>Beginnen Sie mit ASP.net Core Blazor

Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Beginnen Sie mit Blazor:

::: moniker range=">= aspnetcore-3.1"

1. Installieren Sie das [.net Core 3,1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Installieren Sie die [Blazor Webassembly](xref:blazor/hosting-models#blazor-webassembly) -Vorlage, indem Sie den folgenden Befehl in einer Befehlsshell ausführen. [Microsoft. aspnetcore.Blazor. Das Vorlagen](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) Paket verfügt über eine Vorschauversion, während sich Blazor Webassembly in der Vorschau Phase befindet.

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview2.19528.8
   ```

1. Befolgen Sie die Anleitungen für die Auswahl der Tools:

   # <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

   1 \. Installieren Sie [Visual Studio 16,4 Preview 2 oder](https://visualstudio.microsoft.com/vs/preview/) höher mit der Arbeitsauslastung **ASP.net und Webentwicklung** .

   2 \. Erstellen Sie ein neues Projekt.

   3 \. Wählen Sie **Blazor App**aus. Klicken Sie auf **Weiter**.

   4 \. Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen. Vergewissern Sie sich, dass der **Orts** Eintrag korrekt ist, oder geben Sie einen Speicherort für das Projekt Wählen Sie **Erstellen** aus.

   5 \. Wählen Sie für eine Blazor Webassembly die Vorlage **Blazor Webassembly-App** aus. Wählen Sie für eine Blazor Server-Darstellung die **Blazor Server-App** -Vorlage aus. Wählen Sie **Erstellen** aus. Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.

   6 \. Drücken Sie **STRG**+**F5**, um die App auszuführen.

   > [!NOTE]
   > Wenn Sie die Blazor Visual Studio-Erweiterung für eine vorherige Vorschauversion von ASP.net Core Blazor (Preview 6 oder früher) installiert haben, können Sie die Erweiterung deinstallieren. Die Installation der Blazor Vorlagen in einer Befehlsshell reicht nun aus, um die Vorlagen in Visual Studio zu überstehen.

   # <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

   1 \. Installieren Sie [Visual Studio Code](https://code.visualstudio.com/).

   2 \. Installieren Sie die neueste [ C# für Visual Studio Code Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

   3 \. Führen Sie für eine Blazor Webassembly den folgenden Befehl in einer Befehlsshell aus:

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      Führen Sie den folgenden Befehl in einer Befehlsshell aus, um eine Blazor Server-Funktion zu erhalten:

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.

   4 \. Öffnen Sie den Ordner *WebApplication1* in Visual Studio Code.

   5 \. Bei einem Blazor Server-Projekt fordert die IDE an, dass Sie Assets hinzufügen, um das Projekt zu erstellen und zu debuggen. Wählen Sie **Ja**.

   6 \. Wenn Sie eine Blazor Server-App verwenden, führen Sie die APP mit dem Visual Studio Code Debugger aus. Wenn Sie eine Blazor Webassembly-App verwenden, führen Sie `dotnet run` aus dem Projektordner der APP aus.

   7 \. Navigieren Sie in einem Browser zu `https://localhost:5001`.

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor Server experience, select the **Blazor Server App** template. For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli/)

   Führen Sie für eine Blazor Webassembly die folgenden Befehle in einer Befehlsshell aus:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Führen Sie die folgenden Befehle in einer Befehlsshell aus, um die Blazor Server-Darstellung auszuführen:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.

   Navigieren Sie in einem Browser zu `https://localhost:5001`.

   ---

::: moniker-end

::: moniker range="< aspnetcore-3.1"

1. Installieren Sie die neueste Version des [.net Core 3,0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) .

1. Installieren Sie optional die Vorlage [Blazor Webassembly](xref:blazor/hosting-models#blazor-webassembly) , indem Sie das [.net Core 3,1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) installieren und dann den folgenden Befehl in einer Befehlsshell ausführen:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview2.19528.8
   ```

1. Befolgen Sie die Anleitungen für die Auswahl der Tools:

   # <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

   1 \. Installieren Sie die neueste Version von [Visual Studio](https://visualstudio.com/vs/) mit der Arbeitsauslastung **ASP.net und Webentwicklung** .

   2 \. Optional können Sie [Visual Studio 16,4 Preview 2 oder](https://visualstudio.microsoft.com/vs/preview/) höher mit der Arbeitsauslastung **ASP.net und Webentwicklung** für Blazor Webassembly-App-Entwicklung installieren.

   3 \. Erstellen Sie ein neues Projekt.

   4 \. Wählen Sie **Blazor App**aus. Klicken Sie auf **Weiter**.

   5 \. Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen. Vergewissern Sie sich, dass der **Orts** Eintrag korrekt ist, oder geben Sie einen Speicherort für das Projekt Wählen Sie **Erstellen** aus.

   6 \. Wählen Sie für eine Blazor Webassembly die Vorlage **Blazor Webassembly-App** aus. Wählen Sie für eine Blazor Server-Darstellung die **Blazor Server-App** -Vorlage aus. Wählen Sie **Erstellen** aus. Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.

   7 \. Drücken Sie **F5**, um die App auszuführen.

   > [!NOTE]
   > Wenn Sie die Blazor Visual Studio-Erweiterung für eine vorherige Vorschauversion von ASP.net Core Blazor (Preview 6 oder früher) installiert haben, können Sie die Erweiterung deinstallieren. Die Installation der Blazor Vorlagen in einer Befehlsshell reicht nun aus, um die Vorlagen in Visual Studio zu überstehen.

   # <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

   1 \. Installieren Sie [Visual Studio Code](https://code.visualstudio.com/).

   2 \. Installieren Sie die neueste [ C# für Visual Studio Code Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

   3 \. Führen Sie für eine Blazor Webassembly den folgenden Befehl in einer Befehlsshell aus:

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      Führen Sie den folgenden Befehl in einer Befehlsshell aus, um eine Blazor Server-Funktion zu erhalten:

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.

   4 \. Öffnen Sie den Ordner *WebApplication1* in Visual Studio Code.

   5 \. Bei einem Blazor Server-Projekt fordert die IDE an, dass Sie Assets hinzufügen, um das Projekt zu erstellen und zu debuggen. Wählen Sie **Ja**.

   6 \. Wenn Sie eine Blazor Server-App verwenden, führen Sie die APP mit dem Visual Studio Code Debugger aus. Wenn Sie eine Blazor Webassembly-App verwenden, führen Sie `dotnet run` aus dem Projektordner der APP aus.

   7 \. Navigieren Sie in einem Browser zu `https://localhost:5001`.

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor Server experience, select the **Blazor Server App** template. For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli/)

   Führen Sie für eine Blazor Webassembly die folgenden Befehle in einer Befehlsshell aus:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Führen Sie die folgenden Befehle in einer Befehlsshell aus, um die Blazor Server-Darstellung auszuführen:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.

   Navigieren Sie in einem Browser zu `https://localhost:5001`.

   ---

::: moniker-end

Auf den Registerkarten in der Rand Leiste sind mehrere Seiten verfügbar:

* POS1
* Zähler
* Abrufen von Daten

Wählen Sie auf der Seite „Counter“ die Schaltfläche **Hier klicken** aus, um den Zähler ohne Seitenaktualisierung heraufzusetzen. Das Inkrementieren eines Zählers in einer Webseite erfordert normalerweise das Schreiben von JavaScript C#, aber mit Blazor können Sie verwenden.

*Pages/Counter.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

Eine Anforderung für `/counter` im Browser, wie von der `@page`-Direktive angegeben, bewirkt, dass die `Counter`-Komponente ihren Inhalt wieder gibt. Komponenten werden in einer Speicher internen Darstellung der Rendering-Struktur dargestellt, die dann zum flexiblen und effizienten Aktualisieren der Benutzeroberfläche verwendet werden kann.

Jedes Mal, wenn die Schaltfläche " **Click me** " ausgewählt wird:

* Das `onclick`-Ereignis wird ausgelöst.
* Die `IncrementCount` -Methode wird aufgerufen.
* Der `currentCount` wird inkrementiert.
* Die Komponente wird wieder gerendert.

Die Laufzeit vergleicht den neuen Inhalt mit dem vorherigen Inhalt und wendet nur den geänderten Inhalt auf die Dokumentobjektmodell (DOM) an.

Fügen Sie einer anderen Komponente mithilfe der HTML-Syntax eine Komponente hinzu. Fügen Sie beispielsweise der Startseite der APP die Komponente `Counter` hinzu, indem Sie der Komponente `Index` ein `<Counter />`-Element hinzufügen.

*Pages/Index.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Führen Sie die App aus. Die Startseite verfügt über einen eigenen Wert, der von der Komponente `Counter` bereitgestellt wird.

Komponenten Parameter werden mithilfe von Attributen oder untergeordnetem [Inhalt](xref:blazor/components#child-content)angegeben, sodass Sie Eigenschaften für die untergeordnete Komponente festlegen können. Aktualisieren Sie den Block "`@code`" der Komponente, um der Komponente "`Counter`" einen Parameter hinzuzufügen:

* Fügen Sie eine öffentliche Eigenschaft für `IncrementAmount` mit einem `[Parameter]` Attribut hinzu.
* Ändern Sie die `IncrementCount`-Methode, um `IncrementAmount` beim Heraufsetzen des Werts von `currentCount` zu verwenden.

*Pages/Counter.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

Geben Sie den `IncrementAmount` im `<Counter>`-Element der `Index`-Komponente mithilfe eines-Attributs an.

*Pages/Index.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Führen Sie die App aus. Die Komponente "`Index`" hat einen eigenen Wert, der jedes Mal um zehn erhöht wird, wenn die Schaltfläche " **Click me** " ausgewählt ist. Die Komponente "`Counter`" (*counter. Razor*) bei `/counter` wird weiterhin um 1 erhöht.

## <a name="next-steps"></a>Nächste Schritte

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:signalr/introduction>
