---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: Erfahren Sie, wie Razor Pages in ASP.NET Core codierungsseitige Szenarios einfacher und produktiver gestalten als MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 08/23/2019
no-loc:
- Blazor
uid: razor-pages/sdk
ms.openlocfilehash: 872d90662494735dc0e4caa01c46fcdcc2606bc6
ms.sourcegitcommit: b5ceb0a46d0254cc3425578116e2290142eec0f0
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2020
ms.locfileid: "76809132"
---
# <a name="aspnet-core-razor-sdk"></a>ASP.NET Core Razor SDK

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>Übersicht über

Die [!INCLUDE[](~/includes/2.1-SDK.md)] enthält die `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor-SDK). Das Razor SDK:

::: moniker range=">= aspnetcore-3.0"

* Ist erforderlich, um Projekte zu erstellen, zu verpacken und zu veröffentlichen, die [Razor](xref:mvc/views/razor) -Dateien für ASP.net Core MVC-basierte oder [blazor](xref:blazor/index) -Projekte enthalten.
* Enthält einen Satz vordefinierter Ziele, Eigenschaften und Elemente, die das Anpassen der Kompilierung von Razor-Dateien (*cshtml* -oder *Razor*-Dateien) ermöglichen.

Das Razor SDK umfasst `Content` Elemente, deren `Include` Attribute auf die `**\*.cshtml` und `**\*.razor` globmuster festgelegt sind. Übereinstimmende Dateien werden veröffentlicht.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Standardisiert die Vorgänge zum Erstellen, Verpacken und Veröffentlichen von Projekten, die [Razor](xref:mvc/views/razor)-Dateien für auf ASP.NET Core MVC basierende Projekte enthalten.
* Enthält eine vordefinierte Reihe von Zielen, Eigenschaften und Elementen, die das Anpassen der Razor-Dateien ermöglichen.

Das Razor SDK enthält ein `Content` Element mit einem `Include`-Attribut, das auf das `**\*.cshtml` globmuster festgelegt ist. Übereinstimmende Dateien werden veröffentlicht.

::: moniker-end

## <a name="prerequisites"></a>Erforderliche Komponenten

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a>Verwenden des Razor SDK

Die meisten Web-apps müssen nicht explizit das Razor-SDK zu verweisen.

::: moniker range=">= aspnetcore-3.0"

Um das Razor SDK zum Erstellen von Klassenbibliotheken mit Razor-Ansichten oder Razor Pages zu verwenden, wird empfohlen, mit der Projektvorlage für die Razor-Klassenbibliothek (RCL) zu beginnen. Eine RCL, die zum Erstellen von blazor-Dateien (*Razor*-Dateien) verwendet wird, erfordert mindestens einen Verweis auf das Paket " [Microsoft. aspnetcore. Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) ". Eine RCL, die zum Erstellen von Razor-Sichten oder-Seiten (*cshtml* -Dateien) verwendet wird, erfordert mindestens `netcoreapp3.0` oder höher und verfügt über eine `FrameworkReference` für das [Metapaket "Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app) " in der Projektdatei.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

So verwenden Sie das Razor SDK zum Erstellen von Klassenbibliotheken, die Razor-Ansichten oder Razor-Seiten enthalten:

* Verwenden Sie `Microsoft.NET.Sdk.Razor` anstelle von `Microsoft.NET.Sdk`:

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* In der Regel einen Paketverweis auf `Microsoft.AspNetCore.Mvc` ist erforderlich, um zusätzliche Abhängigkeiten zu erhalten, die zum Erstellen und Kompilieren von Razor Pages und Razor-Ansichten erforderlich sind. Zumindest sollten Ihr Projekt Paketverweise zum Hinzufügen:

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  Die `Microsoft.AspNetCore.Razor.Design` Paket stellt die Razor-Kompilierung-Aufgaben und-Ziele für das Projekt bereit.

  Diese Pakete sind in `Microsoft.AspNetCore.Mvc` enthalten. Das folgende Markup zeigt eine Projektdatei, die die Razor-SDK verwendet, um die Razor-Dateien für eine ASP.NET Core Razor Pages-app zu erstellen:

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> Die `Microsoft.AspNetCore.Razor.Design` und `Microsoft.AspNetCore.Mvc.Razor.Extensions` Pakete befinden sich der [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app). Allerdings das Version-kleiner `Microsoft.AspNetCore.App` Paketverweis bietet ein metapaket an die app, die die neueste Version von keine `Microsoft.AspNetCore.Razor.Design`. Projekte müssen eine einheitliche Version des verweisen `Microsoft.AspNetCore.Razor.Design` (oder `Microsoft.AspNetCore.Mvc`) so, dass die neueste Fixes der Buildzeit für Razor enthalten sind. Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/aspnet/Razor/issues/2553).

::: moniker-end

### <a name="properties"></a>Eigenschaften

Die folgenden Eigenschaften steuern das Verhalten des Razor SDK im Rahmen eines Projektbuilds:

* `RazorCompileOnBuild` &ndash; Wenn `true`, kompiliert, und gibt die Razor-Assembly im Rahmen der Erstellung des Projekts. Wird standardmäßig auf `true` festgelegt.
* `RazorCompileOnPublish` &ndash; Wenn `true`, kompiliert, und gibt die Razor-Assembly als Teil der Veröffentlichung des Projekts. Wird standardmäßig auf `true` festgelegt.

Die Eigenschaften und Elemente in der folgenden Tabelle werden zum Konfigurieren von ein- und Ausgabe an die Razor-SDK verwendet.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> Ab ASP.net Core 3,0 werden MVC-Sichten oder-Razor Pages standardmäßig nicht bereitgestellt, wenn die Eigenschaften `RazorCompileOnBuild` oder `RazorCompileOnPublish` MSBuild in der Projektdatei deaktiviert sind. Anwendungen müssen einen expliziten Verweis auf das Paket [Microsoft. aspnetcore. MVC. Razor. runtimecompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) hinzufügen, wenn die APP zur Verarbeitung von *cshtml* -Dateien auf der Lauf Zeit Kompilierung basiert.

::: moniker-end

| Elemente | Beschreibung |
| ----- | ----------- |
| `RazorGenerate` | Element Elemente (*cshtml* -Dateien), die Eingaben für die Codegenerierung sind. |
| `RazorComponent` | Element Elemente (*Razor* -Dateien), die Eingaben für die Codegenerierung von Razor-Komponenten sind. |
| `RazorCompile` | Element Elemente (*CS* -Dateien), die Eingaben für Razor-Kompilierungs Ziele sind. Verwenden Sie diesen `ItemGroup`, um zusätzliche Dateien anzugeben, die in die Razor-Assembly kompiliert werden sollen. |
| `RazorTargetAssemblyAttribute` | Item-Elemente, die für das Codieren von Generate-Attributen für die Razor-Assembly verwendet werden. Beispiel:  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | Elemente, die als eingebettete Ressourcen für die generierte Razor-Assembly hinzugefügt werden. |

| Die Eigenschaften- | Beschreibung |
| -------- | ----------- |
| `RazorTargetName` | Dateiname (ohne Erweiterung) der Assembly, die von Razor erstellt wurde. |
| `RazorOutputPath` | Das Razor-Ausgabeverzeichnis. |
| `RazorCompileToolset` | Wird verwendet, um das Toolset für die Erstellung der Razor-Assembly zu bestimmen. Gültige Werte sind `Implicit`, `RazorSDK` und `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Der Standardwert ist `true`. Wenn `true`, schließt die Dateien *Web. config*, *. JSON*und *. cshtml* als Inhalt in das Projekt ein. Wenn auf die verwiesen wird über `Microsoft.NET.Sdk.Web`, Dateien unter *"Wwwroot"* und Konfigurationsdateien sind ebenfalls enthalten. |
| `EnableDefaultRazorGenerateItems` | Enthält *CSHTML*-Dateien aus `Content`-Elementen in `RazorGenerate`-Elementen, wenn `true` festgelegt ist. |
| `GenerateRazorTargetAssemblyInfo` | Wenn `true`, generiert eine *cs* -Datei mit der Attribute durch `RazorAssemblyAttribute` und die Datei in der Kompilierungsausgabe. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Fügt einen Standardsatz von Assembly-Attributen zu `RazorAssemblyAttribute` hinzu, wenn `true` festgelegt ist. |
| `CopyRazorGenerateFilesToPublishDirectory` | Wenn `true`, Kopien `RazorGenerate` Elemente ( *.cshtml*) Dateien in das Veröffentlichungsverzeichnis. Razor-Dateien nicht in der Regel für eine veröffentlichte app erforderlich, wenn sie an der Kompilierung zur Buildzeit oder veröffentlichen-Laufzeit beteiligt. Wird standardmäßig auf `false` festgelegt. |
| `CopyRefAssembliesToPublishDirectory` | Kopiert Referenzassembly-Elemente in das Veröffentlichungsverzeichnis, wenn `true` festgelegt ist. In der Regel sind nicht referenzierten Assemblys für eine veröffentlichte app erforderlich, wenn Razor-Kompilierung zur Buildzeit oder veröffentlichen-Laufzeit auftritt. Legen Sie auf `true` Ihre veröffentlichte app-Runtime-Kompilierung benötigt. Legen Sie den Wert beispielsweise auf `true` bei die app geändert *.cshtml* Dateien zur Laufzeit oder eingebettete Ansichten verwendet. Wird standardmäßig auf `false` festgelegt. |
| `IncludeRazorContentInPack` | Wenn `true`, alle Razor-Inhaltselemente ( *.cshtml* Dateien) für die Aufnahme in das generierte NuGet-Paket gekennzeichnet sind. Wird standardmäßig auf `false` festgelegt. |
| `EmbedRazorGenerateSources` | Fügt RazorGenerate-Elemente (*CSHTML*-Dateien) als eingebettete Dateien in die generierte Razor-Assembly hinzu, wenn `true` festgelegt ist. Wird standardmäßig auf `false` festgelegt. |
| `UseRazorBuildServer` | Verwendet einen dauerhaften Buildserverprozess, um die Auslastung durch die Codegenerierung zu verlagern, wenn `true` festgelegt ist. Wird standardmäßig auf den Wert `UseSharedCompilation` festgelegt. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Wenn `true`, generiert das SDK zusätzliche Attribute, die von MVC zur Laufzeit verwendet werden, um die Ermittlung von Anwendungs Teilen auszuführen. |
| `DefaultWebContentItemExcludes` | Ein globmuster für Element Elemente, die aus der `Content` Element Gruppe in Projekten ausgeschlossen werden sollen, die auf das Web-oder Razor-SDK abzielen. |
| `ExcludeConfigFilesFromBuildOutput` | Wenn `true`, werden *config* -und *JSON* -Dateien nicht in das Buildausgabeverzeichnis kopiert. |
| `AddRazorSupportForMvc` | Wenn `true`, wird das Razor-SDK konfiguriert, um die Unterstützung für die MVC-Konfiguration hinzuzufügen, die beim Entwickeln von Anwendungen mit MVC-Ansichten oder Razor Pages erforderlich ist. Diese Eigenschaft ist implizit für .net Core 3,0-Projekte oder spätere Projekte festgelegt, die auf das Web SDK abzielen. |
| `RazorLangVersion` | Die Version der zu zielenden Razor-Sprache. |

