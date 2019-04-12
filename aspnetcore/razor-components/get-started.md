---
title: Erste Schritte mit Razor-Komponenten
author: guardrex
description: Erfahren Sie, wie Sie mit Razor-Komponenten beginnen, durch das Erstellen und ändern ein Projekt für die Razor-Komponenten.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: razor-components/get-started
ms.openlocfilehash: 151e58497b0f22fa7c5a9bde1f665eeb73fd5dc3
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59515531"
---
# <a name="get-started-with-razor-components"></a>Erste Schritte mit Razor-Komponenten

Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

# [<a name="visual-studio"></a>Visual Studio](#tab/visual-studio)

Erforderliche Komponenten:

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

So erstellen Ihr erste Projekt mit Razor-Komponenten in Visual Studio:

1. Installieren Sie das neueste [.NET Core 3.0 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) freizugeben.
1. Aktivieren Sie die Visual Studio Preview-SDKs verwenden:
   1. Open **Tools** > **Optionen** in der Menüleiste.
   1. Öffnen der **Projekte und Projektmappen** Knoten. Öffnen der **.NET Core** Registerkarte.
   1. Aktivieren Sie das Kontrollkästchen **verwenden Sie die Preview-Versionen von .NET Core SDK**. Klicken Sie auf **OK**.
1. Erstellen Sie ein neues Projekt.
1. Wählen Sie **ASP.NET Core-Webanwendung** aus. Klicken Sie auf **Weiter**.
1. Geben Sie einen Namen in der **Projektname** Feld. Vergewissern Sie sich die **Speicherort** Eintrag richtig ist, oder geben Sie einen Speicherort für das Projekt. Wählen Sie **Erstellen** aus.
1. Stellen Sie sicher, dass **.NET Core** und **ASP.NET Core 3.0** oben ausgewählt werden.
1. Wählen Sie die **Razor-Komponenten** Vorlage, und wählen **erstellen**.
1. Drücken Sie **F5**, um die App auszuführen.

Herzlichen Glückwunsch! Sie haben soeben Ihre erste app mit Razor-Komponenten ausgeführt!

<!--

# [Visual Studio Code](#tab/visual-studio-code)

Prerequisites:

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

To create your first Razor Components project in Visual Studio Code:

1. Execute the following command from a command shell:

   ```console
   dotnet new razorcomponents -o WebApplication1
   ```

1. Open the *WebApplication1* folder in Visual Studio Code.

1. Add a *.vscode* folder.

1. Add a *tasks.json* file to the *.vscode* folder with the following content:

   [!code-json[](get-started/samples_snapshot/3.x/tasks.json)]

1. Add a *launch.json* file to the *.vscode* folder with the following content:

   [!code-json[](get-started/samples_snapshot/3.x/launch.json)]

1. Execute the app using the Visual Studio Code debugger.

1. In a browser, navigate to `https://localhost:5001`.

Congratulations! You just ran your first Razor Components app!

# [Visual Studio for Mac](#tab/visual-studio-mac)

.NET Core 3.0 will be supported with Visual Studio for Mac version 8.0 or later. Visual Studio for Mac version 8.0 Preview isn't available at this time.

Use the [.NET Core CLI version of this topic](xref:razor-components/get-started?tabs=netcore-cli) on macOS.

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

To create your first project Razor Components project in Visual Studio for Mac:

1. Select **File** > **New Solution** or **New Project**.
1. In the sidebar, select **.NET Core** > **App**.
1. Select **ASP.NET Core Razor Components** and select **Next**.
1. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.
1. In the **Project Name** field, enter `WebApplication1`. Select **Create**.
1. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

Congratulations! You just ran your first Razor Components app!
-->

# [<a name="net-core-cli"></a>.NET Core-CLI](#tab/netcore-cli/)

Erforderliche Komponenten:

