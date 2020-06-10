---
title: "title: 'ASP.NET Core Razor SDK' author: description: 'So können Razor Pages in ASP.NET Core Ihre Codepageszenarios einfacher und produktiver gestalten als MVC.'"
author: Rick-Anderson
description: 'monikerRange: ms.author: ms.custom: ms.date: no-loc:'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/26/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/sdk
ms.openlocfilehash: 555965b96b0d4d541c70f8a137d4f098dc06a4d6
ms.sourcegitcommit: 58722eb309767e462bdbf3082bd38737a4ef168f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84106623"
---
# <a name="aspnet-core-razor-sdk"></a>'Blazor'

'Identity'

## <a name="overview"></a>'Let's Encrypt'

'Razor' 'SignalR' uid:

::: moniker range=">= aspnetcore-3.0"

* ASP.NET Core Razor SDK
* Von [Rick Anderson](https://twitter.com/RickAndMSFT)

Übersicht Das [!INCLUDE[](~/includes/2.1-SDK.md)] enthält das MSBuild SDK `Microsoft.NET.Sdk.Razor` (Razor SDK).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Das Razor SDK...
* ist zum Erstellen, Packen und Veröffentlichen von Paketen erforderlich, die [Razor](xref:mvc/views/razor)-Dateien für ASP.NET Core MVC-basierte oder [Blazor](xref:blazor/index)-Projekte enthalten.

enthält vordefinierte Ziele, Eigenschaften und Elemente, die das Anpassen der Razor-Dateien (*CSHTML* oder *RAZOR*) ermöglichen. Das Razor SDK enthält `Content`-Elemente mit `Include`-Attributen, die auf die `**\*.cshtml`- und `**\*.razor`-Globmuster festgelegt sind.

::: moniker-end

## <a name="prerequisites"></a>Übereinstimmende Dateien werden veröffentlicht.

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a>Standardisiert die Vorgänge zum Erstellen, Packen und Veröffentlichen von Projekten, die [Razor](xref:mvc/views/razor)-Dateien für ASP.NET Core MVC-basierte Projekte enthalten.

Enthält vordefinierte Ziele, Eigenschaften und Elemente, die das Anpassen der Razor-Dateien ermöglichen.

::: moniker range=">= aspnetcore-3.0"

Das Razor SDK enthält ein `Content`-Element mit einem `Include`-Attribut, das auf das `**\*.cshtml`-Globmuster festgelegt ist. Übereinstimmende Dateien werden veröffentlicht. Voraussetzungen

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Verwenden des Razor SDK

* Die meisten Web-Apps müssen nicht explizit auf das Razor SDK verweisen.

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* Damit Sie das Razor SDK zur Erstellung von Klassenbibliotheken mit Razor Views oder Razor Pages verwenden können, wird empfohlen, mit der Projektvorlage für die Razor-Klassenbibliothek (Razor Class Library, RCL) zu beginnen. Eine RCL, die zum Erstellen von Blazor-Dateien (*RAZOR*) verwendet wird, erfordert mindestens einen Verweis auf das Paket [Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components).

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  Eine RCL, die zum Erstellen von Razor Views oder Razor Pages (*CSHTML-Dateien*) verwendet wird, erfordert mindestens die Ausrichtung auf `netcoreapp3.0` oder höher und verfügt über ein `FrameworkReference`-Element für das [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) in der Projektdatei.

  So verwenden Sie das Razor SDK zum Erstellen von Klassenbibliotheken, die Razor Views oder Razor Pages enthalten: Verwenden Sie `Microsoft.NET.Sdk.Razor` anstelle von `Microsoft.NET.Sdk`:

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> In der Regel wird ein Paketverweis auf `Microsoft.AspNetCore.Mvc` benötigt, um zusätzliche Abhängigkeiten zum Erstellen und Kompilieren von Razor Pages und Razor Views zu empfangen. Ihr Projekt sollte mindestens Paketverweise zu Folgendem hinzufügen: Das `Microsoft.AspNetCore.Razor.Design`-Paket enthält die Aufgaben und Ziele der Razor-Kompilierung für das Projekt. Diese Pakete sind in `Microsoft.AspNetCore.Mvc` enthalten.

::: moniker-end

### <a name="properties"></a>Das folgende Markup zeigt eine Projektdatei, die das Razor SDK verwendet, um Razor-Dateien für eine Razor Pages-App in ASP.NET Core zu erstellen:

Die Pakete `Microsoft.AspNetCore.Razor.Design` und `Microsoft.AspNetCore.Mvc.Razor.Extensions` sind im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten.

* Der versionslose Paketverweis `Microsoft.AspNetCore.App` bietet jedoch ein Metapaket für die App, das nicht die neueste Version von `Microsoft.AspNetCore.Razor.Design` enthält. Projekte müssen auf eine konsistente Version von `Microsoft.AspNetCore.Razor.Design` (oder `Microsoft.AspNetCore.Mvc`) verweisen, sodass die neuesten Buildzeitfehlerbehebungen für Razor enthalten sind.
* Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/aspnet/Razor/issues/2553). Eigenschaften