Weitere Informationen zu Eigenschaften finden Sie unter [MSBuild-Eigenschaften](/visualstudio/msbuild/msbuild-properties).

### <a name="targets"></a>Ziele

Das Razor SDK definiert zwei Hauptziele:

* `RazorGenerate` &ndash; Generiert Code *cs* Dateien von `RazorGenerate` item-Elemente. Verwenden Sie die `RazorGenerateDependsOn`-Eigenschaft, um zusätzliche Ziele anzugeben, die vor oder nach diesem Ziel ausgeführt werden können.
* `RazorCompile` &ndash; Kompiliert generiert *cs* Dateien im zu einer Razor-Assembly. Verwenden Sie die `RazorCompileDependsOn`, um zusätzliche Ziele anzugeben, die vor oder nach diesem Ziel ausgeführt werden können.
* `RazorComponentGenerate` &ndash; Code generiert *CS* -Dateien für `RazorComponent` Element Elemente. Verwenden Sie die `RazorComponentGenerateDependsOn`-Eigenschaft, um zusätzliche Ziele anzugeben, die vor oder nach diesem Ziel ausgeführt werden können.

### <a name="runtime-compilation-of-razor-views"></a>Kompilierung von Razor-Ansichten zur Laufzeit

* Standardmäßig veröffentlicht das Razor SDK keine Verweisassemblys, die für das Durchführen der Laufzeitkompilierung erforderlich sind. Dadurch kommt es zu Kompilierungsfehlern, wenn das Anwendungsmodell von der Laufzeitkompilierung abhängt (z.B. wenn die App eingebettete Ansichten verwendet oder Ansichten nach dem Veröffentlichen der App verändert werden). Legen Sie `CopyRefAssembliesToPublishDirectory` auf `true` fest, um mit dem Veröffentlichen von Verweisassemblys fortzufahren.

