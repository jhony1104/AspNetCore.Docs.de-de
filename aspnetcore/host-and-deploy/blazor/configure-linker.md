---
title: Konfigurieren des Linkers für Blazor
author: guardrex
description: Erfahren Sie, wie Sie den Intermediate Language Linker (IL) beim Erstellen einer Blazor-App steuern.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: 01e18498a16e86392755b02b92ffda929669cb7d
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614685"
---
# <a name="configure-the-linker-for-blazor"></a>Konfigurieren des Linkers für Blazor

Von [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

Blazor führt bei jedem Releasemodusbuild eine [IL-Verknüpfung](/dotnet/standard/managed-code#intermediate-language--execution) durch, um nicht benötigte IL aus den Ausgabeassemblys der App zu entfernen.

Sie können die Assemblyverknüpfung mit einer der folgenden Methoden steuern:

* Globales Deaktivieren der Verknüpfung mit einer [MSBuild-Eigenschaft](#disable-linking-with-a-msbuild-property)
* Steuern der Verknüpfung für unterschiedliche Assemblys mit einer [Konfigurationsdatei](#control-linking-with-a-configuration-file)

## <a name="disable-linking-with-a-msbuild-property"></a>Deaktivieren der Verknüpfung mit einer MSBuild-Eigenschaft

Die Verknüpfung wird standardmäßig im Releasemodus aktiviert, wenn eine App erstellt wird, was die Veröffentlichung enthält. Um die Verknüpfung für alle Assemblys zu deaktivieren, legen Sie in der Projektdatei für die `<BlazorLinkOnBuild>`-MSBuild-Eigenschaft `false` fest:

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
      Fixes: https://github.com/aspnet/Blazor/issues/239
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