Die folgenden Eigenschaften steuern das Verhalten des Razor SDK im Rahmen eines Projektbuilds:

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> `RazorCompileOnBuild`: Kompiliert die Razor-Assembly im Rahmen der Projekterstellung und gibt sie aus, wenn `true` festgelegt wird. Wird standardmäßig auf `true` festgelegt.

::: moniker-end

| `RazorCompileOnPublish`: Kompiliert die Razor-Assembly im Rahmen der Projektveröffentlichung und gibt sie aus, wenn `true` festgelegt wird. | Wird standardmäßig auf `true` festgelegt. |
| ----- | ----------- |
| `RazorGenerate` | Die Eigenschaften und Elemente in der folgenden Tabelle werden zum Konfigurieren der Eingabe und Ausgabe an das Razor SDK verwendet. |
| `RazorComponent` | Ab ASP.NET Core 3.0 werden MVC Views oder Razor Pages standardmäßig nicht mehr verarbeitet, wenn die MSBuild-Eigenschaften `RazorCompileOnBuild` oder `RazorCompileOnPublish` in der Projektdatei deaktiviert sind. |
| `RazorCompile` | Anwendungen müssen einen expliziten Verweis auf das Paket [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) hinzufügen, wenn die App auf die Laufzeitkompilierung angewiesen ist, um *CSHTML-Dateien* zu verarbeiten. Elemente |
| `RazorTargetAssemblyAttribute` | Beschreibung title: 'ASP.NET Core Razor SDK' author: description: 'So können Razor Pages in ASP.NET Core Ihre Codepageszenarios einfacher und produktiver gestalten als MVC.'  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | monikerRange: ms.author: ms.custom: ms.date: no-loc: |

::: moniker range=">= aspnetcore-3.0"