* Für eine Web-app, stellen Sie sicher auf die Ihre app die `Microsoft.NET.Sdk.Web` SDK.

## <a name="razor-language-version"></a>Razor-Sprachversion

Wenn das `Microsoft.NET.Sdk.Web` SDK als Ziel verwendet wird, wird die Razor-Sprachversion von der Ziel Framework-Version der APP abgeleitet. Für Projekte, die auf das `Microsoft.NET.Sdk.Razor` SDK abzielen, oder in seltenen Fällen, in denen die APP eine andere Version der Razor-Sprache benötigt als der abherzustehende Wert, kann eine Version konfiguriert werden, indem die `<RazorLangVersion>`-Eigenschaft in der Projektdatei der APP festgelegt wird:

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

Die Sprachversion von Razor ist eng in die Version der Laufzeit integriert, für die Sie erstellt wurde. Das Ziel einer Sprachversion, die nicht für die Laufzeit konzipiert ist, wird nicht unterstützt und erzeugt wahrscheinlich Buildfehler.

## <a name="additional-resources"></a>Weitere Ressourcen

* [Erweiterungen des CSPROJ-Formats für .NET Core](/dotnet/core/tools/csproj)
* [Gemeinsame MSBuild-Projektelemente](/visualstudio/msbuild/common-msbuild-project-items)
