---
title: Beginnen Sie mit ASP.net Core blazor
author: guardrex
description: Beginnen Sie mit blazor, indem Sie eine blazor-App mit den Tools Ihrer Wahl entwickeln.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: blazor/get-started
ms.openlocfilehash: 428e231a38bb6547cfc69ccb253374da5218e058
ms.sourcegitcommit: 0365af91518004c4a44a30dc3a8ac324558a399b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71198909"
---
# <a name="get-started-with-aspnet-core-blazor"></a>Beginnen Sie mit ASP.net Core blazor

Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Beginnen Sie mit blazor:

1. Installieren Sie die neueste Version von [.net Core 3,0 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) .

1. Installieren Sie die blazor-Vorlagen, indem Sie den folgenden Befehl in einer Befehlsshell ausführen:

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.0.0-preview9.19465.2
   ```

1. Befolgen Sie die Anleitungen für die Auswahl der Tools:

   # <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

   1 \. Installieren Sie die neueste [Visual Studio-Vorschau](https://visualstudio.com/vs/preview) mit der Arbeitsauslastung **ASP.net und Webentwicklung** .

   2 \. Erstellen Sie ein neues Projekt.

   3 \. Wählen Sie **blazor-App**aus. Klicken Sie auf **Weiter**.

   4 \. Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen. Vergewissern Sie sich, dass der **Orts** Eintrag korrekt ist, oder geben Sie einen Speicherort für das Projekt Wählen Sie **Erstellen** aus.

   5 \. Wählen Sie für eine blazor-Webassembly die Vorlage " **blazor Webassembly-App** " aus. Wählen Sie für eine blazor-Server-APP die Vorlage für den **blazor-Server** aus. Wählen Sie **Erstellen** aus. Informationen zu den beiden blazor-Hostingmodellen, *blazor Server* und *blazor Webassembly*, <xref:blazor/hosting-models>finden Sie unter.

   6 \. Drücken Sie **F5**, um die App auszuführen.

   > [!NOTE]
   > Wenn Sie die Erweiterung blazor Visual Studio für eine vorherige Vorschauversion von ASP.net Core blazor (Preview 6 oder früher) installiert haben, können Sie die Erweiterung deinstallieren. Die Installation der blazor-Vorlagen in einer Befehlsshell reicht nun aus, um die Vorlagen in Visual Studio zu überstehen.

   # <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

   1 \. Installieren Sie [Visual Studio Code](https://code.visualstudio.com/).

   2 \. Installieren Sie die neueste [ C# für Visual Studio Code Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

   3 \. Führen Sie den folgenden Befehl in einer Befehlsshell aus, um eine blazor-Webassembly zu nutzen:

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      Führen Sie den folgenden Befehl in einer Befehlsshell aus, um einen blazor-Server zu nutzen:

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      Informationen zu den beiden blazor-Hostingmodellen, *blazor Server* und *blazor Webassembly*, <xref:blazor/hosting-models>finden Sie unter.

   4 \. Öffnen Sie den Ordner *WebApplication1* in Visual Studio Code.

   5 \. Für ein blazor-Server Projekt fordert die IDE an, dass Sie Assets hinzufügen, um das Projekt zu erstellen und zu debuggen. Wählen Sie **Ja**.

   6 \. Wenn Sie eine blazor-Server-App verwenden, führen Sie die APP mit dem Visual Studio Code Debugger aus. Wenn Sie eine blazor Webassembly-App verwenden `dotnet run` , führen Sie aus dem Projektordner der APP aus.

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

   Führen Sie für eine blazor-Webassembly die folgenden Befehle in einer Befehlsshell aus:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Führen Sie für eine blazor-Server Darstellung die folgenden Befehle in einer Befehlsshell aus:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   Informationen zu den beiden blazor-Hostingmodellen, *blazor Server* und *blazor Webassembly*, <xref:blazor/hosting-models>finden Sie unter.

   Navigieren Sie in einem Browser zu `https://localhost:5001`.

   ---

Auf den Registerkarten in der Rand Leiste sind mehrere Seiten verfügbar:

* Startseite
* Zähler
* Abrufen von Daten

Wählen Sie auf der Seite „Counter“ die Schaltfläche **Hier klicken** aus, um den Zähler ohne Seitenaktualisierung heraufzusetzen. Das erhöhen eines Leistungs Zählers in einer Webseite erfordert normalerweise das Schreiben von JavaScript, aber Razor- C#Komponenten bieten einen besseren Ansatz mithilfe von.

*Pages/Counter.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

Eine Anforderung für `/counter` im Browser, wie durch die `@page` -Direktive angegeben, bewirkt, dass die `Counter` Komponente ihren Inhalt wieder gibt. Komponenten werden in einer Speicher internen Darstellung der Rendering-Struktur dargestellt, die dann zum flexiblen und effizienten Aktualisieren der Benutzeroberfläche verwendet werden kann.

Jedes Mal, wenn die Schaltfläche " **Click me** " ausgewählt wird:

* Das `onclick` Ereignis wird ausgelöst.
* Die `IncrementCount` -Methode wird aufgerufen.
* Der `currentCount` wird inkrementiert.
* Die Komponente wird wieder gerendert.

Die Laufzeit vergleicht den neuen Inhalt mit dem vorherigen Inhalt und wendet nur den geänderten Inhalt auf die Dokumentobjektmodell (DOM) an.

Fügen Sie einer anderen Komponente mithilfe der HTML-Syntax eine Komponente hinzu. Fügen Sie z. b `Counter` . die Komponente der Startseite der APP hinzu `<Counter />` , indem Sie `Index` der Komponente ein-Element hinzufügen.

*Pages/Index.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Führen Sie die App aus. Die Startseite verfügt über einen eigenen, von `Counter` der Komponente bereitgestellten Counter.

Komponenten Parameter werden mithilfe von Attributen oder untergeordnetem [Inhalt](xref:blazor/components#child-content)angegeben, sodass Sie Eigenschaften für die untergeordnete Komponente festlegen können. Aktualisieren Sie den `@code` Block der Komponente `Counter` , um der Komponente einen Parameter hinzuzufügen:

* Fügen Sie eine öffentliche Eigenschaft `IncrementAmount` für mit `[Parameter]` einem-Attribut hinzu.
* Ändern Sie die `IncrementCount`-Methode, um `IncrementAmount` beim Heraufsetzen des Werts von `currentCount` zu verwenden.

*Pages/Counter.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

Geben Sie `IncrementAmount` das im `Index` -Element `<Counter>` der Komponente mithilfe eines-Attributs an.

*Pages/Index.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

Führen Sie die App aus. Die `Index` Komponente verfügt über einen eigenen Wert, der jedes Mal um zehn erhöht wird, wenn die Schaltfläche " **Click me** " ausgewählt ist. Die `Counter` Komponente (*counter. Razor*) bei `/counter` wird weiterhin um 1 erhöht.

## <a name="next-steps"></a>Nächste Schritte

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:signalr/introduction>