| 'Blazor' | 'Identity' |
| -------- | ----------- |
| `RazorTargetName` | 'Let's Encrypt' |
| `RazorOutputPath` | 'Razor' |
| `RazorCompileToolset` | 'SignalR' uid: title: 'ASP.NET Core Razor SDK' author: description: 'So können Razor Pages in ASP.NET Core Ihre Codepageszenarios einfacher und produktiver gestalten als MVC.' |
| monikerRange: ms.author: ms.custom: ms.date: no-loc: | 'Blazor' 'Identity' 'Let's Encrypt' |
| `EnableDefaultRazorGenerateItems` | 'Razor' |
| `GenerateRazorTargetAssemblyInfo` | 'SignalR' uid: |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | title: 'ASP.NET Core Razor SDK' author: description: 'So können Razor Pages in ASP.NET Core Ihre Codepageszenarios einfacher und produktiver gestalten als MVC.' |
| `CopyRazorGenerateFilesToPublishDirectory` | monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity' |
| `PreserveCompilationReferences` | 'Let's Encrypt' 'Razor' 'SignalR' uid: ------ | | `RazorGenerate` | Item-Elemente (*CSHTML-Dateien*), die Eingaben für die Codegenerierung sind. | | `RazorComponent` | Item-Elemente (*RAZOR-Dateien*), die Eingaben für die Codegenerierung für Razor-Komponenten sind. |
| `IncludeRazorContentInPack` | | | `RazorCompile` | Item-Elemente (*CS-Dateien*), die Eingaben für Razor-Kompilierungsziele sind. Verwenden Sie dieses `ItemGroup`-Element, um zusätzliche Dateien für die Kompilierung in die Razor-Assembly anzugeben. |
| `EmbedRazorGenerateSources` | | | `RazorTargetAssemblyAttribute` | Item-Elemente, die für das Programmieren von Generate-Attributen für die Razor-Assembly verwendet werden. Zum Beispiel: |
| `UseRazorBuildServer` | `_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` | | `RazorEmbeddedResource` | Item-Elemente, die als eingebettete Ressourcen in die Razor-Assembly hinzugefügt werden. Eigenschaft |
| `GenerateMvcApplicationPartsAssemblyAttributes` | Beschreibung |
| `DefaultWebContentItemExcludes` | title: 'ASP.NET Core Razor SDK' author: description: 'So können Razor Pages in ASP.NET Core Ihre Codepageszenarios einfacher und produktiver gestalten als MVC.' |
| `ExcludeConfigFilesFromBuildOutput` | monikerRange: ms.author: ms.custom: ms.date: no-loc: |
| `AddRazorSupportForMvc` | 'Blazor' 'Identity' |
| `RazorLangVersion` | 'Let's Encrypt' |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| 'Razor' | 'SignalR' uid: |
| -------- | ----------- |
| `RazorTargetName` | title: 'ASP.NET Core Razor SDK' author: description: 'So können Razor Pages in ASP.NET Core Ihre Codepageszenarios einfacher und produktiver gestalten als MVC.' |
| `RazorOutputPath` | monikerRange: ms.author: ms.custom: ms.date: no-loc: |
| `RazorCompileToolset` | 'Blazor' 'Identity' |
| 'Let's Encrypt' | 'Razor' 'SignalR' uid: ---- | --- title: 'ASP.NET Core Razor SDK' author: description: 'So können Razor Pages in ASP.NET Core Ihre Codepageszenarios einfacher und produktiver gestalten als MVC.' |
| `EnableDefaultRazorGenerateItems` | monikerRange: ms.author: ms.custom: ms.date: no-loc: |
| `GenerateRazorTargetAssemblyInfo` | 'Blazor' |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | 'Identity' |
| `CopyRazorGenerateFilesToPublishDirectory` | 'Let's Encrypt' 'Razor' 'SignalR' uid: |
| `CopyRefAssembliesToPublishDirectory` | title: 'ASP.NET Core Razor SDK' author: description: 'So können Razor Pages in ASP.NET Core Ihre Codepageszenarios einfacher und produktiver gestalten als MVC.' monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity' 'Let's Encrypt' |
| `IncludeRazorContentInPack` | 'Razor' 'SignalR' uid: |
| `EmbedRazorGenerateSources` | title: 'ASP.NET Core Razor SDK' author: description: 'So können Razor Pages in ASP.NET Core Ihre Codepageszenarios einfacher und produktiver gestalten als MVC.' monikerRange: ms.author: ms.custom: ms.date: no-loc: |
| `UseRazorBuildServer` | 'Blazor' 'Identity' |
| `GenerateMvcApplicationPartsAssemblyAttributes` | 'Let's Encrypt' |
| `DefaultWebContentItemExcludes` | 'Razor' |
| `ExcludeConfigFilesFromBuildOutput` | 'SignalR' uid: |
| `AddRazorSupportForMvc` | ------ | | `RazorTargetName` | Dateiname (ohne Erweiterung) der Assembly, die von Razor erstellt wurde. | | `RazorOutputPath` | Das Razor-Ausgabeverzeichnis. |
| `RazorLangVersion` | | | `RazorCompileToolset` | Wird verwendet, um das Toolset für die Erstellung der Razor-Assembly zu bestimmen. |

