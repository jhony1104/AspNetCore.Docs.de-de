---
title: Konfigurieren des Linkers für Blazor
author: guardrex
description: Erfahren Sie, wie Sie den Intermediate Language Linker (IL) beim Erstellen einer Blazor-App steuern.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/20/2019
uid: host-and-deploy/razor-components/configure-linker
ms.openlocfilehash: 7c53e7912ec3b0ae471ea38777f874f55a32487d
ms.sourcegitcommit: 0945078a09c372f17e9b003758ed87e99c2449f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/21/2019
ms.locfileid: "56647940"
---
# <a name="configure-the-linker-for-blazor"></a>Konfigurieren des Linkers für Blazor

Von [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

Blazor führt auf jedem Releasemodusbuild eine [Intermediate Language-Verknüpfung (IL)](/dotnet/standard/managed-code#intermediate-language--execution) durch, um nicht benötigte IL aus den Ausgabeassemblys zu entfernen.

Sie können die Assemblyverknüpfung mit einer der folgenden Methoden steuern:

* Globales Deaktivieren der Verknüpfung mit einer MSBuild-Eigenschaft.
* Steuern der Verknüpfung pro Assembly mit einer Konfigurationsdatei.

## <a name="disable-linking-with-an-msbuild-property"></a>Deaktivieren der Verknüpfung mit einer MSBuild-Eigenschaft.

Die Verknüpfung wird standardmäßig im Releasemodus aktiviert, wenn eine App erstellt wird, was die Veröffentlichung enthält. Um die Verknüpfung für alle Assemblys zu deaktivieren, legen Sie in der Projektdatei für die `<BlazorLinkOnBuild>`-MSBuild-Eigenschaft `false` fest:

```xml
<PropertyGroup>
  <BlazorLinkOnBuild>false</BlazorLinkOnBuild>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a>Steuern der Verknüpfung mit einer Konfigurationsdatei.

Die Verknüpfung kann pro Assembly durch Bereitstellen einer XML-Konfigurationsdatei und Festlegung der Datei als MSBuild-Element in der Projektdatei gesteuert werden.

Im Folgenden finden Sie ein Beispiel für die Konfigurationsdatei (*Linker.xml*):

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

Weitere Informationen zum Dateiformat für die Konfigurationsdatei finden Sie unter [IL-Linker: Syntax of xml descriptor (IL-Linker: Syntax des XML-Deskriptors)](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).

Geben Sie die Konfigurationsdatei in der Projektdatei mit dem `BlazorLinkerDescriptor`-Element an:

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```
