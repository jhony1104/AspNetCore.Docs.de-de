---
title: Statische Dateien in ASP.NET Core
author: rick-anderson
description: Hier erfahren Sie, wie statische Dateien bereitgestellt und gesichert werden und wie das Verhalten von Middleware beim Hosting statischer Dateien in einer ASP.NET Core-Web-App konfiguriert wird.
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: fundamentals/static-files
ms.openlocfilehash: 95a77defc7e98328e1f4e3615648b1d14485e51e
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78647713"
---
# <a name="static-files-in-aspnet-core"></a>Statische Dateien in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Scott Addie](https://twitter.com/Scott_Addie)

Bei statischen Dateien wie HTML, CSS, Images und JavaScript handelt es sich um Objekte, die Clients von einer ASP.NET Core-App direkt bereitgestellt werden. Damit diese Dateien bereitgestellt werden können, sind einige Konfigurationsschritte erforderlich.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="serve-static-files"></a>Bereitstellen statischer Dateien

Statische Dateien werden im [Webstammverzeichnis](xref:fundamentals/index#web-root) des Projekts gespeichert. Das Standardverzeichnis heißt *{Inhaltsstammverzeichnis}/wwwroot*, das jedoch mit der Methode [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) geändert werden kann. Weitere Informationen finden Sie unter [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) und [Webstammverzeichnis](xref:fundamentals/index#web-root).

Der Web-Host der App muss über das Inhaltsstammverzeichnis informiert werden.

::: moniker range=">= aspnetcore-2.0"

Die Methode `WebHost.CreateDefaultBuilder` legt das Inhaltsstammverzeichnis auf das aktuelle Verzeichnis fest:

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Legen Sie das Inhaltsstammverzeichnis auf das aktuelle Verzeichnis fest, indem Sie [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) innerhalb von `Program.Main` aufrufen:

[!code-csharp[](static-files/samples/1x/Program.cs?name=snippet_ProgramClass&highlight=7)]

::: moniker-end

Auf statische Dateien kann über einen Pfad relativ zum [Webstammverzeichnis](xref:fundamentals/index#web-root) zugegriffen werden. Die Projektvorlage der **Webanwendung** verfügt beispielsweise über mehrere Ordner innerhalb des Ordners *wwwroot*:

* **wwwroot**
  * **css**
  * **images**
  * **js**

Das URI-Format für den Zugriff auf eine Datei im Unterordner *Images* lautet *http://\<server_address>/images/\<image_file_name>* . Beispiel: *http://localhost:9189/images/banner3.svg*

::: moniker range=">= aspnetcore-2.1"

Wenn .NET Framework die Zielkomponente ist, fügen Sie das Paket [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) zum Projekt hinzu. Wenn .NET Core die Zielkomponente ist, ist dieses Paket im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Wenn .NET Framework die Zielkomponente ist, fügen Sie das Paket [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) zum Projekt hinzu. Wenn .NET Core die Zielkomponente ist, ist dieses Paket im Metapaket [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) enthalten.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Fügen Sie das Paket [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) zum Projekt hinzu.

::: moniker-end

Konfigurieren Sie die [Middleware](xref:fundamentals/middleware/index), über die Sie statische Dateien bereitstellen können.

### <a name="serve-files-inside-of-web-root"></a>Bereitstellen von Dateien innerhalb des Webstammverzeichnisses

Rufen Sie die Methode [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) in `Startup.Configure` auf:

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

Die parameterlose Überladung der Methode `UseStaticFiles` markiert die Dateien im [Webstammverzeichnis](xref:fundamentals/index#web-root) als bereitstellbar. Folgendes Markup verweist auf *wwwroot/images/banner1.svg*:

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

Im vorhergehenden Code verweist die Tilde (`~/`) auf das [Webstammverzeichnis](xref:fundamentals/index#web-root).

### <a name="serve-files-outside-of-web-root"></a>Bereitstellen von Dateien außerhalb des Webstammverzeichnisses

Erwägen Sie eine Verzeichnishierarchie, bei der sich die bereitzustellenden statischen Dateien außerhalb des [Webstammverzeichnisses](xref:fundamentals/index#web-root) befinden:

* **wwwroot**
  * **css**
  * **images**
  * **js**
* **MyStaticFiles**
  * **images**
    * *banner1.svg*

Über eine Anforderung kann auf die Datei *banner1.svg* zugegriffen werden, indem die Middleware für statische Dateien wie folgt konfiguriert wird:

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

Im vorangehenden Code wird die *MyStaticFiles*-Verzeichnishierarchie öffentlich über das URI-Segment *StaticFiles* verfügbar gemacht. Über eine Anforderung an *http://\<server_address>/StaticFiles/images/banner1.svg* wird die Datei *banner1.svg* bereitgestellt.

Folgendes Markup verweist auf *MyStaticFiles/images/banner1.svg*:

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a>Festlegen von HTTP-Antwortheadern

Mit einem [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions)-Objekt können HTTP-Antwortheader festgelegt werden. Neben der Konfiguration statischer, über das [Webstammverzeichnis](xref:fundamentals/index#web-root) bereitgestellter Dateien wird mit dem folgenden Code der Header `Cache-Control` festgelegt:

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Die Methode [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) ist im Paket [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) enthalten.

Die Dateien wurden 10 Minuten lang (600 Sekunden) in der Entwicklungsumgebung öffentlich zwischenspeicherbar gemacht:

![Antwortheader mit dem Cache-Control-Header wurden hinzugefügt](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a>Autorisierung statischer Dateien

Die Middleware für statische Dateien bietet keine Autorisierungsüberprüfungen. Alle über die Middleware bereitgestellten Dateien, Dateien unter *wwwroot* inbegriffen, sind öffentlich zugänglich. So stellen Sie Dateien basierend auf der Autorisierung bereit:

* Speichern Sie sie außerhalb von *wwwroot* sowie außerhalb sämtlicher Verzeichnisse, auf die die Middleware für statische Dateien zugreifen kann.
* Stellen Sie sie über eine Aktionsmethode bereit, für die die Autorisierung gilt. Geben Sie ein [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult)-Objekt zurück:

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a>Aktivieren der Verzeichnissuche

Über die Verzeichnissuche können Benutzer Ihrer Web-App eine Verzeichnisliste und Dateien innerhalb eines angegebenen Verzeichnisses anzeigen. Die Verzeichnissuche ist aus Sicherheitsgründen standardmäßig deaktiviert (siehe [Überlegungen](#considerations)). Aktivieren Sie die Verzeichnissuche, indem Sie die Methode [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) in `Startup.Configure` aufrufen:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

Fügen Sie erforderliche Dienste hinzu, indem Sie die Methode [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) von `Startup.ConfigureServices` aufrufen:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

Der vorangehende Code ermöglicht die Verzeichnissuche im Ordner *wwwroot/images* über die URL *http://\<server_address>/MyImages* mit Links zu den einzelnen Dateien und Ordnern:

![Verzeichnissuche](static-files/_static/dir-browse.png)

Weitere Informationen zu den Sicherheitsrisiken bei der Aktivierung der Suche finden Sie unter [Überlegungen](#considerations).

Beachten Sie die beiden `UseStaticFiles`-Aufrufe im folgenden Beispiel. Durch den ersten Aufruf wird die Bereitstellung statischer Dateien im Ordner *wwwroot* aktiviert. Durch den zweiten Aufruf wird die Verzeichnissuche im Ordner *wwwroot/images* über die URL *http://\<server_address>/MyImages* aktiviert:

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a>Bereitstellen eines Standarddokuments

Durch das Festlegen einer Standardstartseite wird Besuchern Ihrer Website ein logischer Ausgangspunkt geboten. Rufen Sie die Methode [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) über `Startup.Configure` auf, um eine Standardseite bereitzustellen, ohne dass der Benutzer den URI vollständig qualifizieren muss:

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> `UseDefaultFiles` muss vor `UseStaticFiles` aufgerufen werden, damit die Standarddatei bereitgestellt wird. `UseDefaultFiles` ist ein URL-Rewriter, der die Datei nicht verarbeiten kann. Aktivieren Sie die Middleware für statische Dateien über `UseStaticFiles`, um die Datei bereitzustellen.

Bei `UseDefaultFiles` werden bei Anforderungen an einen Ordner folgende Dateien durchsucht:

* *default.htm*
* *default.html*
* *index.htm*
* *index.html*

Die erste in der Liste gefundene Datei wird bereitgestellt, als wäre die Anforderung der vollständig qualifizierte URI. Die Browser-URL spiegelt weiterhin den angeforderten URI wider.

Mit dem folgenden Code wird der Standarddateiname in *mydefault.html* geändert:

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a>UseFileServer

In <xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> werden die Funktionen von `UseStaticFiles`, `UseDefaultFiles` und (optional) `UseDirectoryBrowser` miteinander kombiniert.

Mit dem folgenden Code wird die Bereitstellung statischer Dateien und der Standarddatei aktiviert. Die Verzeichnissuche ist nicht aktiviert.

```csharp
app.UseFileServer();
```

Der folgende Code baut auf der parameterlosen Überladung auf, indem die Verzeichnissuche aktiviert wird:

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

Betrachten Sie die folgende Verzeichnishierarchie:

* **wwwroot**
  * **css**
  * **images**
  * **js**
* **MyStaticFiles**
  * **images**
    * *banner1.svg*
  * *default.html*

Mit dem folgenden Code werden statische Dateien, Standarddateien und die Verzeichnissuche von `MyStaticFiles` aktiviert:

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

`AddDirectoryBrowser` muss aufgerufen werden, wenn der `EnableDirectoryBrowsing`-Eigenschaftswert `true` lautet:

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

Unter Verwendung der Dateihierarchie und des vorangehenden Codes werden URLs wie folgt aufgelöst:

| URI            |                             Antwort  |
| ------- | ------|
| *http://\<server_address>/StaticFiles/images/banner1.svg*    |      MyStaticFiles/images/banner1.svg |
| *http://\<server_address>/StaticFiles*             |     MyStaticFiles/default.html |

Wenn im Verzeichnis *MyStaticFiles* keine Datei mit Standardnamen vorhanden ist, gibt *http://\<server_address>/StaticFiles* die Verzeichnisliste mit klickbaren Links zurück:

![Liste der statischen Dateien](static-files/_static/db2.png)

> [!NOTE]
> <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> und <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> führen eine clientseitige Umleitung von `http://{SERVER ADDRESS}/StaticFiles` (ohne nachstehenden Schrägstrich) zu `http://{SERVER ADDRESS}/StaticFiles/` (mit nachstehendem Schrägstrich) durch. Relative URLs im Verzeichnis *StaticFiles* gelten ohne nachstehenden Schrägstrich als ungültig.

## <a name="fileextensioncontenttypeprovider"></a>FileExtensionContentTypeProvider

Die Klasse [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) enthält eine `Mappings`-Eigenschaft, die als Zuordnung von Dateierweiterungen zu MIME-Inhaltstypen dient. Im folgenden Beispiel werden mehrere Dateierweiterungen bei bekannten MIME-Typen registriert. Die Erweiterung *.rtf* wird ersetzt, und *.mp4* wird entfernt.

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

Weitere Informationen finden Sie unter [MIME-Inhaltstypen](https://www.iana.org/assignments/media-types/media-types.xhtml).

## <a name="non-standard-content-types"></a>Inhaltstypen, die vom Standard abweichen

Die Middleware für statische Dateien erkennt fast 400 bekannte Dateiinhaltstypen. Wenn ein Benutzer eine Datei mit einem unbekannten Dateityp anfordert, übergibt die Middleware für statische Dateien die Anforderung an die nächste Middleware in der Pipeline. Wenn keine Middleware die Anforderung verarbeitet, wird die Meldung *404 Nicht gefunden* zurückgegeben. Wenn die Verzeichnissuche aktiviert ist, wird ein Link zur Datei in einer Verzeichnisliste angezeigt.

Mit dem folgenden Code wird die Bereitstellung unbekannter Typen aktiviert und die unbekannte Datei als Image gerendert:

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

Mit dem vorangehenden Code wird eine Anforderung für eine Datei mit unbekanntem Inhaltstyp als Image zurückgegeben.

> [!WARNING]
> Die Aktivierung von [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) stellt ein Sicherheitsrisiko dar. Diese Eigenschaft ist standardmäßig deaktiviert, von einer Verwendung wird abgeraten. [FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) bietet eine sicherere Alternative für die Bereitstellung von Dateien mit vom Standard abweichenden Erweiterungen.

## <a name="serve-files-from-multiple-locations"></a>Bereitstellen von Dateien aus mehreren Speicherorten

Bei `UseStaticFiles` und `UseFileServer` zeigt der Dateianbieter standardmäßig auf *wwwroot*. Sie können weitere Instanzen von `UseStaticFiles` und `UseFileServer` mit anderen Dateianbietern bereitstellen, um Dateien von anderen Speicherorten bereitzustellen. Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).

### <a name="considerations"></a>Weitere Überlegungen

> [!WARNING]
> `UseDirectoryBrowser` und `UseStaticFiles` können Geheimnisse aufdecken. Eine Deaktivierung der Verzeichnissuche in der Produktionsumgebung wird dringend empfohlen. Überprüfen Sie sorgfältig, welche Verzeichnisse über `UseStaticFiles` oder `UseDirectoryBrowser` aktiviert wurden. Das gesamte Verzeichnis und die zugehörigen Unterverzeichnisse werden öffentlich zugänglich gemacht. Speichern Sie Dateien, die für eine Bereitstellung in der Öffentlichkeit geeignet sind, in einem dafür vorgesehenen Verzeichnis wie z.B. *\<content_root>/wwwroot*. Trennen Sie diese Dateien von MVC-Ansichten, Razor Pages (nur 2.x), Konfigurationsdateien usw.

* Die URLs für Inhalte, die mit `UseDirectoryBrowser` und `UseStaticFiles` verfügbar gemacht wurden, unterliegen der Groß-/Kleinschreibung und den Zeichenbeschränkungen des zugrunde liegenden Dateisystems. Bei Windows wird die Groß-/Kleinschreibung beispielsweise beachtet, bei macOS und Linux hingegen nicht.

* In IIS gehostete ASP.NET Core-Apps leiten über das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) alle Anforderungen an die App weiter, darunter die Anforderungen von statischen Dateien. Der statische IIS-Dateihandler wird nicht verwendet. Er kann Anforderungen erst verarbeiten, nachdem sie vom Modul verarbeitet wurden.

* Führen Sie im IIS-Manager die folgenden Schritte aus, um den statischen IIS-Dateihandler auf Server- oder Website-Ebene zu entfernen:
    1. Navigieren Sie zum Feature **Module**.
    1. Wählen Sie **StaticFileModule** aus der Liste aus.
    1. Klicken Sie in der Randleiste **Aktionen** auf **Entfernen**.

> [!WARNING]
> Wenn der statische IIS-Dateihandler aktiviert ist **und** das ASP.NET Core-Modul falsch konfiguriert wurde, werden statische Dateien bereitgestellt. Dies geschieht beispielsweise, wenn die Datei *web.config* nicht bereitgestellt worden ist.

* Platzieren Sie Codedateien (einschließlich *.cs* und *.cshtml*) außerhalb des [Webstammverzeichnisses](xref:fundamentals/index#web-root) des App-Projekts. Aus diesem Grund wird eine logische Trennung zwischen den clientseitigen Inhalten der App und dem serverbasierten Code erschaffen. Dadurch wird verhindert, dass serverseitiger Code aufgedeckt wird.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Middleware](xref:fundamentals/middleware/index)
* [Einführung in ASP.NET Core](xref:index)
