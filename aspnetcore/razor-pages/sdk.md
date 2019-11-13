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
ms.openlocfilehash: 2fbdf95d02d7918236981c7fee8ebcbedf5c55e1
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963265"
---
# <a name="aspnet-core-razor-sdk"></a>ASP.NET Core Razor SDK

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>Übersicht

Die [!INCLUDE[](~/includes/2.1-SDK.md)] umfasst das `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK). Das Razor SDK:

::: moniker range=">= aspnetcore-3.0"

* Ist erforderlich, um Projekte zu erstellen, zu verpacken und zu veröffentlichen, die [Razor](xref:mvc/views/razor) -Dateien für ASP.net Core MVC-basierte-oder [Blazor](xref:blazor/index) -Projekte enthalten.
* Enthält einen Satz vordefinierter Ziele, Eigenschaften und Elemente, die das Anpassen der Kompilierung von Razor-Dateien (*cshtml* -oder *Razor*-Dateien) ermöglichen.

Das Razor SDK umfasst `Content`-Elemente, für die `Include`-Attribute auf die globmuster `**\*.cshtml` und `**\*.razor` festgelegt sind. Übereinstimmende Dateien werden veröffentlicht.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Standardisiert die Vorgänge zum Erstellen, Verpacken und Veröffentlichen von Projekten, die [Razor](xref:mvc/views/razor)-Dateien für auf ASP.NET Core MVC basierende Projekte enthalten.
* Enthält eine vordefinierte Reihe von Zielen, Eigenschaften und Elementen, die das Anpassen der Razor-Dateien ermöglichen.

Das Razor SDK enthält ein `Content`-Element mit einem `Include`-Attribut, das auf das `**\*.cshtml`-globmuster festgelegt ist. Übereinstimmende Dateien werden veröffentlicht.

::: moniker-end

## <a name="prerequisites"></a>Erforderliche Voraussetzungen

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a>Verwenden des Razor SDK

Die meisten Web-Apps müssen nicht explizit auf das Razor-SDK verweisen.

::: moniker range=">= aspnetcore-3.0"

Um das Razor SDK zum Erstellen von Klassenbibliotheken mit Razor-Ansichten oder Razor Pages zu verwenden, wird empfohlen, mit der Projektvorlage für die Razor-Klassenbibliothek (RCL) zu beginnen. Eine RCL, die verwendet wird, um Blazor ( *. Razor*)-Dateien zu erstellen, erfordert mindestens einen Verweis auf das Paket [Microsoft. aspnetcore. Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) . Eine RCL, die zum Erstellen von Razor-Sichten oder-Seiten (*cshtml* -Dateien) verwendet wird, erfordert mindestens `netcoreapp3.0` oder höher und verfügt über eine `FrameworkReference` bis zum [Microsoft. aspnetcore. app-Metapaket](xref:fundamentals/metapackage-app) in der Projektdatei.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

So verwenden Sie das Razor SDK zum Erstellen von Klassenbibliotheken, die Razor-Ansichten oder Razor-Seiten enthalten:

* Verwenden Sie `Microsoft.NET.Sdk.Razor` anstelle von `Microsoft.NET.Sdk`:

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* In der Regel ist ein Paket Verweis auf `Microsoft.AspNetCore.Mvc` erforderlich, um zusätzliche Abhängigkeiten zu erhalten, die zum Erstellen und Kompilieren von Razor Pages-und Razor-Ansichten erforderlich sind. Das Projekt sollte mindestens Paket Verweise hinzufügen:

  * `Microsoft.AspNetCore.Razor.Design` 
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`
    
  Das `Microsoft.AspNetCore.Razor.Design`-Paket stellt die Razor-Kompilierungs Aufgaben und-Ziele für das Projekt bereit.

  Diese Pakete sind in `Microsoft.AspNetCore.Mvc` enthalten. Das folgende Markup zeigt eine Projektdatei, die das Razor-SDK verwendet, um Razor-Dateien für eine ASP.net Core Razor Pages-APP zu erstellen:
    
  [!code-xml[](sdk/sample/RazorSDK.csproj)]
  
::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> Die Pakete "`Microsoft.AspNetCore.Razor.Design`" und "`Microsoft.AspNetCore.Mvc.Razor.Extensions`" sind im [Metapaket "Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)" enthalten. Der Versions lose `Microsoft.AspNetCore.App`-Paket Verweis stellt jedoch ein Metapaket für die APP bereit, das nicht die neueste Version von `Microsoft.AspNetCore.Razor.Design` enthält. Projekte müssen auf eine konsistente Version von `Microsoft.AspNetCore.Razor.Design` (oder `Microsoft.AspNetCore.Mvc`) verweisen, damit die neuesten Build-Time-Korrekturen für Razor eingefügt werden. Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/aspnet/Razor/issues/2553).

