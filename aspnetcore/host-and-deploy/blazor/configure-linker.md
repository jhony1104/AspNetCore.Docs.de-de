---
title: Konfigurieren des Linkers für ASP.NET Core Blazor
author: guardrex
description: Erfahren Sie, wie Sie den Intermediate Language Linker (IL) beim Erstellen einer Blazor-App steuern.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/14/2019
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: bdddae16885f45df2c10e4d98b1c33eb11dfdf24
ms.sourcegitcommit: 4ef0362ef8b6e5426fc5af18f22734158fe587e1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67153210"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="e46f3-103">Konfigurieren des Linkers für ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="e46f3-103">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="e46f3-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e46f3-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e46f3-105">Blazor führt bei einem Releasebuild eine [IL-Verknüpfung](/dotnet/standard/managed-code#intermediate-language--execution) durch, um nicht benötigte IL aus den Ausgabeassemblys der App zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="e46f3-105">Blazor performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a Release build to remove unnecessary IL from the app's output assemblies.</span></span>

<span data-ttu-id="e46f3-106">Sie können die Assemblyverknüpfung mit einer der folgenden Methoden steuern:</span><span class="sxs-lookup"><span data-stu-id="e46f3-106">Control assembly linking using either of the following approaches:</span></span>

* <span data-ttu-id="e46f3-107">Globales Deaktivieren der Verknüpfung mit einer [MSBuild-Eigenschaft](#disable-linking-with-a-msbuild-property)</span><span class="sxs-lookup"><span data-stu-id="e46f3-107">Disable linking globally with a [MSBuild property](#disable-linking-with-a-msbuild-property).</span></span>
* <span data-ttu-id="e46f3-108">Steuern der Verknüpfung für unterschiedliche Assemblys mit einer [Konfigurationsdatei](#control-linking-with-a-configuration-file)</span><span class="sxs-lookup"><span data-stu-id="e46f3-108">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="disable-linking-with-a-msbuild-property"></a><span data-ttu-id="e46f3-109">Deaktivieren der Verknüpfung mit einer MSBuild-Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="e46f3-109">Disable linking with a MSBuild property</span></span>

<span data-ttu-id="e46f3-110">Die Verknüpfung wird standardmäßig im Releasemodus aktiviert, wenn eine App erstellt wird, was die Veröffentlichung enthält.</span><span class="sxs-lookup"><span data-stu-id="e46f3-110">Linking is enabled by default in Release mode when an app is built, which includes publishing.</span></span> <span data-ttu-id="e46f3-111">Um die Verknüpfung für alle Assemblys zu deaktivieren, legen Sie in der Projektdatei für die `<BlazorLinkOnBuild>`-MSBuild-Eigenschaft `false` fest:</span><span class="sxs-lookup"><span data-stu-id="e46f3-111">To disable linking for all assemblies, set the `<BlazorLinkOnBuild>` MSBuild property to `false` in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorLinkOnBuild>false</BlazorLinkOnBuild>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="e46f3-112">Steuern der Verknüpfung mit einer Konfigurationsdatei.</span><span class="sxs-lookup"><span data-stu-id="e46f3-112">Control linking with a configuration file</span></span>

<span data-ttu-id="e46f3-113">Sie können die Verknüpfung für unterschiedliche Assemblys steuern, indem Sie eine XML-Konfigurationsdatei bereitstellen und die Datei als MSBuild-Element in der Projektdatei festlegen:</span><span class="sxs-lookup"><span data-stu-id="e46f3-113">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```

<span data-ttu-id="e46f3-114">*Linker.xml*:</span><span class="sxs-lookup"><span data-stu-id="e46f3-114">*Linker.xml*:</span></span>

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

<span data-ttu-id="e46f3-115">Weitere Informationen finden Sie unter [IL Linker: Syntax of xml descriptor (IL-Linker: Syntax des XML-Deskriptors)](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).</span><span class="sxs-lookup"><span data-stu-id="e46f3-115">For more information, see [IL Linker: Syntax of xml descriptor](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).</span></span>
