---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: Erfahren Sie, wie Razor Pages in ASP.NET Core codierungsseitige Szenarios einfacher und produktiver gestalten als MVC.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/25/2018
uid: razor-pages/sdk
ms.openlocfilehash: de51c9443e639cd64c234b6975cf7252bb7a2b9a
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64895297"
---
# <a name="aspnet-core-razor-sdk"></a>ASP.NET Core Razor SDK

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

Die [!INCLUDE[](~/includes/2.1-SDK.md)] enthält die `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor-SDK). Das Razor SDK:

* Standardisiert die Vorgänge zum Erstellen, Verpacken und Veröffentlichen von Projekten, die [Razor](xref:mvc/views/razor)-Dateien für auf ASP.NET Core MVC basierende Projekte enthalten.
* Enthält eine vordefinierte Reihe von Zielen, Eigenschaften und Elementen, die das Anpassen der Razor-Dateien ermöglichen.

## <a name="prerequisites"></a>Vorraussetzungen

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="using-the-razor-sdk"></a>Verwenden des Razor SDK

Die meisten Web-apps müssen nicht explizit das Razor-SDK zu verweisen.

So verwenden Sie das Razor SDK zum Erstellen von Klassenbibliotheken, die Razor-Ansichten oder Razor-Seiten enthalten:

* Verwenden Sie `Microsoft.NET.Sdk.Razor` anstelle von `Microsoft.NET.Sdk`:

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    ...
  </Project>
  ```

