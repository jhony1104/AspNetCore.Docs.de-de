---
title: Konfigurieren des Linkers für ASP.NET Core Blazor
author: guardrex
description: Erfahren Sie, wie Sie den IL-Linker (Intermediate Language, Zwischensprache) beim Erstellen einer Blazor-App steuern.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- Blazor
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: cdf506f0c0fa720df64e59342d352ef41271d24b
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/10/2020
ms.locfileid: "75866045"
---
# <a name="configure-the-linker-for-aspnet-core-opno-locblazor"></a><span data-ttu-id="b6bd8-103">Konfigurieren des Linkers für ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="b6bd8-103">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="b6bd8-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b6bd8-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="b6bd8-105"> führt bei einem Build eine [IL](/dotnet/standard/managed-code#intermediate-language--execution)-Verknüpfung durch, um nicht benötigte Zwischensprache aus den Ausgabeassemblys der App zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="b6bd8-105"> performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to remove unnecessary IL from the app's output assemblies.</span></span>

<span data-ttu-id="b6bd8-106">Sie können die Assemblyverknüpfung mit einer der folgenden Methoden steuern:</span><span class="sxs-lookup"><span data-stu-id="b6bd8-106">Control assembly linking using either of the following approaches:</span></span>

* <span data-ttu-id="b6bd8-107">Globales Deaktivieren der Verknüpfung mit einer [MSBuild-Eigenschaft](#disable-linking-with-a-msbuild-property)</span><span class="sxs-lookup"><span data-stu-id="b6bd8-107">Disable linking globally with a [MSBuild property](#disable-linking-with-a-msbuild-property).</span></span>
* <span data-ttu-id="b6bd8-108">Steuern der Verknüpfung für unterschiedliche Assemblys mit einer [Konfigurationsdatei](#control-linking-with-a-configuration-file)</span><span class="sxs-lookup"><span data-stu-id="b6bd8-108">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="disable-linking-with-a-msbuild-property"></a><span data-ttu-id="b6bd8-109">Deaktivieren der Verknüpfung mit einer MSBuild-Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="b6bd8-109">Disable linking with a MSBuild property</span></span>

<span data-ttu-id="b6bd8-110">Die Verknüpfung wird standardmäßig aktiviert, wenn eine App erstellt wird. Dieser Prozess umfasst die Veröffentlichung.</span><span class="sxs-lookup"><span data-stu-id="b6bd8-110">Linking is enabled by default when an app is built, which includes publishing.</span></span> <span data-ttu-id="b6bd8-111">Um die Verknüpfung für alle Assemblys zu deaktivieren, legen Sie in der Projektdatei für die `BlazorLinkOnBuild`-MSBuild-Eigenschaft `false` fest:</span><span class="sxs-lookup"><span data-stu-id="b6bd8-111">To disable linking for all assemblies, set the `BlazorLinkOnBuild` MSBuild property to `false` in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorLinkOnBuild>false</BlazorLinkOnBuild>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="b6bd8-112">Steuern der Verknüpfung mit einer Konfigurationsdatei.</span><span class="sxs-lookup"><span data-stu-id="b6bd8-112">Control linking with a configuration file</span></span>

<span data-ttu-id="b6bd8-113">Sie können die Verknüpfung für unterschiedliche Assemblys steuern, indem Sie eine XML-Konfigurationsdatei bereitstellen und die Datei als MSBuild-Element in der Projektdatei festlegen:</span><span class="sxs-lookup"><span data-stu-id="b6bd8-113">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```

<span data-ttu-id="b6bd8-114">*Linker.xml*:</span><span class="sxs-lookup"><span data-stu-id="b6bd8-114">*Linker.xml*:</span></span>

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

<span data-ttu-id="b6bd8-115">Weitere Informationen finden Sie unter [IL Linker: Syntax of xml descriptor (IL-Linker: Syntax des XML-Deskriptors)](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).</span><span class="sxs-lookup"><span data-stu-id="b6bd8-115">For more information, see [IL Linker: Syntax of xml descriptor](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).</span></span>

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="b6bd8-116">Konfigurieren des Linkers für die Internationalisierung</span><span class="sxs-lookup"><span data-stu-id="b6bd8-116">Configure the linker for internationalization</span></span>

<span data-ttu-id="b6bd8-117">Die Blazor-Linkerkonfiguration für Blazor-WebAssembly-Apps entfernt standardmäßig Internationalisierungsinformationen, mit Ausnahme von explizit angeforderten Gebietsschemas.</span><span class="sxs-lookup"><span data-stu-id="b6bd8-117">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="b6bd8-118">Durch die Entfernung dieser Assemblys wird die Größe der App minimiert.</span><span class="sxs-lookup"><span data-stu-id="b6bd8-118">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="b6bd8-119">Legen Sie die MSBuild-Eigenschaft `<MonoLinkerI18NAssemblies>` in der Projektdatei fest, um zu steuern, welche I18N-Assemblys beibehalten werden:</span><span class="sxs-lookup"><span data-stu-id="b6bd8-119">To control which I18N assemblies are retained, set the `<MonoLinkerI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <MonoLinkerI18NAssemblies>{all|none|REGION1,REGION2,...}</MonoLinkerI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="b6bd8-120">Regionswert</span><span class="sxs-lookup"><span data-stu-id="b6bd8-120">Region Value</span></span>     | <span data-ttu-id="b6bd8-121">Mono-Regionsassembly</span><span class="sxs-lookup"><span data-stu-id="b6bd8-121">Mono region assembly</span></span>    |
| ---------------- | ----------------------- |
| `all`            | <span data-ttu-id="b6bd8-122">Alle Assemblys sind enthalten</span><span class="sxs-lookup"><span data-stu-id="b6bd8-122">All assemblies included</span></span> |
| `cjk`            | <span data-ttu-id="b6bd8-123">*I18N.CJK.dll*</span><span class="sxs-lookup"><span data-stu-id="b6bd8-123">*I18N.CJK.dll*</span></span>          |
| `mideast`        | <span data-ttu-id="b6bd8-124">*I18N.MidEast.dll*</span><span class="sxs-lookup"><span data-stu-id="b6bd8-124">*I18N.MidEast.dll*</span></span>      |
| <span data-ttu-id="b6bd8-125">`none` (Standardwert)</span><span class="sxs-lookup"><span data-stu-id="b6bd8-125">`none` (default)</span></span> | <span data-ttu-id="b6bd8-126">Keine</span><span class="sxs-lookup"><span data-stu-id="b6bd8-126">None</span></span>                    |
| `other`          | <span data-ttu-id="b6bd8-127">*I18N.Other.dll*</span><span class="sxs-lookup"><span data-stu-id="b6bd8-127">*I18N.Other.dll*</span></span>        |
| `rare`           | <span data-ttu-id="b6bd8-128">*I18N.Rare.dll*</span><span class="sxs-lookup"><span data-stu-id="b6bd8-128">*I18N.Rare.dll*</span></span>         |
| `west`           | <span data-ttu-id="b6bd8-129">*I18N.West.dll*</span><span class="sxs-lookup"><span data-stu-id="b6bd8-129">*I18N.West.dll*</span></span>         |

<span data-ttu-id="b6bd8-130">Verwenden Sie ein Komma, um mehrere Werte voneinander zu trennen (z. B. `mideast,west`).</span><span class="sxs-lookup"><span data-stu-id="b6bd8-130">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="b6bd8-131">Weitere Informationen finden Sie unter [I18N: Pnetlib-Framework-Bibliothek für die Internationalisierung (Mono/Mono-Github-Repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span><span class="sxs-lookup"><span data-stu-id="b6bd8-131">For more information, see [I18N: Pnetlib Internationalization Framework Libary (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>
