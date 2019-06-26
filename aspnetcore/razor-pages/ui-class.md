---
title: Wiederverwendbare Razor-Benutzeroberfläche in Klassenbibliotheken mit ASP.NET Core
author: Rick-Anderson
description: Es wird erläutert, wie wiederverwendbare Teilansichten in einer Klassenbibliothek in ASP.NET Core mit Razor-Benutzeroberfläche zu erstellen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 06/24/2019
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: 96ef8fc055a6b92cd0808d02031d917b8446f305
ms.sourcegitcommit: 763af2cbdab0da62d1f1cfef4bcf787f251dfb5c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2019
ms.locfileid: "67394743"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a>Erstellen von wiederverwendbaren Benutzeroberfläche, die mit der Razor-Klassenbibliotheksprojekt in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

Razor-Ansichten, Seiten, Controller, seitenmodelle, [Razor-Komponenten](xref:blazor/class-libraries), [Ansichtskomponenten](xref:mvc/views/view-components), Datenmodelle in einer Razor-Klassenbibliothek (RCL) erstellt werden können. Die RCL kann verpackt und wiederverwendet werden. Sie kann in Anwendungen eingebunden werden, wodurch sich die in der RCL enthaltenen Ansichten und Seiten überschreiben lassen. Wenn eine Ansicht, Teilansicht oder Razor-Seite sowohl in der Web-App als auch in der RCL enthalten ist, hat das Razor-Markup (die *CSHTML*-Datei) in der Web-App Vorrang.

Dieses Feature erfordert [!INCLUDE[](~/includes/2.1-SDK.md)]

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-razor-ui"></a>Erstellen einer Klassenbibliothek, die eine Razor-Benutzeroberfläche enthält

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.
* Wählen Sie **ASP.NET Core-Webanwendung** aus.
* Geben Sie der Bibliothek einen Namen (z.B. „RazorClassLib“), und klicken Sie anschließend auf **OK**. Stellen Sie sicher, dass der Bibliotheksname nicht auf `.Views` endet, um zu verhindern, dass es zu einem Dateinamenkonflikt mit der generierten Ansichtsbibliothek kommt.
* Überprüfen Sie, ob **ASP.NET Core 2.1** oder höher ausgewählt ist.
* Klicken Sie auf **Razor Class Library** (Razor-Klassenbibliothek) > **OK**.

Ein RCL hat die folgende Projektdatei:

