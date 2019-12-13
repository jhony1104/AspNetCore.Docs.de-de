---
title: Erste Schritte mit ASP.NET Core Blazor
author: guardrex
description: Beginnen Sie mit der Blazor, indem Sie eine Blazor-App mit den Tools Ihrer Wahl entwickeln.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/09/2019
no-loc:
- Blazor
uid: blazor/get-started
ms.openlocfilehash: e368ecaf931d392de7e52ec2d5a2dfd171c2c86f
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74943763"
---
# <a name="get-started-with-aspnet-core-opno-locblazor"></a>Erste Schritte mit ASP.NET Core Blazor

Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Beginnen Sie mit Blazor:

::: moniker range=">= aspnetcore-3.1"

1. Installieren Sie das [.net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).

1. Installieren Sie optional die Vorlage [Blazor Webassembly](xref:blazor/hosting-models#blazor-webassembly) :
   * Installieren Sie das [.net Core 3,1-oder spätere SDK (Vorschauversion)](https://dotnet.microsoft.com/download/dotnet-core/3.1).
   * Führen Sie den folgenden Befehl in einer Befehlsshell aus. [Microsoft. aspnetcore.Blazor. Das Vorlagen](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) Paket verfügt über eine Vorschauversion, während sich Blazor Webassembly in der Vorschau Phase befindet.

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. Befolgen Sie die Anleitungen für die Auswahl der Tools:

   # <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

   1 \. Installieren Sie [Visual Studio 16,4 oder](https://visualstudio.microsoft.com/vs/preview/) höher mit der Arbeitsauslastung **ASP.net und Webentwicklung** .

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

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

   1 \. Installieren Sie [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/). Wechseln [Sie zum Aktualisierungs Kanal, um die Vorschau anzuzeigen](/visualstudio/mac/install-preview).

   2 \. Wählen Sie Datei > **neue** Projekt **Mappe** aus, oder erstellen Sie ein **Neues Projekt**.

   3 \. Wählen Sie in der Rand Leiste die Option **.net Core** - > **App**aus.

   4 \. Wählen Sie die **Blazor Server-App** -Vorlage aus. Zurzeit ist nur die Blazor Server-Vorlage in Visual Studio für Mac verfügbar. Befolgen Sie für eine Blazor Webassembly die Anweisungen auf der Registerkarte **.net Core-CLI** . Nachdem Sie die Vorlage Blazor Server ausgewählt haben, wählen Sie **weiter**aus. Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   5 \. Legen Sie das **Ziel Framework** auf **.net Core 3,1** fest, und wählen Sie **weiter**aus.

   6 \. Benennen Sie die APP im Feld **Projektname** `WebApplication1`. Wählen Sie **Erstellen** aus.

   7 \. Wählen **Sie > ausführen** **ohne Debuggen** ausführen aus, um die APP *ohne den Debugger*auszuführen. Führen Sie die APP mit **Debuggen starten** aus, um die APP *mit dem Debugger*auszuführen.

       If a prompt appears to trust the development certificate, trust the certificate and continue.

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

1. Installieren Sie das neueste [.net Core 3,0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0).

1. Installieren Sie optional die Vorlage [Blazor Webassembly](xref:blazor/hosting-models#blazor-webassembly) :
   * Installieren Sie das [.net Core 3,1-oder spätere SDK (Vorschauversion)](https://dotnet.microsoft.com/download/dotnet-core/3.1).
   * Führen Sie den folgenden Befehl in einer Befehlsshell aus. [Microsoft. aspnetcore.Blazor. Das Vorlagen](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) Paket verfügt über eine Vorschauversion, während sich Blazor Webassembly in der Vorschau Phase befindet.

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
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

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

   1 \. Installieren Sie [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/). Wechseln [Sie zum Aktualisierungs Kanal, um die Vorschau anzuzeigen](/visualstudio/mac/install-preview).

   2 \. Wählen Sie Datei > **neue** Projekt **Mappe** aus, oder erstellen Sie ein **Neues Projekt**.

   3 \. Wählen Sie in der Rand Leiste die Option **.net Core** - > **App**aus.

   4 \. Wählen Sie die **Blazor Server-App** -Vorlage aus. Zurzeit ist nur die Blazor Server-Vorlage in Visual Studio für Mac verfügbar. Befolgen Sie für eine Blazor Webassembly die Anweisungen auf der Registerkarte **.net Core-CLI** . Nachdem Sie die Vorlage Blazor Server ausgewählt haben, wählen Sie **weiter**aus. Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   5 \. Legen Sie das **Ziel Framework** auf **.net Core 3,0** fest, und wählen Sie **weiter**aus.

   6 \. Benennen Sie die APP im Feld **Projektname** `WebApplication1`. Wählen Sie **Erstellen** aus.

   7 \. Wählen **Sie > ausführen** **ohne Debuggen** ausführen aus, um die APP *ohne den Debugger*auszuführen. Führen Sie die APP mit **Debuggen starten** aus, um die APP *mit dem Debugger*auszuführen.

       If a prompt appears to trust the development certificate, trust the certificate and continue.

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

* Startseite
* Zähler
* Abrufen von Daten

Wählen Sie auf der Seite „Counter“ die Schaltfläche **Hier klicken** aus, um den Zähler ohne Seitenaktualisierung heraufzusetzen. Das Inkrementieren eines Zählers in einer Webseite erfordert normalerweise das Schreiben von JavaScript C#, aber mit Blazor können Sie verwenden.

*Pages/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

Eine Anforderung für `/counter` im Browser, wie von der `@page`-Direktive angegeben, bewirkt, dass die `Counter` Komponente ihren Inhalt wieder gibt. Komponenten werden in einer Speicher internen Darstellung der Rendering-Struktur dargestellt, die dann zum flexiblen und effizienten Aktualisieren der Benutzeroberfläche verwendet werden kann.

Jedes Mal, wenn die Schaltfläche " **Click me** " ausgewählt wird:

* Das `onclick` Ereignis wird ausgelöst.
* Die `IncrementCount` -Methode wird aufgerufen.
* Der `currentCount` wird inkrementiert.
* Die Komponente wird wieder gerendert.

Die Laufzeit vergleicht den neuen Inhalt mit dem vorherigen Inhalt und wendet nur den geänderten Inhalt auf die Dokumentobjektmodell (DOM) an.

Fügen Sie einer anderen Komponente mithilfe der HTML-Syntax eine Komponente hinzu. Fügen Sie z. b. der Startseite der APP die `Counter` Komponente hinzu, indem Sie der `Index` Komponente ein `<Counter />`-Element hinzufügen.

*Pages/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Führen Sie die App aus. Die Startseite verfügt über einen eigenen Counter, der von der `Counter` Komponente bereitgestellt wird.

Komponenten Parameter werden mithilfe von Attributen oder untergeordnetem [Inhalt](xref:blazor/components#child-content)angegeben, sodass Sie Eigenschaften für die untergeordnete Komponente festlegen können. Um der `Counter` Komponente einen Parameter hinzuzufügen, aktualisieren Sie den `@code` Block der Komponente:

* Fügen Sie eine öffentliche Eigenschaft für `IncrementAmount` mit einem `[Parameter]` Attribut hinzu.
* Ändern Sie die `IncrementCount`-Methode, um `IncrementAmount` beim Heraufsetzen des Werts von `currentCount` zu verwenden.

*Pages/Counter.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

Geben Sie die `IncrementAmount` im `<Counter>`-Element der `Index` Komponente mithilfe eines-Attributs an.

*Pages/Index.razor*:

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Führen Sie die App aus. Die `Index` Komponente verfügt über einen eigenen Wert, der jedes Mal um zehn erhöht wird, wenn die Schaltfläche " **Click me** " ausgewählt ist. Die `Counter` Komponente (*counter. Razor*) bei `/counter` wird weiterhin um 1 erhöht.

## <a name="next-steps"></a>Nächste Schritte

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:blazor/templates>
* <xref:signalr/introduction>