* In der Regel einen Paketverweis auf `Microsoft.AspNetCore.Mvc` ist erforderlich, um zusätzliche Abhängigkeiten zu erhalten, die zum Erstellen und Kompilieren von Razor Pages und Razor-Ansichten erforderlich sind. Zumindest sollten Ihr Projekt Paketverweise zum Hinzufügen:

  * `Microsoft.AspNetCore.Razor.Design` 
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`
    
  Die `Microsoft.AspNetCore.Razor.Design` Paket stellt die Razor-Kompilierung-Aufgaben und-Ziele für das Projekt bereit.

  Diese Pakete sind in `Microsoft.AspNetCore.Mvc` enthalten. Das folgende Markup zeigt eine Projektdatei, die die Razor-SDK verwendet, um die Razor-Dateien für eine ASP.NET Core Razor Pages-app zu erstellen:
    
  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> Die `Microsoft.AspNetCore.Razor.Design` und `Microsoft.AspNetCore.Mvc.Razor.Extensions` Pakete befinden sich der [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app). Allerdings das Version-kleiner `Microsoft.AspNetCore.App` Paketverweis bietet ein metapaket an die app, die die neueste Version von keine `Microsoft.AspNetCore.Razor.Design`. Projekte müssen eine einheitliche Version des verweisen `Microsoft.AspNetCore.Razor.Design` (oder `Microsoft.AspNetCore.Mvc`) so, dass die neueste Fixes der Buildzeit für Razor enthalten sind. Weitere Informationen finden Sie unter [GitHub-Problem](https://github.com/aspnet/Razor/issues/2553).

::: moniker-end

### <a name="properties"></a>Eigenschaften

Die folgenden Eigenschaften steuern das Verhalten des Razor SDK im Rahmen eines Projektbuilds:

* `RazorCompileOnBuild` &ndash; Wenn `true`, kompiliert, und gibt die Razor-Assembly im Rahmen der Erstellung des Projekts. Wird standardmäßig auf `true` festgelegt.
* `RazorCompileOnPublish` &ndash; Wenn `true`, kompiliert, und gibt die Razor-Assembly als Teil der Veröffentlichung des Projekts. Wird standardmäßig auf `true` festgelegt.

Die Eigenschaften und Elemente in der folgenden Tabelle werden zum Konfigurieren von ein- und Ausgabe an die Razor-SDK verwendet.

| Elemente | Beschreibung |
| ----- | ----------- |
| `RazorGenerate` | Item-Elemente (*CSHTML*-Dateien), die Eingaben für Codegenerierungsziele sind. |
| `RazorCompile` | Die Item-Elemente (*cs* Dateien), die Eingaben für Razor-kompilierungsziele sind. Verwenden Sie dieses ItemGroup-Element, um zusätzliche Dateien für die Kompilierung in die Razor-Assembly anzugeben. |
| `RazorTargetAssemblyAttribute` | Item-Elemente, die für das Codieren von Generate-Attributen für die Razor-Assembly verwendet werden. Zum Beispiel:  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | Elemente, die als eingebettete Ressourcen für die generierte Razor-Assembly hinzugefügt werden. |

| Eigenschaft | Beschreibung |
| -------- | ----------- |
| `RazorTargetName` | Dateiname (ohne Erweiterung) der Assembly, die von Razor erstellt wurde. | 
| `RazorOutputPath` | Das Razor-Ausgabeverzeichnis. |
| `RazorCompileToolset` | Wird verwendet, um das Toolset für die Erstellung der Razor-Assembly zu bestimmen. Gültige Werte sind `Implicit`, `RazorSDK` und `PrecompilationTool`. |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | Der Standardwert ist `true`. Wenn `true`, umfasst *"Web.config"*, *JSON*, und *.cshtml* -Dateien als Inhalt in das Projekt. Wenn auf die verwiesen wird über `Microsoft.NET.Sdk.Web`, Dateien unter *"Wwwroot"* und Konfigurationsdateien sind ebenfalls enthalten. |
| `EnableDefaultRazorGenerateItems` | Enthält *CSHTML*-Dateien aus `Content`-Elementen in `RazorGenerate`-Elementen, wenn `true` festgelegt ist. |
| `GenerateRazorTargetAssemblyInfo` | Wenn `true`, generiert eine *cs* -Datei mit der Attribute durch `RazorAssemblyAttribute` und die Datei in der Kompilierungsausgabe. |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | Fügt einen Standardsatz von Assembly-Attributen zu `RazorAssemblyAttribute` hinzu, wenn `true` festgelegt ist. |
| `CopyRazorGenerateFilesToPublishDirectory` | Wenn `true`, Kopien `RazorGenerate` Elemente (*.cshtml*) Dateien in das Veröffentlichungsverzeichnis. Razor-Dateien nicht in der Regel für eine veröffentlichte app erforderlich, wenn sie an der Kompilierung zur Buildzeit oder veröffentlichen-Laufzeit beteiligt. Wird standardmäßig auf `false` festgelegt. |
| `CopyRefAssembliesToPublishDirectory` | Kopiert Referenzassembly-Elemente in das Veröffentlichungsverzeichnis, wenn `true` festgelegt ist. In der Regel sind nicht referenzierten Assemblys für eine veröffentlichte app erforderlich, wenn Razor-Kompilierung zur Buildzeit oder veröffentlichen-Laufzeit auftritt. Legen Sie auf `true` Ihre veröffentlichte app-Runtime-Kompilierung benötigt. Legen Sie den Wert beispielsweise auf `true` bei die app geändert *.cshtml* Dateien zur Laufzeit oder eingebettete Ansichten verwendet. Wird standardmäßig auf `false` festgelegt. |
| `IncludeRazorContentInPack` | Wenn `true`, alle Razor-Inhaltselemente (*.cshtml* Dateien) für die Aufnahme in das generierte NuGet-Paket gekennzeichnet sind. Wird standardmäßig auf `false` festgelegt. |
| `EmbedRazorGenerateSources` | Fügt RazorGenerate-Elemente (*CSHTML*-Dateien) als eingebettete Dateien in die generierte Razor-Assembly hinzu, wenn `true` festgelegt ist. Wird standardmäßig auf `false` festgelegt. |
| `UseRazorBuildServer` | Verwendet einen dauerhaften Buildserverprozess, um die Auslastung durch die Codegenerierung zu verlagern, wenn `true` festgelegt ist. Wird standardmäßig auf den Wert `UseSharedCompilation` festgelegt. |

Weitere Informationen zu Eigenschaften finden Sie unter [MSBuild-Eigenschaften](/visualstudio/msbuild/msbuild-properties).

### <a name="targets"></a>Ziele

Das Razor SDK definiert zwei Hauptziele:

* `RazorGenerate` &ndash; Generiert Code *cs* Dateien von `RazorGenerate` item-Elemente. Verwenden Sie die Eigenschaft `RazorGenerateDependsOn`, um zusätzliche Ziele anzugeben, die vor oder nach diesem Ziel ausgeführt werden können.
* `RazorCompile` &ndash; Kompiliert generiert *cs* Dateien im zu einer Razor-Assembly. Verwenden Sie `RazorCompileDependsOn`, um zusätzliche Ziele anzugeben, die vor oder nach diesem Ziel ausgeführt werden können.

### <a name="runtime-compilation-of-razor-views"></a>Kompilierung von Razor-Ansichten zur Laufzeit

* Standardmäßig veröffentlicht das Razor SDK keine Verweisassemblys, die für das Durchführen der Laufzeitkompilierung erforderlich sind. Dadurch kommt es zu Kompilierungsfehlern, wenn das Anwendungsmodell von der Laufzeitkompilierung abhängt (z.B. wenn die App eingebettete Ansichten verwendet oder Ansichten nach dem Veröffentlichen der App verändert werden). Legen Sie `CopyRefAssembliesToPublishDirectory` auf `true` fest, um mit dem Veröffentlichen von Verweisassemblys fortzufahren.

* Für eine Web-app, stellen Sie sicher auf die Ihre app die `Microsoft.NET.Sdk.Web` SDK.
