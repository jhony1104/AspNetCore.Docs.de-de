---
title: Konfigurieren des Linkers für ASP.NET Core Blazor
author: guardrex
description: Erfahren Sie, wie Sie den IL-Linker (Intermediate Language, Zwischensprache) beim Erstellen einer Blazor-App steuern.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/10/2020
no-loc:
- Blazor
- SignalR
uid: host-and-deploy/blazor/configure-linker
ms.openlocfilehash: b08ec26fb8d139223c57774600bc3cb19a56ac49
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083292"
---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="61e1c-103">Konfigurieren des Linkers für ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="61e1c-103">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="61e1c-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="61e1c-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="61e1c-105">Blazor WebAssembly führt bei einem Build eine [IL](/dotnet/standard/managed-code#intermediate-language--execution)-Verknüpfung durch, um nicht benötigte Zwischensprache aus den Ausgabeassemblys der App zu kürzen.</span><span class="sxs-lookup"><span data-stu-id="61e1c-105">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="61e1c-106">Der Linker ist beim Buildvorgang in der Debugkonfiguration deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="61e1c-106">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="61e1c-107">Apps müssen in der Releasekonfiguration erstellt werden, damit der Linker aktiviert wird.</span><span class="sxs-lookup"><span data-stu-id="61e1c-107">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="61e1c-108">Es wird empfohlen, die Releasekonfiguration beim Buildvorgang anzuwenden, wenn Sie Blazor WebAssembly-Apps erstellen.</span><span class="sxs-lookup"><span data-stu-id="61e1c-108">We recommend building in Release when deploying your Blazor WebAssembly apps.</span></span> 

<span data-ttu-id="61e1c-109">Das Verknüpfen einer App optimiert die Größe, kann jedoch auch negative Auswirkungen haben.</span><span class="sxs-lookup"><span data-stu-id="61e1c-109">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="61e1c-110">Apps, die Reflektion oder ähnliche dynamische Features verwenden, können beim Kürzen unterbrochen werden, da der Linker dieses dynamische Verhalten nicht kennt und nicht ermitteln kann, welche Typen für die Reflektion zur Laufzeit erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="61e1c-110">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="61e1c-111">Zur Kürzung solcher Apps muss der Linker über alle Typen informiert werden, die für die Reflektion im Code und in Paketen oder Frameworks erforderlich sind, von denen die App abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="61e1c-111">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span> 

<span data-ttu-id="61e1c-112">Wenn Sie sicherstellen möchten, dass die gekürzte App nach der Bereitstellung ordnungsgemäß funktioniert, ist es wichtig, bei der Entwicklung häufig die Releasebuilds der App zu testen.</span><span class="sxs-lookup"><span data-stu-id="61e1c-112">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="61e1c-113">Das Verknüpfen von Blazor-Apps kann mithilfe der folgenden MSBuild-Funktionen konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="61e1c-113">Linking for Blazor apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="61e1c-114">Globales Konfigurieren der Verknüpfung mit einer [MSBuild-Eigenschaft](#control-linking-with-an-msbuild-property)</span><span class="sxs-lookup"><span data-stu-id="61e1c-114">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="61e1c-115">Steuern der Verknüpfung für unterschiedliche Assemblys mit einer [Konfigurationsdatei](#control-linking-with-a-configuration-file)</span><span class="sxs-lookup"><span data-stu-id="61e1c-115">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="61e1c-116">Steuern der Verknüpfung mit einer MSBuild-Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="61e1c-116">Control linking with an MSBuild property</span></span>

<span data-ttu-id="61e1c-117">Verknüpfungen werden aktiviert, wenn eine App in der `Release`-Konfiguration erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="61e1c-117">Linking is enabled when an app is built in `Release` configuation.</span></span> <span data-ttu-id="61e1c-118">Sie können dieses Verhalten ändern, indem Sie die MSBuild-Eigenschaft `BlazorWebAssemblyEnableLinking` in der Projektdatei konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="61e1c-118">To change this, configure the `BlazorWebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="61e1c-119">Steuern der Verknüpfung mit einer Konfigurationsdatei.</span><span class="sxs-lookup"><span data-stu-id="61e1c-119">Control linking with a configuration file</span></span>

<span data-ttu-id="61e1c-120">Sie können die Verknüpfung für unterschiedliche Assemblys steuern, indem Sie eine XML-Konfigurationsdatei bereitstellen und die Datei als MSBuild-Element in der Projektdatei festlegen:</span><span class="sxs-lookup"><span data-stu-id="61e1c-120">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="Linker.xml" />
</ItemGroup>
```

<span data-ttu-id="61e1c-121">*Linker.xml*:</span><span class="sxs-lookup"><span data-stu-id="61e1c-121">*Linker.xml*:</span></span>

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

<span data-ttu-id="61e1c-122">Weitere Informationen finden Sie unter [IL Linker: Syntax of xml descriptor (IL-Linker: Syntax des XML-Deskriptors)](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).</span><span class="sxs-lookup"><span data-stu-id="61e1c-122">For more information, see [IL Linker: Syntax of xml descriptor](https://github.com/mono/linker/blob/master/src/linker/README.md#syntax-of-xml-descriptor).</span></span>

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="61e1c-123">Konfigurieren des Linkers für die Internationalisierung</span><span class="sxs-lookup"><span data-stu-id="61e1c-123">Configure the linker for internationalization</span></span>

<span data-ttu-id="61e1c-124">Die Blazor-Linkerkonfiguration für Blazor-WebAssembly-Apps entfernt standardmäßig Internationalisierungsinformationen, mit Ausnahme von explizit angeforderten Gebietsschemas.</span><span class="sxs-lookup"><span data-stu-id="61e1c-124">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="61e1c-125">Durch die Entfernung dieser Assemblys wird die Größe der App minimiert.</span><span class="sxs-lookup"><span data-stu-id="61e1c-125">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="61e1c-126">Legen Sie die MSBuild-Eigenschaft `<MonoLinkerI18NAssemblies>` in der Projektdatei fest, um zu steuern, welche I18N-Assemblys beibehalten werden:</span><span class="sxs-lookup"><span data-stu-id="61e1c-126">To control which I18N assemblies are retained, set the `<MonoLinkerI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <MonoLinkerI18NAssemblies>{all|none|REGION1,REGION2,...}</MonoLinkerI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="61e1c-127">Regionswert</span><span class="sxs-lookup"><span data-stu-id="61e1c-127">Region Value</span></span>     | <span data-ttu-id="61e1c-128">Mono-Regionsassembly</span><span class="sxs-lookup"><span data-stu-id="61e1c-128">Mono region assembly</span></span>    |
| ---------------- | ----------------------- |
| `all`            | <span data-ttu-id="61e1c-129">Alle Assemblys sind enthalten</span><span class="sxs-lookup"><span data-stu-id="61e1c-129">All assemblies included</span></span> |
| `cjk`            | <span data-ttu-id="61e1c-130">*I18N.CJK.dll*</span><span class="sxs-lookup"><span data-stu-id="61e1c-130">*I18N.CJK.dll*</span></span>          |
| `mideast`        | <span data-ttu-id="61e1c-131">*I18N.MidEast.dll*</span><span class="sxs-lookup"><span data-stu-id="61e1c-131">*I18N.MidEast.dll*</span></span>      |
| <span data-ttu-id="61e1c-132">`none` (Standardwert)</span><span class="sxs-lookup"><span data-stu-id="61e1c-132">`none` (default)</span></span> | <span data-ttu-id="61e1c-133">Keine</span><span class="sxs-lookup"><span data-stu-id="61e1c-133">None</span></span>                    |
| `other`          | <span data-ttu-id="61e1c-134">*I18N.Other.dll*</span><span class="sxs-lookup"><span data-stu-id="61e1c-134">*I18N.Other.dll*</span></span>        |
| `rare`           | <span data-ttu-id="61e1c-135">*I18N.Rare.dll*</span><span class="sxs-lookup"><span data-stu-id="61e1c-135">*I18N.Rare.dll*</span></span>         |
| `west`           | <span data-ttu-id="61e1c-136">*I18N.West.dll*</span><span class="sxs-lookup"><span data-stu-id="61e1c-136">*I18N.West.dll*</span></span>         |

<span data-ttu-id="61e1c-137">Verwenden Sie ein Komma, um mehrere Werte voneinander zu trennen (z. B. `mideast,west`).</span><span class="sxs-lookup"><span data-stu-id="61e1c-137">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="61e1c-138">Weitere Informationen finden Sie unter [I18N: Pnetlib-Framework-Bibliothek für die Internationalisierung (Mono/Mono-Github-Repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span><span class="sxs-lookup"><span data-stu-id="61e1c-138">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>