::: moniker-end

### <a name="properties"></a>Eigenschaften

Die folgenden Eigenschaften steuern das Verhalten des Razor SDK im Rahmen eines Projektbuilds:

* `RazorCompileOnBuild` &ndash;, wenn `true` ist, wird die Razor-Assembly im Rahmen der Projekt Erstellung kompiliert und ausgegeben. Wird standardmäßig auf `true` festgelegt.
* `RazorCompileOnPublish` &ndash;, wenn `true` ist, kompiliert und gibt die Razor-Assembly als Teil der Veröffentlichung des Projekts aus. Wird standardmäßig auf `true` festgelegt.

Die Eigenschaften und Elemente in der folgenden Tabelle werden verwendet, um Eingaben und Ausgaben für das Razor SDK zu konfigurieren.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> Ab ASP.net Core 3,0 werden MVC-Sichten oder-Razor Pages standardmäßig nicht bereitgestellt, wenn die MSBuild-Eigenschaften `RazorCompileOnBuild` oder `RazorCompileOnPublish` in der Projektdatei deaktiviert sind. Anwendungen müssen einen expliziten Verweis auf das Paket [Microsoft. aspnetcore. MVC. Razor. runtimecompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) hinzufügen, wenn die APP zur Verarbeitung von *cshtml* -Dateien auf der Lauf Zeit Kompilierung basiert.

::: moniker-end

| Elemente | Beschreibung |
| ----- | ----------- |
| `RazorGenerate` | Element Elemente (*cshtml* -Dateien), die Eingaben für die Codegenerierung sind. |
| `RazorComponent` | Element Elemente (*Razor* -Dateien), die Eingaben für die Codegenerierung von Razor-Komponenten sind. |
| `RazorCompile` | Element Elemente (*CS* -Dateien), die Eingaben für Razor-Kompilierungs Ziele sind. Verwenden Sie diesen `ItemGroup`, um zusätzliche Dateien anzugeben, die in die Razor-Assembly kompiliert werden sollen. |
| `RazorTargetAssemblyAttribute` | Item-Elemente, die für das Codieren von Generate-Attributen für die Razor-Assembly verwendet werden. Beispiel:  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | Element Elemente, die der generierten Razor-Assembly als eingebettete Ressourcen hinzugefügt wurden. |

