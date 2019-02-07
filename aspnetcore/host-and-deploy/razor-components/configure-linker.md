---
title: Konfigurieren des Linkers für Blazor
author: guardrex
description: Erfahren Sie, wie Sie den Intermediate Language Linker (IL) beim Erstellen einer Blazor-App steuern.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2019
uid: host-and-deploy/razor-components/configure-linker
ms.openlocfilehash: c3c38ec2509344cc02f3895d5d0c2d35059d1d8e
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55668014"
---
# <a name="configure-the-linker-for-blazor"></a><span data-ttu-id="c5932-103">Konfigurieren des Linkers für Blazor</span><span class="sxs-lookup"><span data-stu-id="c5932-103">Configure the Linker for Blazor</span></span>

<span data-ttu-id="c5932-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c5932-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="c5932-105">Blazor führt auf jedem Releasemodusbuild eine [Intermediate Language-Verknüpfung (IL)](/dotnet/standard/managed-code#intermediate-language--execution) durch, um nicht benötigte IL aus den Ausgabeassemblys zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="c5932-105">Blazor performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during each Release mode build to remove unnecessary IL from the output assemblies.</span></span>

<span data-ttu-id="c5932-106">Sie können die Assemblyverknüpfung mit einer der folgenden Methoden steuern:</span><span class="sxs-lookup"><span data-stu-id="c5932-106">You can control assembly linking with either of the following approaches:</span></span>

* <span data-ttu-id="c5932-107">Globales Deaktivieren der Verknüpfung mit einer MSBuild-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="c5932-107">Disable linking globally with an MSBuild property.</span></span>
* <span data-ttu-id="c5932-108">Steuern der Verknüpfung pro Assembly mit einer Konfigurationsdatei.</span><span class="sxs-lookup"><span data-stu-id="c5932-108">Control linking on a per-assembly basis with a configuration file.</span></span>

## <a name="disable-linking-with-an-msbuild-property"></a><span data-ttu-id="c5932-109">Deaktivieren der Verknüpfung mit einer MSBuild-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="c5932-109">Disable linking with an MSBuild property</span></span>

<span data-ttu-id="c5932-110">Die Verknüpfung wird standardmäßig im Releasemodus aktiviert, wenn eine App erstellt wird, was die Veröffentlichung enthält.</span><span class="sxs-lookup"><span data-stu-id="c5932-110">Linking is enabled by default in Release mode when an app is built, which includes publishing.</span></span> <span data-ttu-id="c5932-111">Um die Verknüpfung für alle Assemblys zu deaktivieren, legen Sie in der Projektdatei für die `<BlazorLinkOnBuild>`-MSBuild-Eigenschaft `false` fest:</span><span class="sxs-lookup"><span data-stu-id="c5932-111">To disable linking for all assemblies, set the `<BlazorLinkOnBuild>` MSBuild property to `false` in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorLinkOnBuild>false</BlazorLinkOnBuild>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="c5932-112">Steuern der Verknüpfung mit einer Konfigurationsdatei.</span><span class="sxs-lookup"><span data-stu-id="c5932-112">Control linking with a configuration file</span></span>

<span data-ttu-id="c5932-113">Die Verknüpfung kann pro Assembly durch Bereitstellen einer XML-Konfigurationsdatei und Festlegung der Datei als MSBuild-Element in der Projektdatei gesteuert werden.</span><span class="sxs-lookup"><span data-stu-id="c5932-113">Linking can be controlled on a per-assembly basis by providing an XML configuration file and specifying the file as an MSBuild item in the project file.</span></span>

<span data-ttu-id="c5932-114">Im Folgenden finden Sie ein Beispiel für die Konfigurationsdatei (*Linker.xml*):</span><span class="sxs-lookup"><span data-stu-id="c5932-114">The following is an example configuration file (*Linker.xml*):</span></span>

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

<span data-ttu-id="c5932-115">Weitere Informationen zum Dateiformat für die Konfigurationsdatei finden Sie unter [IL-Linker: Syntax of xml descriptor (IL-Linker: Syntax des XML-Deskriptors)](https://github.com/mono/linker/blob/master/linker/README.md#syntax-of-xml-descriptor).</span><span class="sxs-lookup"><span data-stu-id="c5932-115">To learn more about the file format for the configuration file, see [IL Linker: Syntax of xml descriptor](https://github.com/mono/linker/blob/master/linker/README.md#syntax-of-xml-descriptor).</span></span>

<span data-ttu-id="c5932-116">Geben Sie die Konfigurationsdatei in der Projektdatei mit dem `BlazorLinkerDescriptor`-Element an:</span><span class="sxs-lookup"><span data-stu-id="c5932-116">Specify the configuration file in the project file with the `BlazorLinkerDescriptor` item:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```