[!code-xml[Main](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Führen Sie `dotnet new razorclasslib` über die Befehlszeile aus. Zum Beispiel:

```console
dotnet new razorclasslib -o RazorUIClassLib
```

Weitere Informationen finden Sie unter [dotnet new](/dotnet/core/tools/dotnet-new). Stellen Sie sicher, dass der Bibliotheksname nicht auf `.Views` endet, um zu verhindern, dass es zu einem Dateinamenkonflikt mit der generierten Ansichtsbibliothek kommt.

---

Fügen Sie der RCL Razor-Dateien hinzu.

ASP.NET Core-Vorlagen wird davon ausgegangen, der RCL Inhalt befindet sich in der *Bereiche* Ordner. Finden Sie unter [RCL Seitenlayout](#afs) zum Erstellen der Inhalte in ein RCL, die verfügbar macht `~/Pages` statt `~/Areas/Pages`.

## <a name="referencing-rcl-content"></a>Verweisen auf RCL Inhalt

Folgende Komponenten können auf die RCL verweisen:

* NuGet-Pakete. Weitere Informationen finden Sie unter [Creating NuGet packages](/nuget/create-packages/creating-a-package) (Erstellen von NuGet-Paketen), [dotnet add package](/dotnet/core/tools/dotnet-add-package) und [Erstellen und Veröffentlichen eines NuGet-Pakets](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{ProjectName}.csproj*-Dateien. Weitere Informationen finden Sie unter [dotnet add reference](/dotnet/core/tools/dotnet-add-reference).

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a>Exemplarische Vorgehensweise: Erstellen eines Projekts RCL aus und ein Projekt mit Razor Pages

Sie können das [vollständige Projekt](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) herunterladen und testen, anstatt es zu erstellen. Der Beispieldownload enthält zusätzlichen Code sowie Links, die das Testen des Projekts erleichtern. [In diesem GitHub-Issue](https://github.com/aspnet/AspNetCore.Docs/issues/6098) können Sie in Form von Kommentaren Feedback zu Unterschieden zwischen Beispieldownloads und ausführlichen Anleitungen geben.

### <a name="test-the-download-app"></a>Testen der heruntergeladenen App

Wenn Sie die vollständige App nicht heruntergeladen haben und das Beispielprojekt selbst erstellen möchten, können Sie mit dem [nächsten Abschnitt](#create-an-rcl) fortfahren.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Öffnen Sie die *SLN*-Datei in Visual Studio. Führen Sie die App aus.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Erstellen Sie über die Eingabeaufforderung im *cli*-Verzeichnis die RCL und die Web-App.

```console
dotnet build
```

Wechseln Sie ins Verzeichnis *WebApp1*, und führen Sie die App aus:

```console
dotnet run
```

---

Folgen Sie den Anweisungen in [Testen des WebApp1-Projekts](#test).

## <a name="create-an-rcl"></a>Erstellen Sie eine RCL

In diesem Abschnitt wird ein RCL erstellt. Dabei werden der RCL Razor-Dateien hinzugefügt.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Erstellen Sie das RCL-Projekt:

* Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.
* Wählen Sie **ASP.NET Core-Webanwendung** aus.
* Benennen Sie die app **RazorUIClassLib** > **OK**.
* Überprüfen Sie, ob **ASP.NET Core 2.1** oder höher ausgewählt ist.
* Klicken Sie auf **Razor Class Library** (Razor-Klassenbibliothek) > **OK**.
* Fügen Sie eine Razor-Datei für die Teilansicht mit dem Namen „_Message.cshtml“ unter *RazorUIClassLib/Areas/MyFeature/Pages/Shared* hinzu.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Führen Sie die folgenden Befehle über die Befehlszeile aus:

```console
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

Die obenstehenden Befehle haben folgende Konsequenzen:

* Erstellt die `RazorUIClassLib` RCL.
* Die Seite „Razor_Message“ wird erstellt und der RCL hinzugefügt. Durch den `-np`-Parameter wird die Seite ohne `PageModel` erstellt.
* Erstellt eine [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) Datei und fügt es der RCL hinzu.

Die *_ViewStart.cshtml* Datei ist erforderlich, um das Layout des Projekts mit Razor Pages verwenden (die im nächsten Abschnitt hinzugefügt wird).

---

### <a name="add-razor-files-and-folders-to-the-project"></a>Razor-Dateien und Ordner zum Projekt hinzufügen

* Ersetzen Sie das Markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* durch den folgenden Code:

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* Ersetzen Sie das Markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* durch den folgenden Code:

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` ist erforderlich, um die Teilansicht `<partial name="_Message" />` verwenden zu können. Wenn Sie nicht die `@addTagHelper`-Anweisung einbinden möchten, können Sie die Datei *_ViewImports.cshtml* hinzufügen. Zum Beispiel:

```console
dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
```

Weitere Informationen zu *_ViewImports.cshtml*, finden Sie unter [freigegebenen Richtlinien importieren](xref:mvc/views/layout#importing-shared-directives)

* Erstellen Sie die Klassenbibliothek, um sicherzustellen, dass keine Compilerfehler vorliegen:

```console
dotnet build RazorUIClassLib
```

Die Buildausgabe enthält *RazorUIClassLib.dll* und *RazorUIClassLib.Views.dll*. *RazorUIClassLib.Views.dll* enthält den kompilierten Razor-Inhalt.

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a>Verwenden der Razor-Benutzeroberflächenbibliothek über ein Razor-Seiten-Projekt

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Erstellen Sie die Web-App mit Razor-Seiten:

* Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Projektmappe und anschließend auf **Hinzufügen** >  **Neues Projekt**.
* Wählen Sie **ASP.NET Core-Webanwendung** aus.
* Legen Sie als Namen für die App **WebApp1** fest.
* Überprüfen Sie, ob **ASP.NET Core 2.1** oder höher ausgewählt ist.
* Klicken Sie auf **Webanwendung** > **OK**.

* Klicken Sie im **Projektmappen-Explorer** zuerst mit der rechten Maustaste auf **WebApp1** und anschließend auf **Als Startprojekt festlegen**.
* Klicken Sie im **Projektmappen-Explorer** zuerst mit der rechten Maustaste auf **WebApp1** und anschließend auf **Buildabhängigkeiten** > **Projektabhängigkeiten**.
* Aktivieren Sie für **WebApp1** die Abhängigkeit **RazorUIClassLib**.
* Klicken Sie im **Projektmappen-Explorer** zuerst mit der rechten Maustaste auf **WebApp1** und anschließend auf **Hinzufügen** > **Verweis**.
* Aktivieren Sie im Dialogfeld **Verweis-Manager** das Kontrollkästchen neben **RazorUIClassLib**, und klicken Sie anschließend auf **OK**.

Führen Sie die App aus.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Erstellen einer Razor Pages-Web-app und eine Projektmappendatei, die die Razor-Seiten-app und die RCL enthält:

```console
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

Erstellen Sie die Web-App, und führen Sie sie aus:

```console
cd WebApp1
dotnet run
```

---

<a name="test"></a>

### <a name="test-webapp1"></a>Testen des WebApp1-Projekts

Stellen Sie sicher, dass die Benutzeroberfläche der Razor-Klassenbibliothek verwendet wird:

* Wechseln Sie zu `/MyFeature/Page1`.

## <a name="override-views-partial-views-and-pages"></a>Überschreiben von Ansichten, Teilansichten und Seiten

Wenn eine Ansicht, Teilansicht oder Razor-Seite sowohl in der Web-App als auch in der RCL enthalten ist, hat das Razor-Markup (die *CSHTML*-Datei) in der Web-App Vorrang. Fügen Sie z. B. *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* auf "WebApp1", und Seite1 aus dem "WebApp1" Vorrang vor Seite1 aus dem RCL.

Ob Page1 in WebApp1 tatsächlich die Priorität zugeordnet wird, können Sie testen, indem Sie im Beispieldownload *WebApp1/Areas/MyFeature2* in *WebApp1/Areas/MyFeature* umbenennen.

Kopieren Sie die Teilansicht *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* nach *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*. Aktualisieren Sie das Markup, um den neuen Speicherort anzugeben. Erstellen Sie die App, und führen Sie sie aus, um sicherzustellen, dass die Version mit der Teilansicht der App verwendet wird.

<a name="afs"></a>

### <a name="rcl-pages-layout"></a>RCL Seitenlayout

Auf Inhalt RCL, als wäre es Teil der Web-app-Verweis *Seiten* Ordner das RCL-Projekt erstellen, mit der folgenden Dateistruktur:

* *RazorUIClassLib/Seiten*
* *RazorUIClassLib/Pages/Shared*

Nehmen wir an *RazorUIClassLib/Pages/Shared* enthält zwei partielle Dateien: *_Header.cshtml* und *_Footer.cshtml*. Die `<partial>` können Tags hinzugefügt werden *"_Layout.cshtml"* Datei:

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a>Erstellen Sie eine RCL mit statischen Objekten

Ein RCL unter Umständen Companion statische Ressourcen, die von der Nutzung der RCL-app verwiesen werden kann. ASP.NET Core ermöglicht das Erstellen von RCLs, die statische Ressourcen enthalten, die für eine Nutzung app verfügbar sind.

Wenn zugehörige Ressourcen als Teil einer RCL einschließen möchten, erstellen Sie eine *"Wwwroot"* Ordner in der Klassenbibliothek und alle erforderlichen Dateien in diesem Ordner enthalten.

Beim Packen einer RCL, companion alle Ressourcen in der *"Wwwroot"* Ordner automatisch im Paket enthalten sind, und verweisen auf das Paket-Apps zur Verfügung gestellt.

### <a name="consume-content-from-a-referenced-rcl"></a>Nutzen von Inhalten aus einer referenzierten RCL

Die Dateien in die *"Wwwroot"* Ordner die RCL werden verfügbar gemacht, an die verarbeitende-app unter dem Präfix `_content/{LIBRARY NAME}/`. `{LIBRARY NAME}` ist der Name des Klassenbibliothekprojekts konvertiert Kleinbuchstaben durch Punkte (`.`) entfernt. Z. B. eine Bibliothek, die mit dem Namen *Razor.Class.Lib* führt zu einem Pfad an statische Inhalte auf `_content/razorclasslib/`.

Die Consumer-app verweist auf statische Objekte, die von der Bibliothek mit bereitgestellt `<script>`, `<style>`, `<img>`, und andere HTML-Tags. Die Consumer-app benötigen [statische dateiunterstützung](xref:fundamentals/static-files) aktiviert.

### <a name="multi-project-development-flow"></a>Mit mehreren Projekten Entwicklungsablauf

Ausführung der verarbeitende Anwendung:

* Die Ressourcen in der RCL behalten die ursprünglichen Ordner. Die Ressourcen werden nicht an die verarbeitende app verschoben werden.
* Alle Änderungen innerhalb der RCL des *"Wwwroot"* Ordner wird in der verwendeten app, nachdem die RCL neu erstellt wird und ohne Neuerstellung von der verwendeten app wiedergegeben.

Wenn die RCL erstellt wird, wird ein Manifest erstellt, die den statischen Asset-Websites zu beschreiben. Die verwendete app liest das Manifest zur Laufzeit, um die Objekte aus referenzierten Projekten und Paketen verwenden. Wenn ein RCL ein neues Asset hinzugefügt wird, muss die RCL neu erstellt werden, um das Manifest zu aktualisieren, bevor eine verarbeitende Anwendung das neue Medienobjekt zugreifen kann.

### <a name="publish"></a>Veröffentlichen

Wenn die app veröffentlicht wird, werden die Begleit-Ressourcen über alle referenzierten Projekten und Paketen in kopiert die *"Wwwroot"* Ordner der veröffentlichten app unter `_content/{LIBRARY NAME}/`.