| property | Beschreibung |
| -------- | ----------- |
| `RazorTargetName` | Dateiname (ohne Erweiterung) der Assembly, die von Razor erstellt wurde. | 
| `RazorOutputPath` | Das Razor-Ausgabeverzeichnis. |
| `RazorCompileToolset` | Wird verwendet, um das Toolset für die Erstellung der Razor-Assembly zu bestimmen. Gültige Werte sind `Implicit`, `RazorSDK` und `PrecompilationTool`. |
| [Enabledefaultcontentitems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Der Standardwert ist `true`. Wenn `true` ist, enthält die Dateien *Web. config*, *. JSON*und *. cshtml* als Inhalt in das Projekt. Wenn Sie über `Microsoft.NET.Sdk.Web` referenziert werden, sind auch Dateien unter den Dateien " *wwwroot* " und "config" enthalten. |
| `EnableDefaultRazorGenerateItems` | Enthält *CSHTML*-Dateien aus `Content`-Elementen in `RazorGenerate`-Elementen, wenn `true` festgelegt ist. |
| `GenerateRazorTargetAssemblyInfo` | Wenn `true`, generiert eine *CS* -Datei mit Attributen, die von `RazorAssemblyAttribute` angegeben werden, und schließt die Datei in die Kompilierungs Ausgabe ein. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Fügt einen Standardsatz von Assembly-Attributen zu `RazorAssemblyAttribute` hinzu, wenn `true` festgelegt ist. |
| `CopyRazorGenerateFilesToPublishDirectory` | Wenn `true`, werden `RazorGenerate` Items-Dateien ( *. cshtml*) in das Veröffentlichungs Verzeichnis kopiert. In der Regel sind Razor-Dateien für eine veröffentlichte APP nicht erforderlich, wenn Sie zur Buildzeit oder zum Zeitpunkt der Veröffentlichung an der Kompilierung teilnehmen. Wird standardmäßig auf `false` festgelegt. |
| `CopyRefAssembliesToPublishDirectory` | Kopiert Referenzassembly-Elemente in das Veröffentlichungsverzeichnis, wenn `true` festgelegt ist. In der Regel sind Verweisassemblys für eine veröffentlichte APP nicht erforderlich, wenn die Razor-Kompilierung zum Zeitpunkt der Erstellung oder zur Veröffentlichung erfolgt. Legen Sie den Wert auf `true` fest, wenn die veröffentlichte App eine Lauf Zeit Kompilierung Legen Sie beispielsweise den Wert `true` fest, wenn die APP *cshtml* -Dateien zur Laufzeit ändert oder eingebettete Sichten verwendet. Wird standardmäßig auf `false` festgelegt. |
| `IncludeRazorContentInPack` | Wenn `true`, werden alle Razor-Inhaltselemente (*cshtml* -Dateien) für die Einbindung in das generierte nuget-Paket markiert. Wird standardmäßig auf `false` festgelegt. |
| `EmbedRazorGenerateSources` | Fügt RazorGenerate-Elemente (*CSHTML*-Dateien) als eingebettete Dateien in die generierte Razor-Assembly hinzu, wenn `true` festgelegt ist. Wird standardmäßig auf `false` festgelegt. |
| `UseRazorBuildServer` | Verwendet einen dauerhaften Buildserverprozess, um die Auslastung durch die Codegenerierung zu verlagern, wenn `true` festgelegt ist. Wird standardmäßig auf den Wert `UseSharedCompilation` festgelegt. |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Wenn `true`, generiert das SDK zusätzliche Attribute, die von MVC zur Laufzeit verwendet werden, um die Ermittlung von Anwendungs Teilen auszuführen. |

Weitere Informationen zu Eigenschaften finden Sie unter [MSBuild-Eigenschaften](/visualstudio/msbuild/msbuild-properties).

### <a name="targets"></a>Ziele

Das Razor SDK definiert zwei Hauptziele:

* `RazorGenerate`-&ndash;-Code generiert *CS* -Dateien aus `RazorGenerate` item-Elementen. Verwenden Sie die `RazorGenerateDependsOn`-Eigenschaft, um zusätzliche Ziele anzugeben, die vor oder nach diesem Ziel ausgeführt werden können.
* mit `RazorCompile` &ndash; werden generierte *CS* -Dateien in in eine Razor-Assembly kompiliert. Verwenden Sie den `RazorCompileDependsOn`, um zusätzliche Ziele anzugeben, die vor oder nach diesem Ziel ausgeführt werden können.
* `RazorComponentGenerate`-&ndash;-Code generiert *CS* -Dateien für `RazorComponent`-Element Elemente. Verwenden Sie die `RazorComponentGenerateDependsOn`-Eigenschaft, um zusätzliche Ziele anzugeben, die vor oder nach diesem Ziel ausgeführt werden können.

### <a name="runtime-compilation-of-razor-views"></a>Kompilierung von Razor-Ansichten zur Laufzeit

* Standardmäßig veröffentlicht das Razor SDK keine Verweisassemblys, die für das Durchführen der Laufzeitkompilierung erforderlich sind. Dadurch kommt es zu Kompilierungsfehlern, wenn das Anwendungsmodell von der Laufzeitkompilierung abhängt (z.B. wenn die App eingebettete Ansichten verwendet oder Ansichten nach dem Veröffentlichen der App verändert werden). Legen Sie `CopyRefAssembliesToPublishDirectory` auf `true` fest, um mit dem Veröffentlichen von Verweisassemblys fortzufahren.

* Stellen Sie für eine Web-App sicher, dass Ihre APP auf das `Microsoft.NET.Sdk.Web`-SDK abzielt.

## <a name="razor-language-version"></a>Razor-Sprachversion

Wenn das `Microsoft.NET.Sdk.Web`-SDK als Ziel verwendet wird, wird die Razor-Sprachversion von der Zielframeworkversion der APP abgeleitet. Für Projekte, die auf das `Microsoft.NET.Sdk.Razor`-SDK abzielen, oder in dem seltenen Fall, dass die APP eine andere Version der Razor-Sprache benötigt als der abherzustehende Wert, kann eine Version konfiguriert werden, indem die Eigenschaft `<RazorLangVersion>` in der Projektdatei der APP festgelegt wird:

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

Die Sprachversion von Razor ist eng in die Version der Laufzeit integriert, für die Sie erstellt wurde. Das Ziel einer Sprachversion, die nicht für die Laufzeit konzipiert ist, wird nicht unterstützt und erzeugt wahrscheinlich Buildfehler.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Erweiterungen des CSPROJ-Formats für .NET Core](/dotnet/core/tools/csproj)
* [Gemeinsame MSBuild-Projektelemente](/visualstudio/msbuild/common-msbuild-project-items)
