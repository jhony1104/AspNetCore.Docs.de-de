---
title: Konfigurieren des Linkers für ASP.NET Core Blazor
author: guardrex
description: Erfahren Sie, wie Sie den IL-Linker (Intermediate Language, Zwischensprache) beim Erstellen einer Blazor-App steuern.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: cdcd62915b8f1bae26773ed91e55973527e158f6
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/17/2020
ms.locfileid: "76160274"
---
# <a name="configure-the-linker-for-aspnet-core-opno-locblazor"></a>Konfigurieren des Linkers für ASP.NET Core Blazor

Von [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor führt bei einem Build eine [IL](/dotnet/standard/managed-code#intermediate-language--execution)-Verknüpfung durch, um nicht benötigte Zwischensprache aus den Ausgabeassemblys der App zu entfernen.

Sie können die Assemblyverknüpfung mit einer der folgenden Methoden steuern:

* Globales Deaktivieren der Verknüpfung mit einer [MSBuild-Eigenschaft](#disable-linking-with-a-msbuild-property)
* Steuern der Verknüpfung für unterschiedliche Assemblys mit einer [Konfigurationsdatei](#control-linking-with-a-configuration-file)

## <a name="disable-linking-with-a-msbuild-property"></a>Deaktivieren der Verknüpfung mit einer MSBuild-Eigenschaft

Die Verknüpfung wird standardmäßig aktiviert, wenn eine App erstellt wird. Dieser Prozess umfasst die Veröffentlichung. Um die Verknüpfung für alle Assemblys zu deaktivieren, legen Sie in der Projektdatei für die `BlazorLinkOnBuild`-MSBuild-Eigenschaft `false` fest:

```xml
<PropertyGroup>
  <BlazorLinkOnBuild>false</BlazorLinkOnBuild>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a>Steuern der Verknüpfung mit einer Konfigurationsdatei.

Sie können die Verknüpfung für unterschiedliche Assemblys steuern, indem Sie eine XML-Konfigurationsdatei bereitstellen und die Datei als MSBuild-Element in der Projektdatei festlegen:

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```

*Linker.xml*:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--
  This file specifies which parts of the BCL or Blazor packages must not be
  stripped by the IL Linker even if they aren't referenced by user code.
-->
<linker>
  <assembly fullname="mscorlib">
    <!--
      Preserve the methods in WasmRuntime because its methods are called by 
      JavaScript client-side code to implement timers.
      Fixes: https://github.com/dotnet/blazor/issues/239
    -->
    <type fullname="System.Threading.WasmRuntime" />
  </assembly>
  <assembly fullname="System.Core">
    <!--
      System.Linq.Expressions* is required by Json.NET and any 
      expression.Compile caller. The assembly isn't stripped.
    -->
    <type fullname="System.Linq.Expressions*" />
  </assembly>
  <!--
    In this example, the app's entry point assembly is listed. The assembly
    isn't stripped by the IL Linker.
  -->
  <assembly fullname="MyCoolBlazorApp" />
</linker>
```

Weitere Informationen finden Sie unter [IL Linker: Syntax of xml descriptor (IL-Linker: Syntax des XML-Deskriptors)](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).

### <a name="configure-the-linker-for-internationalization"></a>Konfigurieren des Linkers für die Internationalisierung

Die Blazor-Linkerkonfiguration für Blazor-WebAssembly-Apps entfernt standardmäßig Internationalisierungsinformationen, mit Ausnahme von explizit angeforderten Gebietsschemas. Durch die Entfernung dieser Assemblys wird die Größe der App minimiert.

Legen Sie die MSBuild-Eigenschaft `<MonoLinkerI18NAssemblies>` in der Projektdatei fest, um zu steuern, welche I18N-Assemblys beibehalten werden:

```xml
<PropertyGroup>
  <MonoLinkerI18NAssemblies>{all|none|REGION1,REGION2,...}</MonoLinkerI18NAssemblies>
</PropertyGroup>
```

| Regionswert     | Mono-Regionsassembly    |
| ---------------- | ----------------------- |
| `all`            | Alle Assemblys sind enthalten |
| `cjk`            | *I18N.CJK.dll*          |
| `mideast`        | *I18N.MidEast.dll*      |
| `none` (Standardwert) | Keine                    |
| `other`          | *I18N.Other.dll*        |
| `rare`           | *I18N.Rare.dll*         |
| `west`           | *I18N.West.dll*         |

Verwenden Sie ein Komma, um mehrere Werte voneinander zu trennen (z. B. `mideast,west`).

Weitere Informationen finden Sie unter [I18N: Pnetlib-Framework-Bibliothek für die Internationalisierung (Mono/Mono-Github-Repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).
