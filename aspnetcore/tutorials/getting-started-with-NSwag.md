---
title: Erste Schritte mit NSwag und ASP.NET Core
author: zuckerthoben
description: Erfahren Sie, wie Sie NSwag zum Generieren von Dokumentationen und Hilfeseiten für eine ASP.NET Core-Web-API-App verwenden können.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/18/2018
uid: tutorials/get-started-with-nswag
ms.openlocfilehash: 8af5bed1e042c4f6d83043b05084c51b3064a548
ms.sourcegitcommit: ea215df889e89db44037a6ac2f01baede0450da9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53595359"
---
# <a name="get-started-with-nswag-and-aspnet-core"></a>Erste Schritte mit NSwag und ASP.NET Core

Von [Christoph Nienaber](https://twitter.com/zuckerthoben) und [Rico Suter](https://rsuter.com)

::: moniker range=">= aspnetcore-2.1"

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

::: moniker-end

Registrieren Sie die NSwag-Middlewares bei:

* Generieren Sie die Swagger-Spezifikation für die implementierte Web-API.
* Stellen Sie die Swagger-Benutzeroberfläche zum Durchsuchen und Testen die Web-API bereit.

Um [NSwag](https://github.com/RSuter/NSwag) mit ASP.NET Core-Middleware zu verwenden, installieren Sie das NuGet-Paket [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/). Dieses Paket enthält die Middleware zum Generieren und Bereitstellen der Swagger-Spezifikation, der Swagger-Benutzeroberfläche (v2 und v3) und der [ReDoc-Benutzeroberfläche](https://github.com/Rebilly/ReDoc).

Außerdem wird dringend empfohlen, die Funktionen zur Codegenerierung von NSwag zu verwenden. Wählen Sie eine der folgenden Optionen aus, um die Codegenerierungsfunktionen zu verwenden:

* Verwenden Sie [NSwagStudio](https://github.com/NSwag/NSwag/wiki/NSwagStudio). Dies ist eine Windows-Desktop-App zum Generieren von Clientcode in C# und TypeScript für Ihre API.
* Verwenden Sie die NuGet-Pakete [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) oder [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/), um Code innerhalb des Projekts zu generieren.
* Verwenden Sie NSwag über die [Befehlszeile](https://github.com/NSwag/NSwag/wiki/CommandLine).
* Verwenden Sie das NuGet-Paket [NSwag.MSBuild](https://github.com/NSwag/NSwag/wiki/MSBuild).

## <a name="features"></a>Features

Der Hauptgrund für die Verwendung von NSwag liegt darin, dass nicht nur die Swagger-Benutzeroberfläche und der Swagger-Generator, sondern auch die flexiblen Funktionen für die Codegenerierung verwendet werden können. Sie müssen keine API besitzen, sondern können APIs von Drittanbietern verwenden, die Swagger integrieren, und NSwag eine Clientimplementierung generieren lassen. In jedem Fall wird der Entwicklungszyklus beschleunigt und Sie können sich schnell an API-Änderungen anpassen.

## <a name="package-installation"></a>Paketinstallation

Das NuGet-Paket „NSwag“ kann folgendermaßen hinzugefügt werden:

### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Aus dem Fenster **Paket-Manager-Konsole**:
  * Navigieren Sie zu **Ansicht** > **Weitere Fenster** > **Paket-Manager-Konsole**.
  * Navigieren Sie zu dem Verzeichnis, in dem die *TodoApi.csproj*-Datei gespeichert ist.
  * Führen Sie den folgenden Befehl aus:

    ```powershell
    Install-Package NSwag.AspNetCore
    ```

* Aus dem Dialogfeld **NuGet-Pakete verwalten**:
  * Klicken Sie mit der rechten Maustaste unter **Projektmappen-Explorer** > **NuGet-Pakete verwalten** auf Ihr Projekt.
  * Legen Sie die **Paketquelle** auf „nuget.org“ fest.
  * Geben Sie „NSwag.AspNetCore“ in das Suchfeld ein.
  * Wählen Sie das Paket „NSwag.AspNetCore“ auf der Registerkarte **Durchsuchen** aus, und klicken Sie auf **Installieren**.

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Klicken Sie mit der rechten Maustaste auf den Ordner *Pakete* unter **Lösungspad** > **Pakete hinzufügen...**.
* Legen Sie im Fenster **Pakete hinzufügen** das Dropdownmenü **Quelle** auf „nuget.org“ fest.
* Geben Sie „NSwag.AspNetCore“ in das Suchfeld ein.
* Wählen Sie das Paket „NSwag.AspNetCore“ aus dem Ergebnisbereich aus, und klicken Sie auf **Paket hinzufügen**.

### <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Führen Sie folgenden Befehl aus dem **integrierten Terminal** aus:

```console
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

### <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Führen Sie den folgenden Befehl aus:

```console
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a>Hinzufügen und Konfigurieren von Swagger-Middleware

Importieren Sie folgende Namespaces in die `Startup`-Klasse:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_StartupConfigureImports)]

Registrieren Sie in der `Startup.ConfigureServices`-Methode die erforderlichen Swagger-Dienste: 

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

Aktivieren Sie die Middleware in der `Startup.Configure`-Methode, um die generierte Swagger-Spezifikation und die Swagger-Benutzeroberfläche (v3) bereitzustellen:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-10)]

Starten Sie die App. Navigieren Sie zu `http://localhost:<port>/swagger`, um die Swagger-Benutzeroberfläche anzuzeigen. Navigieren Sie zu `http://localhost:<port>/swagger/v1/swagger.json`, um die Swagger-Spezifikation anzuzeigen.

## <a name="code-generation"></a>Codeerzeugung

### <a name="via-nswagstudio"></a>Über NSwagStudio

* Installieren Sie NSwagStudio über das offizielle [GitHub-Repository](https://github.com/RSuter/NSwag/wiki/NSwagStudio).
* Starten Sie NSwagStudio. Geben Sie die Datei-URL *swagger.json* in das Textfeld **Swagger Specification URL** (URL zur Spezifizierung des Swaggers) ein, und klicken Sie auf die Schaltfläche **Create local Copy** (Lokale Kopie erstellen).
* Klicken Sie auf den Clientausgabetyp **CSharp Client**. Zu den Optionen zählen außerdem der **TypeScript-Client** und der **CSharp-Web-API-Controller**. Bei der Verwendung eines Web-API-Controllers handelt es sich im Grunde um eine umgekehrte Generierung. Dieser verwendet die Spezifikation eines Diensts, um den Dienst erneut zu erstellen.
* Klicken Sie auf die Schaltfläche **Ausgaben generieren**. Dann wird eine vollständige Clientimplementierung des *TodoApi.NSwag*-Projekts in C# durchgeführt. Klicken Sie auf die Registerkarte **CSharp-Client** im Abschnitt **Ausgaben**, damit der generierte Clientcode angezeigt wird:

```csharp
//----------------------
// <auto-generated>
//     Generated using the NSwag toolchain v11.17.3.0 (NJsonSchema v9.10.46.0 (Newtonsoft.Json v9.0.0.0)) (http://NSwag.org)
// </auto-generated>
//----------------------

namespace MyNamespace
{
    #pragma warning disable // Disable all warnings

    [System.CodeDom.Compiler.GeneratedCode("NSwag",
        "11.17.3.0 (NJsonSchema v9.10.46.0 (Newtonsoft.Json v9.0.0.0))")]
    public partial class TodoClient
    {
        private string _baseUrl = "http://localhost:50499";
        private System.Lazy<Newtonsoft.Json.JsonSerializerSettings> _settings;

        public TodoClient()
        {
            _settings = new System.Lazy
                <Newtonsoft.Json.JsonSerializerSettings>(() =>
            {
                var settings = new Newtonsoft.Json.JsonSerializerSettings();
                UpdateJsonSerializerSettings(settings);
                return settings;
            });
        }

        public string BaseUrl
        {
            get { return _baseUrl; }
            set { _baseUrl = value; }
        }

        // code omitted for brevity
```

> [!TIP]
> Der C#-Clientcode wird basierend auf den Einstellungen generiert, die über die Registerkarte **Einstellungen** der Registerkarte **CSharp-Client** definiert werden. Ändern Sie die Einstellungen, um Aufgaben wie das Umbenennen von Standardnamespaces und die Generierung von synchronen Methoden auszuführen.

* Kopieren Sie den generierten C#_code in eine Datei in einem Clientprojekt (z.B. zu einer [Xamarin.Forms](/xamarin/xamarin-forms/)-App).
* Verwendung der Web-API:

```csharp
var todoClient = new TodoClient();

// Gets all to-dos from the API
var allTodos = await todoClient.GetAllAsync();

// Create a new TodoItem, and save it in the API
var createdTodo = await todoClient.CreateAsync(new TodoItem());

// Get a single to-do by ID
var foundTodo = await todoClient.GetByIdAsync(1);
```

> [!NOTE]
> Sie können eine Basis-URL und/oder einen HTTP-Client in den API-Client einfügen. Eine bewährte Methode besteht darin, [HttpClient](https://aspnetmonsters.com/2016/08/2016-08-27-httpclientwrong/) immer wieder zu verwenden.

### <a name="other-ways-to-generate-client-code"></a>Weitere Möglichkeiten zum Generieren von Clientcode

Sie können den Clientcode auf andere Arten generieren, die besser zu Ihrem Workflow passen:

* [MSBuild](https://www.nuget.org/packages/NSwag.MSBuild/)

* [Im Code](https://github.com/NSwag/NSwag/wiki/SwaggerToCSharpClientGenerator)

* [T4-Vorlagen](https://github.com/NSwag/NSwag/wiki/T4)

## <a name="customize"></a>Anpassen

Swagger umfasst Optionen zum Dokumentieren des Objektmodells, um die Verwendung der Web-API zu vereinfachen.

### <a name="api-info-and-description"></a>API-Informationen und -Beschreibung

In der `Startup.Configure`-Methode werden Informationen wie Autor, Lizenz und Beschreibung durch die Konfigurationsaktion hinzugefügt, die an die `UseSwagger`-Methode übergeben wird:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_UseSwagger)]

Auf der Swagger-Benutzeroberfläche werden die Versionsinformationen angezeigt:

![Swagger-Benutzeroberfläche mit Versionsinformationen](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a>XML-Kommentare

XML-Kommentare werden mithilfe von folgenden Ansätzen aktiviert:

# <a name="visual-studiotabvisual-studio-xml"></a>[Visual Studio](#tab/visual-studio-xml/)

::: moniker range=">= aspnetcore-2.0"

* Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **<project_name>.csproj bearbeiten** aus.
* Fügen Sie die hervorgehobenen Zeilen manuell der *CSPROJ*-Datei hinzu:

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Eigenschaften** aus.
* Überprüfen Sie das Feld **XML-Dokumentationsdatei** im Abschnitt **Ausgabe** der Registerkarte **Erstellen**.

::: moniker-end

# <a name="visual-studio-for-mactabvisual-studio-mac-xml"></a>[Visual Studio für Mac](#tab/visual-studio-mac-xml/)

::: moniker range=">= aspnetcore-2.0"

* Drücken und halten Sie im *Lösungspad* die **CONTROL**-Taste, und klicken Sie auf den Projektnamen. Navigieren Sie zu **Extras** > **Datei bearbeiten**.
* Fügen Sie die hervorgehobenen Zeilen manuell der *CSPROJ*-Datei hinzu:

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Öffnen Sie das Dialogfeld **Projektoptionen** > **Erstellen** > **Compiler**.
* Überprüfen Sie das Feld **XML-Dokumentation generieren** im Abschnitt **Allgemeine Optionen**.

::: moniker-end

# <a name="visual-studio-codetabvisual-studio-code-xml"></a>[Visual Studio Code](#tab/visual-studio-code-xml/)

Fügen Sie die hervorgehobenen Zeilen manuell der *CSPROJ*-Datei hinzu:

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

---

### <a name="data-annotations"></a>Datenanmerkungen

::: moniker range="<= aspnetcore-2.0"

NSwag verwendet die [Reflektion](/dotnet/csharp/programming-guide/concepts/reflection), und der für Web-API-Aktionen empfohlene Rückgabetyp lautet [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult). Folglich kann NSwag nicht ableiten, was die Aktion ausführt und was sie zurückgibt. Betrachten Sie das folgende Beispiel:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

Die vorherige Aktion gibt `IActionResult` zurück. Innerhalb der Aktion gibt sie jedoch [CreatedAtRoute](/dotnet/api/system.web.http.apicontroller.createdatroute) oder [BadRequest](/dotnet/api/system.web.http.apicontroller.badrequest) zurück. Datenanmerkungen werden verwendet, um Clients mitzuteilen, welche HTTP-Statuscodes diese Aktion in der Regel zurückgibt. Ergänzen Sie die Aktion mit folgenden Attributen:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

NSwag verwendet [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), und der empfohlene Rückgabetyp für Web-API-Aktionen ist [ActionResult\<T>](/dotnet/api/microsoft.aspnetcore.mvc.actionresult-1). D.h., NSwag kann nur den von `T` definierten Rückgabetypen ableiten. Es können keine weiteren Rückgabetypen in dieser Aktion abgeleitet werden. Betrachten Sie das folgende Beispiel:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

Die vorherige Aktion gibt `ActionResult<T>` zurück. In der Aktion gibt sie [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) zurück. Da der Controller mit dem [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)-Attribut ausgestattet ist, ist auch eine [BadRequest](xref:System.Web.Http.ApiController.BadRequest*)-Antwort möglich. Weitere Informationen finden Sie unter [Automatische HTTP 400-Antworten](xref:web-api/index#automatic-http-400-responses). Datenanmerkungen werden verwendet, um Clients mitzuteilen, welche HTTP-Statuscodes diese Aktion in der Regel zurückgibt. Ergänzen Sie die Aktion mit folgenden Attributen:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

In ASP.NET Core 2.2 oder höher können Konventionen als Alternative verwendet werden, um einzelne Aktionen explizit mit `[ProducesResponseType]` zu ergänzen. Weitere Informationen finden Sie unter <xref:web-api/advanced/conventions>.

::: moniker-end

Der Swagger-Generator kann diese Aktion nun genau beschreiben, und generierte Clients wissen, was sie beim Aufrufen des Endpunkts empfangen. Es wird empfohlen, alle Aktionen mit diesen Attributen zu ergänzen. Weitere Informationen zu den Richtlinien dazu, welche HTTP-Antworten Ihre API-Aktionen zurückgeben sollten, finden Sie in der [RFC 7231 specification (Spezifikation von RFC 7231)](https://tools.ietf.org/html/rfc7231#section-4.3).
