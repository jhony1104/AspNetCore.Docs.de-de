---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: Erfahren Sie, wie Razor Pages in ASP.NET Core codierungsseitige Szenarios einfacher und produktiver gestalten als MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/18/2019
uid: razor-pages/sdk
ms.openlocfilehash: 1dc001c7c5fe320629835e06fe6db7fadabff94d
ms.sourcegitcommit: 6189b0ced9c115248c6ede02efcd0b29d31f2115
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2019
ms.locfileid: "69985401"
---
# <a name="aspnet-core-razor-sdk"></a>ASP.NET Core Razor SDK

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>Übersicht

Die [!INCLUDE[](~/includes/2.1-SDK.md)] enthält die `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor-SDK). Das Razor SDK:

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"
* Standardisiert die Vorgänge zum Erstellen, Verpacken und Veröffentlichen von Projekten, die [Razor](xref:mvc/views/razor)-Dateien für auf ASP.NET Core MVC basierende Projekte enthalten.
* Enthält eine vordefinierte Reihe von Zielen, Eigenschaften und Elementen, die das Anpassen der Razor-Dateien ermöglichen.
::: moniker-end

::: moniker range=">= aspnetcore-3.0"
* ist erforderlich, um Projekte zu erstellen, zu verpacken und zu veröffentlichen, die [Razor](xref:mvc/views/razor) -Dateien für ASP.net Core MVC-basierte Projekte oder [blazor](xref:blazor/index) -Projekte enthalten.
* Enthält einen Satz vordefinierter Ziele, Eigenschaften und Elemente, die das Anpassen der Kompilierung von Razor-Dateien (cshtml-oder Razor-Dateien) ermöglichen.
::: moniker-end


::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Das Razor SDK enthält ein `<Content>` -Element mit `Include` einem-Attribut, `**\*.cshtml` das auf das globmuster festgelegt ist. Übereinstimmende Dateien werden veröffentlicht.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Das Razor SDK umfasst `<Content>` Elemente mit `Include` Attributen, die auf `**\*.cshtml` die `**\*.razor` globmuster und festgelegt sind. Übereinstimmende Dateien werden veröffentlicht.

::: moniker-end

## <a name="prerequisites"></a>Erforderliche Komponenten

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a>Verwenden des Razor SDK

Die meisten Web-apps müssen nicht explizit das Razor-SDK zu verweisen.

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"
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

::: moniker range=">= aspnetcore-3.0"
Um das Razor SDK zum Erstellen von Klassenbibliotheken mit Razor-Ansichten oder Razor Pages zu verwenden, wird empfohlen, mit der Projektvorlage für die Razor-Klassenbibliothek zu beginnen. Eine Razor-Klassenbibliothek, die zum Erstellen von blazor-Dateien (Razor-Dateien) verwendet wird, erfordert mindestens `Microsoft.AspNetCore.Components` einen Verweis auf das Paket. Eine Razor-Klassenbibliothek, die verwendet wird, um Razor-Sichten oder-Seiten (cshtml-Dateien) `netcoreapp3.0` zu erstellen, erfordert die `FrameworkReference` Ziel `Microsoft.AspNetCore.App`Plattform oder höher und eine auf.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> Die `Microsoft.AspNetCore.Razor.Design` und `Microsoft.AspNetCore.Mvc.Razor.Extensions` Pakete befinden sich der [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app). Allerdings das Version-kleiner `Microsoft.AspNetCore.App` Paketverweis bietet ein metapaket an die app, die die neueste Version von keine `Microsoft.AspNetCore.Razor.Design`. Projekte müssen eine einheitliche Version des verweisen `Microsoft.AspNetCore.Razor.Design` (oder `Microsoft.AspNetCore.Mvc`) so, dass die neueste Fixes der Buildzeit für Razor enthalten sind. Weitere Informationen finden Sie unter [GitHub-Problem](https://github.com/aspnet/Razor/issues/2553).

::: moniker-end

### <a name="properties"></a>Eigenschaften

Die folgenden Eigenschaften steuern das Verhalten des Razor SDK im Rahmen eines Projektbuilds:

* `RazorCompileOnBuild` &ndash; Wenn `true`, kompiliert, und gibt die Razor-Assembly im Rahmen der Erstellung des Projekts. Wird standardmäßig auf `true` festgelegt.
* `RazorCompileOnPublish` &ndash; Wenn `true`, kompiliert, und gibt die Razor-Assembly als Teil der Veröffentlichung des Projekts. Wird standardmäßig auf `true` festgelegt.

Die Eigenschaften und Elemente in der folgenden Tabelle werden zum Konfigurieren von ein- und Ausgabe an die Razor-SDK verwendet.

::: moniker range=">= aspnetcore-3.0"
> [!WARNING]
Ab ASP.net Core 3,0 werden MVC-Sichten oder-Razor Pages nicht standardmäßig bedient, wenn `RazorCompileOnBuild` oder `RazorCompileOnPublish` deaktiviert ist. Anwendungen müssen einen expliziten Verweis auf `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` das-Paket hinzufügen, um die Unterstützung für die Lauf Zeit Kompilierung hinzuzufügen, wenn Sie zur Verarbeitung von cshtml-Dateien auf
::: moniker-end


| Elemente | Beschreibung |
| ----- | ----------- |
| `RazorGenerate` | Element Elemente (*cshtml* -Dateien), die Eingaben für die Codegenerierung sind. |
| `RazorComponent` | Element Elemente (*Razor* -Dateien), die Eingaben für die Generierung von Komponenten Code sind.
| `RazorCompile` | Element Elemente (*CS* -Dateien), die Eingaben für Razor-Kompilierungs Ziele sind. Verwenden Sie `ItemGroup` diese Angabe, um zusätzliche Dateien anzugeben, die in die Razor-Assembly kompiliert werden sollen. |
| `RazorTargetAssemblyAttribute` | Item-Elemente, die für das Codieren von Generate-Attributen für die Razor-Assembly verwendet werden. Zum Beispiel:  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | Elemente, die als eingebettete Ressourcen für die generierte Razor-Assembly hinzugefügt werden. |

| Eigenschaft | Beschreibung |
| -------- | ----------- |
| `RazorTargetName` | Dateiname (ohne Erweiterung) der Assembly, die von Razor erstellt wurde. | 
| `RazorOutputPath` | Das Razor-Ausgabeverzeichnis. |
| `RazorCompileToolset` | Wird verwendet, um das Toolset für die Erstellung der Razor-Assembly zu bestimmen. Gültige Werte sind `Implicit`, `RazorSDK` und `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Der Standardwert ist `true`. Wenn `true`, werden die Dateien *Web. config*, *. JSON*und *. cshtml* als Inhalt in das Projekt eingeschlossen. Wenn auf die verwiesen wird über `Microsoft.NET.Sdk.Web`, Dateien unter *"Wwwroot"* und Konfigurationsdateien sind ebenfalls enthalten. |
| `EnableDefaultRazorGenerateItems` | Enthält *CSHTML*-Dateien aus `Content`-Elementen in `RazorGenerate`-Elementen, wenn `true` festgelegt ist. |
| `GenerateRazorTargetAssemblyInfo` | Wenn `true`, generiert eine *cs* -Datei mit der Attribute durch `RazorAssemblyAttribute` und die Datei in der Kompilierungsausgabe. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Fügt einen Standardsatz von Assembly-Attributen zu `RazorAssemblyAttribute` hinzu, wenn `true` festgelegt ist. |
| `CopyRazorGenerateFilesToPublishDirectory` | Wenn `true`, Kopien `RazorGenerate` Elemente ( *.cshtml*) Dateien in das Veröffentlichungsverzeichnis. Razor-Dateien nicht in der Regel für eine veröffentlichte app erforderlich, wenn sie an der Kompilierung zur Buildzeit oder veröffentlichen-Laufzeit beteiligt. Wird standardmäßig auf `false` festgelegt. |
| `CopyRefAssembliesToPublishDirectory` | Kopiert Referenzassembly-Elemente in das Veröffentlichungsverzeichnis, wenn `true` festgelegt ist. In der Regel sind nicht referenzierten Assemblys für eine veröffentlichte app erforderlich, wenn Razor-Kompilierung zur Buildzeit oder veröffentlichen-Laufzeit auftritt. Legen Sie auf `true` Ihre veröffentlichte app-Runtime-Kompilierung benötigt. Legen Sie den Wert beispielsweise auf `true` bei die app geändert *.cshtml* Dateien zur Laufzeit oder eingebettete Ansichten verwendet. Wird standardmäßig auf `false` festgelegt. |
| `IncludeRazorContentInPack` | Wenn `true`, alle Razor-Inhaltselemente ( *.cshtml* Dateien) für die Aufnahme in das generierte NuGet-Paket gekennzeichnet sind. Wird standardmäßig auf `false` festgelegt. |
| `EmbedRazorGenerateSources` | Fügt RazorGenerate-Elemente (*CSHTML*-Dateien) als eingebettete Dateien in die generierte Razor-Assembly hinzu, wenn `true` festgelegt ist. Wird standardmäßig auf `false` festgelegt. |
| `UseRazorBuildServer` | Verwendet einen dauerhaften Buildserverprozess, um die Auslastung durch die Codegenerierung zu verlagern, wenn `true` festgelegt ist. Wird standardmäßig auf den Wert `UseSharedCompilation` festgelegt. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Wenn `true`, generiert das SDK zusätzliche Attribute, die von MVC zur Laufzeit verwendet werden, um die Ermittlung von Anwendungs Teilen auszuführen. |

