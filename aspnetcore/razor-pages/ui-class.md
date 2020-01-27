---
title: Wiederverwendbare Razor-Benutzeroberfläche in Klassenbibliotheken mit ASP.NET Core
author: Rick-Anderson
description: Es wird erläutert, wie wiederverwendbare Teilansichten in einer Klassenbibliothek in ASP.NET Core mit Razor-Benutzeroberfläche zu erstellen.
ms.author: riande
ms.date: 10/26/2019
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: b5235f4e31f6edd21fb410824fb215ab2d4a41b6
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76727288"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a>Erstellen wiederverwendbarer Benutzeroberflächen mithilfe des Razor-Klassen Bibliotheks Projekts in ASP.net Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Razor-Ansichten, Seiten, Controller, Seiten Modelle, [Razor-Komponenten](xref:blazor/class-libraries), [Ansichts Komponenten](xref:mvc/views/view-components)und Datenmodelle können in eine Razor-Klassenbibliothek (RCL) integriert werden. Die RCL kann verpackt und wiederverwendet werden. Sie kann in Anwendungen eingebunden werden, wodurch sich die in der RCL enthaltenen Ansichten und Seiten überschreiben lassen. Wenn eine Ansicht, Teilansicht oder Razor-Seite sowohl in der Web-App als auch in der RCL enthalten ist, hat das Razor-Markup (die *CSHTML*-Datei) in der Web-App Vorrang.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-razor-ui"></a>Erstellen einer Klassenbibliothek, die eine Razor-Benutzeroberfläche enthält

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Wählen Sie in Visual Studio die Option **Neues Projekt erstellen**aus.
* Wählen **Sie >** **Razor-Klassenbibliothek** aus.
* Benennen Sie die Bibliothek (z. b. "razorclasslib"), > **Erstellen**. Stellen Sie sicher, dass der Bibliotheksname nicht auf `.Views` endet, um zu verhindern, dass es zu einem Dateinamenkonflikt mit der generierten Ansichtsbibliothek kommt.
* Wählen Sie **Supportseiten und Sichten** aus, wenn Sie Ansichten unterstützen müssen. Standardmäßig werden nur Razor Pages unterstützt. Wählen Sie **Erstellen** aus.

Die Vorlage der Razor-Klassenbibliothek (Razor Class Library, RCL) gilt standardmäßig für die Razor-Komponentenentwicklung. Die Option **Support Pages und Views** unterstützt Seiten und Ansichten.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Führen Sie `dotnet new razorclasslib` über die Befehlszeile aus. Beispiel:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

Die Vorlage der Razor-Klassenbibliothek (Razor Class Library, RCL) gilt standardmäßig für die Razor-Komponentenentwicklung. Übergeben Sie die Option `--support-pages-and-views` (`dotnet new razorclasslib --support-pages-and-views`), um Unterstützung für Seiten und Ansichten bereitzustellen.

Weitere Informationen finden Sie unter [dotnet new](/dotnet/core/tools/dotnet-new). Stellen Sie sicher, dass der Bibliotheksname nicht auf `.Views` endet, um zu verhindern, dass es zu einem Dateinamenkonflikt mit der generierten Ansichtsbibliothek kommt.

---

Fügen Sie der RCL Razor-Dateien hinzu.

