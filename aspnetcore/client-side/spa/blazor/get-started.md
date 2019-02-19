---
title: Erste Schritte mit Blazor
author: guardrex
description: Erfahren Sie, wie Blazor Einstieg durch Erstellen und ändern ein Projekt Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2019
uid: spa/blazor/get-started
ms.openlocfilehash: 26336f73f6c8976ed5de819cebc3c5c50274ab03
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410007"
---
# <a name="get-started-with-blazor"></a>Erste Schritte mit Blazor

Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Erforderliche Komponenten:

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

So erstellen Ihr erste Blazor-Projekt in Visual Studio:

1. Installieren Sie das neueste [Blazor sprachdiensteerweiterung](https://go.microsoft.com/fwlink/?linkid=870389) in Visual Studio Marketplace. Dieser Schritt stellt Blazor Vorlagen in Visual Studio zur Verfügung.
1. Stellen Sie die Vorlagen Blazor mithilfe des folgenden Befehls in einer Befehlsshell für die Verwendung mit .NET Core-CLI zur Verfügung:

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::0.8.0-preview-19104-04
   ```

1. Wählen Sie **Datei** > **neues Projekt** > **Web** > **ASP.NET Core-Webanwendung**.
1. Stellen Sie sicher, dass **.NET Core** und **ASP.NET Core 3.0** oben ausgewählt werden.
1. Wählen Sie die **Blazor**-Vorlage und dann **OK** aus.
1. Drücken Sie **F5**, um die App auszuführen.

Herzlichen Glückwunsch! Sie haben soeben Ihre erste app für die Blazor ausgeführt!

<!--

# [Visual Studio Code](#tab/visual-studio-code)

Prerequisites:

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

To create your first Blazor project in Visual Studio Code:

1. Execute the following command in a command shell:

   ```console
   dotnet new blazor -o WebApplication1
   ```

1. Open the *WebApplication1* folder in Visual Studio Code.

1. Visual Studio code offers to create assets to build and debug the app, which includes the *tasks.json* and *launch.json* files. Select **Yes** to add the assets.

1. Execute the app using the Visual Studio Code debugger.

1. In a browser, navigate to `https://localhost:5001`.

Congratulations! You just ran your first Blazor app!

# [Visual Studio for Mac](#tab/visual-studio-mac)

.NET Core 3.0 will be supported with Visual Studio for Mac version 8.0 or later. Visual Studio for Mac version 8.0 Preview isn't available at this time.

Use the [.NET Core CLI version of this topic](xref:razor-components/get-started?tabs=netcore-cli) on macOS.

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

To create your first project Blazor project in Visual Studio for Mac:

1. Select **File** > **New Solution** or **New Project**.
1. In the sidebar, select **.NET Core** > **App**.
1. Select **Blazor** and select **Next**.
1. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.
1. In the **Project Name** field, enter `WebApplication1`. Select **Create**.
1. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

Congratulations! You just ran your first Blazor app!
-->

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli/)

Erforderliche Komponenten:

* [.NET Core SDK 3.0 (Vorschau)](https://dotnet.microsoft.com/download/dotnet-core/3.0)

1. Fügen Sie die Vorlagen Blazor mithilfe des folgenden Befehls in einer Befehlsshell hinzu:

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::0.8.0-preview-19104-04
   ```

1. Erstellen Sie Ihr erste Blazor-Projekt in einer Befehlsshell ein:

   ```console
   dotnet new blazor -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

1. Navigieren Sie in einem Browser zu `https://localhost:5001`.

Herzlichen Glückwunsch! Sie haben soeben Ihre erste app für die Blazor ausgeführt!

---

## <a name="blazor-project"></a>Blazor-Projekt

Wenn die app ausgeführt wird, stehen mehrere Seiten von Registerkarten in der Randleiste aus:

* Startseite
* Zähler
* Abrufen der Daten

Wählen Sie auf der Seite „Counter“ die Schaltfläche **Hier klicken** aus, um den Zähler ohne Seitenaktualisierung heraufzusetzen. Erhöhen eines Zählers auf einer Webseite bei normalerweise erfordert das Schreiben von JavaScript allerdings Blazor bietet einen besseren Ansatz mit C#.

*Pages/Counter.cshtml*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.cshtml)]

Eine Anforderung für `/counter` im Browser, laut der `@page` -Direktive am Anfang, bewirkt, dass die Leistungsindikator-Komponente, um seinen Inhalt zu rendern. Rendern von Komponenten in eine in-Memory-Darstellung der Render-Struktur, die zur Aktualisierung der Benutzeroberfläche in eine flexible und effiziente Möglichkeit verwendet werden kann.

Jedes Mal die **hier klicken** ausgewählt ist:

* Die `onclick` Ereignis wird ausgelöst.
* Die `IncrementCount`-Methode wird aufgerufen.
* Die `currentCount` wird erhöht.
* Die Komponente wird erneut gerendert.

Die Runtime vergleicht den neuen Inhalt zum vorherigen Inhalt und den geänderten Inhalt gilt nur, die (DOKUMENTOBJEKTMODELL).

Fügen Sie eine Komponente an eine andere Komponente, die mithilfe einer HTML-ähnliche Syntax. Parameter der Komponente werden mithilfe von Attributen oder untergeordneten Inhalt angegeben. Eine Komponente eines Leistungsindikators kann z. B. an der app-Startseite hinzugefügt werden, durch das Hinzufügen einer `<Counter />` Element an die Index-Komponente.

In *Pages/Index.cshtml*, ersetzen Sie die Umfrage Eingabeaufforderung Komponente mit der eine Komponente eines Leistungsindikators:

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.cshtml?highlight=7)]

Führen Sie die App aus. Die Startseite verfügt über eine eigene Leistungsindikator.

Um einen Parameter an die Leistungsindikator-Komponente hinzuzufügen, aktualisieren Sie der Komponente `@functions` blockieren:

* Hinzufügen einer Eigenschaft für `IncrementAmount` ergänzt die `[Parameter]` Attribut.
* Ändern Sie die `IncrementCount`-Methode, um `IncrementAmount` beim Heraufsetzen des Werts von `currentCount` zu verwenden.

*Pages/Counter.cshtml*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.cshtml?highlight=4,8)]

Geben Sie unter Verwendung eines Attributs einen `IncrementAmount`-Parameter in das `<Counter>`-Element der Home-Komponente ein.

*Pages/Index.cshtml*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.cshtml)]

Führen Sie die App aus. Die Startseite verfügt über eine eigene Zähler, der mit zehn jedes Mal erhöht die **hier klicken** ausgewählt ist.

## <a name="next-steps"></a>Nächste Schritte

<xref:tutorials/first-razor-components-app>