* [.NET Core SDK 3.0 (Vorschau)](https://dotnet.microsoft.com/download/dotnet-core/3.0)

1. So erstellen Sie Ihr erste Projekt mit Razor-Komponenten über eine Befehlsshell:

   ```console
   dotnet new razorcomponents -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

1. Navigieren Sie in einem Browser zu `https://localhost:5001`.

Herzlichen Glückwunsch! Sie haben soeben Ihre erste app mit Razor-Komponenten ausgeführt!

---

## <a name="razor-components-project"></a>Razor-Komponenten-Projekt

Razor-Komponenten werden mithilfe der Razor-Syntax erstellt, aber anders als die Razor-Seiten und MVC-Ansichten kompiliert werden. Die *.razor* Dateierweiterung wird an eine Razor-Komponente verwendet. Razor-Seiten und MVC Ansichten verwenden weiterhin die *.cshtml* Dateierweiterung.

> [!NOTE]
> Razor-Komponenten können erstellt werden, mithilfe der *.cshtml* Dateierweiterung aus, solange diese Dateien als Komponente der Razor-Dateien, die mit identifiziert werden die `_RazorComponentInclude` MSBuild-Eigenschaft. Eine app, die mit der Komponente der Razor-Vorlage erstellt z. B. gibt an, dass alle *.cshtml* Dateien unter den *Komponenten* Ordner als Razor-Komponenten behandelt werden soll:
>
> ```xml
> <_RazorComponentInclude>Components\**\*.cshtml</_RazorComponentInclude>
> ```

Wenn die app ausgeführt wird, stehen mehrere Seiten von Registerkarten in der Randleiste aus:

* Startseite
* Zähler
* Abrufen der Daten

Wählen Sie auf der Seite „Counter“ die Schaltfläche **Hier klicken** aus, um den Zähler ohne Seitenaktualisierung heraufzusetzen. Das Heraufsetzen eines Zählers auf einer Webseite erfordert normalerweise das Schreiben in JavaScript, aber Razor Components bietet einen besseren Ansatz mit C#.

*WebApplication1/Components/Pages/Counter.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor)]

Eine Anforderung für `/counter` im Browser, laut der `@page` -Direktive am Anfang, bewirkt, dass die Leistungsindikator-Komponente, um seinen Inhalt zu rendern. Rendern von Komponenten in eine in-Memory-Darstellung der Render-Struktur, die zur Aktualisierung der Benutzeroberfläche in eine flexible und effiziente Möglichkeit verwendet werden kann.

Jedes Mal die **hier klicken** ausgewählt ist:

* Die `onclick` Ereignis wird ausgelöst.
* Die `IncrementCount` -Methode wird aufgerufen.
* Die `currentCount` wird erhöht.
* Die Komponente wird erneut gerendert.

Die Runtime vergleicht den neuen Inhalt zum vorherigen Inhalt und den geänderten Inhalt gilt nur, die (DOKUMENTOBJEKTMODELL).

Fügen Sie eine Komponente an eine andere Komponente, die mithilfe einer HTML-ähnliche Syntax. Parameter der Komponente werden mithilfe von Attributen oder untergeordneten Inhalt angegeben. Eine Komponente eines Leistungsindikators kann z. B. an der app-Startseite hinzugefügt werden, durch das Hinzufügen einer `<Counter />` Element an die Index-Komponente.

*WebApplication1/Components/Pages/Index.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

Führen Sie die App aus. Die Startseite verfügt über eine eigene Leistungsindikator.

Um einen Parameter an die Leistungsindikator-Komponente hinzuzufügen, aktualisieren Sie der Komponente `@functions` blockieren:

* Hinzufügen einer Eigenschaft für `IncrementAmount` ergänzt die `[Parameter]` Attribut.
* Ändern Sie die `IncrementCount`-Methode, um `IncrementAmount` beim Heraufsetzen des Werts von `currentCount` zu verwenden.

*WebApplication1/Components/Pages/Counter.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=4,8)]

Geben Sie unter Verwendung eines Attributs einen `IncrementAmount`-Parameter in das `<Counter>`-Element der Home-Komponente ein.

*WebApplication1/Components/Pages/Index.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor)]

Führen Sie die App aus. Die Startseite verfügt über eine eigene Zähler, der mit zehn jedes Mal erhöht die **hier klicken** ausgewählt ist.

## <a name="next-steps"></a>Nächste Schritte

<xref:tutorials/first-razor-components-app>