Weitere Informationen zu Eigenschaften finden Sie unter [MSBuild-Eigenschaften](/visualstudio/msbuild/msbuild-properties).

### <a name="targets"></a>Ziele

Das Razor SDK definiert zwei Hauptziele:

* `RazorGenerate` &ndash; Generiert Code *cs* Dateien von `RazorGenerate` item-Elemente. Verwenden Sie die Eigenschaft `RazorGenerateDependsOn`, um zusätzliche Ziele anzugeben, die vor oder nach diesem Ziel ausgeführt werden können.
* `RazorCompile` &ndash; Kompiliert generiert *cs* Dateien im zu einer Razor-Assembly. Verwenden Sie `RazorCompileDependsOn`, um zusätzliche Ziele anzugeben, die vor oder nach diesem Ziel ausgeführt werden können.
* `RazorComponentGenerate`Code generiert *CS* -Dateien für `RazorComponent` Element Elemente. &ndash; Verwenden Sie die Eigenschaft `RazorComponentGenerateDependsOn`, um zusätzliche Ziele anzugeben, die vor oder nach diesem Ziel ausgeführt werden können.

### <a name="runtime-compilation-of-razor-views"></a>Kompilierung von Razor-Ansichten zur Laufzeit

* Standardmäßig veröffentlicht das Razor SDK keine Verweisassemblys, die für das Durchführen der Laufzeitkompilierung erforderlich sind. Dadurch kommt es zu Kompilierungsfehlern, wenn das Anwendungsmodell von der Laufzeitkompilierung abhängt (z.B. wenn die App eingebettete Ansichten verwendet oder Ansichten nach dem Veröffentlichen der App verändert werden). Legen Sie `CopyRefAssembliesToPublishDirectory` auf `true` fest, um mit dem Veröffentlichen von Verweisassemblys fortzufahren.

* Für eine Web-app, stellen Sie sicher auf die Ihre app die `Microsoft.NET.Sdk.Web` SDK.

## <a name="razor-language-version"></a>Razor-Sprachversion

Wenn das SDK als Zielversion verwendet wird, wird die Razor-Sprachversion von der Ziel Framework-Version der APP abgeleitet. `Microsoft.NET.Sdk.Web` Für Projekte, die `Microsoft.NET.Sdk.Razor` auf das SDK abzielen, oder in seltenen Fällen, in denen die APP eine andere Version der Razor-Sprache erfordert als der abherzustehende Wert `<RazorLangVersion>` , kann eine Version konfiguriert werden, indem die-Eigenschaft in der Projektdatei der APP festgelegt wird:

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

Die Sprachversion von Razor ist eng in die Version der Laufzeit integriert, für die Sie erstellt wurde. Das Ziel einer Sprachversion, die nicht für die Laufzeit konzipiert ist, wird nicht unterstützt und erzeugt wahrscheinlich Buildfehler.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Erweiterungen des CSPROJ-Formats für .NET Core](/dotnet/core/tools/csproj)
* [Gemeinsame MSBuild-Projektelemente](/visualstudio/msbuild/common-msbuild-project-items)