ASP.NET Core-Vorlagen wird davon ausgegangen, der RCL Inhalt befindet sich in der *Bereiche* Ordner. Weitere Informationen finden Sie unter [RCL Pages Layout](#rcl-pages-layout) zum Erstellen einer RCL, die Inhalte in `~/Pages` und nicht in `~/Areas/Pages`verfügbar macht.

## <a name="reference-rcl-content"></a>Verweisen auf den RCL-Inhalt

Folgende Komponenten können auf die RCL verweisen:

* NuGet-Pakete. Weitere Informationen finden Sie unter [Creating NuGet packages](/nuget/create-packages/creating-a-package) (Erstellen von NuGet-Paketen), [dotnet add package](/dotnet/core/tools/dotnet-add-package) und [Erstellen und Veröffentlichen eines NuGet-Pakets](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{ProjectName}.csproj*-Dateien. Weitere Informationen finden Sie unter [dotnet add reference](/dotnet/core/tools/dotnet-add-reference).

## <a name="override-views-partial-views-and-pages"></a>Überschreiben von Ansichten, Teilansichten und Seiten

Wenn eine Ansicht, Teilansicht oder Razor-Seite sowohl in der Web-App als auch in der RCL enthalten ist, hat das Razor-Markup (die *CSHTML*-Datei) in der Web-App Vorrang. Fügen Sie z. b. *"WebApp1"/Areas/myfeature/Pages/"Page1. cshtml* zu" WebApp1 "hinzu, und" Page1 im "WebApp1" hat Vorrang vor "Page1 in der RCL.

Ob Page1 in WebApp1 tatsächlich die Priorität zugeordnet wird, können Sie testen, indem Sie im Beispieldownload *WebApp1/Areas/MyFeature2* in *WebApp1/Areas/MyFeature* umbenennen.

Kopieren Sie die Teilansicht *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* nach *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*. Aktualisieren Sie das Markup, um den neuen Speicherort anzugeben. Erstellen Sie die App, und führen Sie sie aus, um sicherzustellen, dass die Version mit der Teilansicht der App verwendet wird.

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

## <a name="create-an-rcl-with-static-assets"></a>Erstellen einer RCL mit statischen Assets

Eine RCL erfordert möglicherweise begleitende statische Ressourcen, auf die von der Verb raubenden App der RCL verwiesen werden kann. ASP.net Core ermöglicht das Erstellen von rcls, die statische Ressourcen enthalten, die für eine verarbeitende app verfügbar sind.

Wenn Sie begleitende Assets als Teil einer RCL einschließen möchten, erstellen Sie in der Klassenbibliothek einen Ordner *wwwroot* , und fügen Sie alle erforderlichen Dateien in diesen Ordner ein.

Beim Packen einer RCL werden alle Begleit Objekte im Ordner " *wwwroot* " automatisch in das Paket eingefügt.

### <a name="exclude-static-assets"></a>Statische Assets ausschließen

Fügen Sie den gewünschten Ausschluss Pfad zur `$(DefaultItemExcludes)` Eigenschaften Gruppe in der Projektdatei hinzu, um statische Assets auszuschließen. Trennen Sie Einträge durch ein Semikolon (`;`).

Im folgenden Beispiel wird das *lib. CSS* -Stylesheet im Ordner " *wwwroot* " nicht als statisches Asset angesehen und ist nicht in der veröffentlichten RCL enthalten:

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a>Typescript-Integration

So fügen Sie typescript-Dateien in eine RCL ein:

1. Platzieren Sie die typescript-Dateien (*TS*) außerhalb des Ordners *wwwroot* . Platzieren Sie die Dateien z. b. in einem *Client* Ordner.

1. Konfigurieren Sie die typescript-Buildausgabe für den Ordner " *wwwroot* ". Legen Sie die `TypescriptOutDir`-Eigenschaft innerhalb einer `PropertyGroup` in der Projektdatei fest:

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. Fügen Sie das typescript-Ziel als Abhängigkeit des `ResolveCurrentProjectStaticWebAssets` Ziels ein, indem Sie das folgende Ziel in einer `PropertyGroup` in der Projektdatei hinzufügen:

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a>Nutzen von Inhalten aus einer referenzierten RCL

Die Dateien, die im Ordner " *wwwroot* " der RCL enthalten sind, werden für die verbrauchende app unter dem Präfix `_content/{LIBRARY NAME}/`verfügbar gemacht. Eine Bibliothek mit dem Namen *Razor. Class. lib* führt z. b. zu einem Pfad zu statischem Inhalt `_content/Razor.Class.Lib/`. Wenn Sie ein nuget-Paket erstellen und der Assemblyname nicht mit der Paket-ID übereinstimmt, verwenden Sie die Paket-ID für `{LIBRARY NAME}`.

Die verarbeitende App verweist auf statische Ressourcen, die von der Bibliothek mit `<script>`, `<style>`, `<img>`und anderen HTML-Tags bereitgestellt werden. Für die verarbeitende app muss die [Unterstützung statischer Dateien](xref:fundamentals/static-files) in `Startup.Configure`aktiviert sein:

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

Wenn Sie die Verb raubende App aus der Buildausgabe (`dotnet run`) ausführen, werden statische Webressourcen standardmäßig in der Entwicklungsumgebung aktiviert. Um Assets in anderen Umgebungen bei Ausführung über die Buildausgabe zu unterstützen, wenden Sie `UseStaticWebAssets` auf dem Host-Generator in *Program.cs*an:

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

Das Aufrufen von `UseStaticWebAssets` ist nicht erforderlich, wenn eine APP aus der veröffentlichten Ausgabe (`dotnet publish`) ausgeführt wird.

### <a name="multi-project-development-flow"></a>Entwicklungsfluss für mehrere Projekte

Wenn die verarbeitende app ausgeführt wird:

* Die Assets in der RCL bleiben in ihren ursprünglichen Ordnern. Die Assets werden nicht in die Verb raubende App verschoben.
* Jede Änderung im Ordner " *wwwroot* " der RCL wird in der nutzenden App widergespiegelt, nachdem die RCL neu erstellt wurde und ohne die Verb raubende APP neu zu erstellen.

Wenn die RCL erstellt wurde, wird ein Manifest erstellt, in dem die statischen webasset-Speicherorte beschrieben werden. Die Verb raubende App liest das Manifest zur Laufzeit, um die Assets aus referenzierten Projekten und Paketen zu nutzen. Wenn einer RCL ein neues Asset hinzugefügt wird, muss die RCL neu erstellt werden, um das Manifest zu aktualisieren, bevor eine verarbeitende App auf das neue Medienobjekt zugreifen kann.

### <a name="publish"></a>Veröffentlichungs

Wenn die App veröffentlicht wird, werden die begleitenden Objekte aus allen referenzierten Projekten und Paketen in den Ordner *wwwroot* der veröffentlichten app unter `_content/{LIBRARY NAME}/`kopiert.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Razor-Ansichten, Seiten, Controller, Seiten Modelle, [Razor-Komponenten](xref:blazor/class-libraries), [Ansichts Komponenten](xref:mvc/views/view-components)und Datenmodelle können in eine Razor-Klassenbibliothek (RCL) integriert werden. Die RCL kann verpackt und wiederverwendet werden. Sie kann in Anwendungen eingebunden werden, wodurch sich die in der RCL enthaltenen Ansichten und Seiten überschreiben lassen. Wenn eine Ansicht, Teilansicht oder Razor-Seite sowohl in der Web-App als auch in der RCL enthalten ist, hat das Razor-Markup (die *CSHTML*-Datei) in der Web-App Vorrang.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-razor-ui"></a>Erstellen einer Klassenbibliothek, die eine Razor-Benutzeroberfläche enthält

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.
* Wählen Sie **ASP.NET Core-Webanwendung** aus.
* Geben Sie der Bibliothek einen Namen (z.B. „RazorClassLib“), und klicken Sie anschließend auf **OK**. Stellen Sie sicher, dass der Bibliotheksname nicht auf `.Views` endet, um zu verhindern, dass es zu einem Dateinamenkonflikt mit der generierten Ansichtsbibliothek kommt.
* Überprüfen Sie, ob **ASP.NET Core 2.1** oder höher ausgewählt ist.
* Wählen Sie **Razor-Klassenbibliothek** > **OK**aus.

Eine RCL hat die folgende Projektdatei:

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Führen Sie `dotnet new razorclasslib` über die Befehlszeile aus. Beispiel:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

Weitere Informationen finden Sie unter [dotnet new](/dotnet/core/tools/dotnet-new). Stellen Sie sicher, dass der Bibliotheksname nicht auf `.Views` endet, um zu verhindern, dass es zu einem Dateinamenkonflikt mit der generierten Ansichtsbibliothek kommt.

---

Fügen Sie der RCL Razor-Dateien hinzu.

ASP.NET Core-Vorlagen wird davon ausgegangen, der RCL Inhalt befindet sich in der *Bereiche* Ordner. Weitere Informationen finden Sie unter [RCL Pages Layout](#rcl-pages-layout) zum Erstellen einer RCL, die Inhalte in `~/Pages` und nicht in `~/Areas/Pages`verfügbar macht.

## <a name="reference-rcl-content"></a>Verweisen auf den RCL-Inhalt

Folgende Komponenten können auf die RCL verweisen:

* NuGet-Pakete. Weitere Informationen finden Sie unter [Creating NuGet packages](/nuget/create-packages/creating-a-package) (Erstellen von NuGet-Paketen), [dotnet add package](/dotnet/core/tools/dotnet-add-package) und [Erstellen und Veröffentlichen eines NuGet-Pakets](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{ProjectName}.csproj*-Dateien. Weitere Informationen finden Sie unter [dotnet add reference](/dotnet/core/tools/dotnet-add-reference).

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a>Exemplarische Vorgehensweise: Erstellen eines RCL-Projekts und verwenden aus einem Razor Pages Projekt

Sie können das [vollständige Projekt](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) herunterladen und testen, anstatt es zu erstellen. Der Beispieldownload enthält zusätzlichen Code sowie Links, die das Testen des Projekts erleichtern. [In diesem GitHub-Issue](https://github.com/aspnet/AspNetCore.Docs/issues/6098) können Sie in Form von Kommentaren Feedback zu Unterschieden zwischen Beispieldownloads und ausführlichen Anleitungen geben.

### <a name="test-the-download-app"></a>Testen der heruntergeladenen App

Wenn Sie die vollständige App nicht heruntergeladen haben und das Beispielprojekt selbst erstellen möchten, können Sie mit dem [nächsten Abschnitt](#create-an-rcl) fortfahren.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Öffnen Sie die *SLN*-Datei in Visual Studio. Führen Sie die App aus.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Erstellen Sie über die Eingabeaufforderung im *cli*-Verzeichnis die RCL und die Web-App.

```dotnetcli
dotnet build
```

Wechseln Sie ins Verzeichnis *WebApp1*, und führen Sie die App aus:

```dotnetcli
dotnet run
```

---

Folgen Sie den Anweisungen in [Testen des WebApp1-Projekts](#test-webapp1).

## <a name="create-an-rcl"></a>Erstellen einer RCL

In diesem Abschnitt wird eine RCL erstellt. Dabei werden der RCL Razor-Dateien hinzugefügt.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Erstellen Sie das RCL-Projekt:

* Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.
* Wählen Sie **ASP.NET Core-Webanwendung** aus.
* Nennen Sie die APP **razoruiclasslib** > **OK**.
* Überprüfen Sie, ob **ASP.NET Core 2.1** oder höher ausgewählt ist.
* Wählen Sie **Razor-Klassenbibliothek** > **OK**aus.
* Fügen Sie eine Razor-Datei für die Teilansicht mit dem Namen „_Message.cshtml“ unter *RazorUIClassLib/Areas/MyFeature/Pages/Shared* hinzu.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Führen Sie die folgenden Befehle über die Befehlszeile aus:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

Die obenstehenden Befehle haben folgende Konsequenzen:

* Erstellt den `RazorUIClassLib` RCL.
* Die Seite „Razor_Message“ wird erstellt und der RCL hinzugefügt. Durch den `-np`-Parameter wird die Seite ohne `PageModel` erstellt.
* Erstellt eine [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) Datei und fügt es der RCL hinzu.

Die *_ViewStart.cshtml* Datei ist erforderlich, um das Layout des Projekts mit Razor Pages verwenden (die im nächsten Abschnitt hinzugefügt wird).

---

### <a name="add-razor-files-and-folders-to-the-project"></a>Razor-Dateien und Ordner zum Projekt hinzufügen

* Ersetzen Sie das Markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* durch den folgenden Code:

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* Ersetzen Sie das Markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* durch den folgenden Code:

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` ist erforderlich, um die Teilansicht `<partial name="_Message" />` verwenden zu können. Wenn Sie nicht die `@addTagHelper`-Anweisung einbinden möchten, können Sie die Datei *_ViewImports.cshtml* hinzufügen. Beispiel:

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  Weitere Informationen zu *_ViewImports.cshtml*, finden Sie unter [freigegebenen Richtlinien importieren](xref:mvc/views/layout#importing-shared-directives)

* Erstellen Sie die Klassenbibliothek, um sicherzustellen, dass keine Compilerfehler vorliegen:

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

Die Buildausgabe enthält *RazorUIClassLib.dll* und *RazorUIClassLib.Views.dll*. *RazorUIClassLib.Views.dll* enthält den kompilierten Razor-Inhalt.

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a>Verwenden der Razor-Benutzeroberflächenbibliothek über ein Razor-Seiten-Projekt

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Erstellen Sie die Web-App mit Razor-Seiten:

* Klicken Sie in **Projektmappen-Explorer**mit der rechten Maustaste auf die Projekt Mappe, > >**Neues Projekt** **hinzuzufügen** .
* Wählen Sie **ASP.NET Core-Webanwendung** aus.
* Legen Sie als Namen für die App **WebApp1** fest.
* Überprüfen Sie, ob **ASP.NET Core 2.1** oder höher ausgewählt ist.
* Wählen Sie **Webanwendung** > **OK**aus.

* Klicken Sie im **Projektmappen-Explorer** zuerst mit der rechten Maustaste auf **WebApp1** und anschließend auf **Als Startprojekt festlegen**.
* Klicken Sie in **Projektmappen-Explorer**mit der rechten Maustaste auf **"WebApp1"** , und wählen Sie Abhängigkeiten > **Projekt Abhängigkeiten** **Erstellen** aus.
* Aktivieren Sie für **WebApp1** die Abhängigkeit **RazorUIClassLib**.
* Klicken Sie in **Projektmappen-Explorer**mit der rechten Maustaste auf **"WebApp1"** , und wählen Sie > **Verweis** **Hinzufügen** aus.
* Aktivieren Sie im Dialogfeld **Verweis-Manager** die Option **razoruiclasslib** > **OK**.

Führen Sie die App aus.

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Erstellen Sie eine Razor Pages Web-App und eine Projektmappendatei, die die Razor Pages-APP und die RCL enthält:

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

Erstellen Sie die Web-App, und führen Sie sie aus:

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a>Testen des WebApp1-Projekts

Navigieren Sie zu `/MyFeature/Page1`, um zu überprüfen, ob die Razor-UI-Klassenbibliothek verwendet wird.

## <a name="override-views-partial-views-and-pages"></a>Überschreiben von Ansichten, Teilansichten und Seiten

Wenn eine Ansicht, Teilansicht oder Razor-Seite sowohl in der Web-App als auch in der RCL enthalten ist, hat das Razor-Markup (die *CSHTML*-Datei) in der Web-App Vorrang. Fügen Sie z. b. *"WebApp1"/Areas/myfeature/Pages/"Page1. cshtml* zu" WebApp1 "hinzu, und" Page1 im "WebApp1" hat Vorrang vor "Page1 in der RCL.

Ob Page1 in WebApp1 tatsächlich die Priorität zugeordnet wird, können Sie testen, indem Sie im Beispieldownload *WebApp1/Areas/MyFeature2* in *WebApp1/Areas/MyFeature* umbenennen.

Kopieren Sie die Teilansicht *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* nach *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*. Aktualisieren Sie das Markup, um den neuen Speicherort anzugeben. Erstellen Sie die App, und führen Sie sie aus, um sicherzustellen, dass die Version mit der Teilansicht der App verwendet wird.

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

::: moniker-end