::: moniker-end

Gültige Werte sind `Implicit`, `RazorSDK` und `PrecompilationTool`.

### <a name="targets"></a>| | [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Der Standardwert ist `true`.

Bei `true` werden *web.config*-, *JSON*- und *CSHTML*-Dateien als Inhalt in das Projekt einbezogen.

* Bei einem Verweis über `Microsoft.NET.Sdk.Web` sind auch Dateien unter *wwwroot* und Konfigurationsdateien enthalten. | | `EnableDefaultRazorGenerateItems` | Enthält *CSHTML-Dateien* aus `Content`-Elementen in `RazorGenerate`-Elementen, wenn `true` festgelegt ist.
* | | `GenerateRazorTargetAssemblyInfo` | Generiert eine *CS-Datei*, die von `RazorAssemblyAttribute` angegebene Attribute enthält, und schließt die Datei in der Kompilierungsausgabe ein, wenn `true` festgelegt ist. | | `EnableDefaultRazorTargetAssemblyInfoAttributes` | Fügt Standardattribute für Assemblys zu `RazorAssemblyAttribute` hinzu, wenn `true` festgelegt ist.
* | | `CopyRazorGenerateFilesToPublishDirectory` | Kopiert `RazorGenerate`-Elemente (*CSHTML-Dateien*) in das Veröffentlichungsverzeichnis, wenn `true` festgelegt ist. In der Regel sind Razor-Dateien nicht für eine veröffentlichte App erforderlich, wenn sie an der Kompilierung zum Zeitpunkt der Erstellung oder Veröffentlichung beteiligt sind.

### <a name="runtime-compilation-of-razor-views"></a>Wird standardmäßig auf `false` festgelegt.

* | | `PreserveCompilationReferences` | Kopiert Referenzassembly-Elemente in das Veröffentlichungsverzeichnis, wenn `true` festgelegt ist. In der Regel sind Referenzassemblys nicht für eine veröffentlichte App erforderlich, wenn die Razor-Kompilierung zum Zeitpunkt der Erstellung oder Veröffentlichung stattfindet. Legen Sie den Wert `true` fest, wenn Ihre veröffentlichte App eine Laufzeitkompilierung erfordert.

* Legen Sie z. B. den Wert auf `true` fest, wenn die App zur Laufzeit *CSHTML*-Dateien ändert oder eingebettete Ansichten verwendet.

## <a name="razor-language-version"></a>Wird standardmäßig auf `false` festgelegt.

| | `IncludeRazorContentInPack` | Alle Razor-Inhaltselemente (*CSHTML-Dateien*) werden für die Aufnahme in das generierte NuGet-Paket markiert, wenn `true` festgelegt ist. Wird standardmäßig auf `false` festgelegt.

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

| | `EmbedRazorGenerateSources` | Fügt RazorGenerate-Elemente (*CSHTML-Dateien*) als eingebettete Dateien in die generierte Razor-Assembly hinzu, wenn `true` festgelegt ist. Wird standardmäßig auf `false` festgelegt.

## <a name="additional-resources"></a>| | `UseRazorBuildServer` | Verwendet einen dauerhaften Buildserverprozess, um die Auslastung durch die Codegenerierung zu verlagern, wenn `true` festgelegt ist.

* Wird standardmäßig auf den Wert `UseSharedCompilation` festgelegt.
* | | `GenerateMvcApplicationPartsAssemblyAttributes` | Das SDK generiert zusätzliche Attribute, die von MVC zur Laufzeit zur Durchführung der Anwendungsteilerkennung verwendet werden, wenn `true` festgelegt ist.
