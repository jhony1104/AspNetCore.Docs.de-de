---
<span data-ttu-id="a627e-101">title: 'Konfigurieren des Linkers für ASP.NET Core Blazor' author: description: 'Erfahren Sie, wie Sie den IL-Linker (Intermediate Language, Zwischensprache) beim Erstellen einer Blazor-App steuern.'</span><span class="sxs-lookup"><span data-stu-id="a627e-101">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="a627e-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="a627e-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a627e-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a627e-103">'Blazor'</span></span>
- <span data-ttu-id="a627e-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a627e-104">'Identity'</span></span>
- <span data-ttu-id="a627e-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a627e-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="a627e-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a627e-106">'Razor'</span></span>
- <span data-ttu-id="a627e-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="a627e-107">'SignalR' uid:</span></span> 

---
# <a name="configure-the-linker-for-aspnet-core-blazor"></a><span data-ttu-id="a627e-108">Konfigurieren des Linkers für ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="a627e-108">Configure the Linker for ASP.NET Core Blazor</span></span>

<span data-ttu-id="a627e-109">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a627e-109">By [Luke Latham](https://github.com/guardrex)</span></span>

Blazor<span data-ttu-id="a627e-110"> WebAssembly führt bei einem Build eine [IL](/dotnet/standard/managed-code#intermediate-language--execution)-Verknüpfung durch, um nicht benötigte Zwischensprache aus den Ausgabeassemblys der App zu kürzen.</span><span class="sxs-lookup"><span data-stu-id="a627e-110"> WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="a627e-111">Der Linker ist beim Buildvorgang in der Debugkonfiguration deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="a627e-111">The linker is disabled when building in Debug configuration.</span></span> <span data-ttu-id="a627e-112">Apps müssen in der Releasekonfiguration erstellt werden, damit der Linker aktiviert wird.</span><span class="sxs-lookup"><span data-stu-id="a627e-112">Apps must build in Release configuration to enable the linker.</span></span> <span data-ttu-id="a627e-113">Sie sollten die Releasekonfiguration beim Buildvorgang anwenden, wenn Sie Blazor WebAssembly-Apps erstellen.</span><span class="sxs-lookup"><span data-stu-id="a627e-113">We recommend building in Release when deploying your Blazor WebAssembly apps.</span></span> 

<span data-ttu-id="a627e-114">Das Verknüpfen einer App optimiert die Größe, kann jedoch auch negative Auswirkungen haben.</span><span class="sxs-lookup"><span data-stu-id="a627e-114">Linking an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="a627e-115">Apps, die Reflektion oder ähnliche dynamische Features verwenden, können beim Kürzen unterbrochen werden, da der Linker dieses dynamische Verhalten nicht kennt und nicht ermitteln kann, welche Typen für die Reflektion zur Laufzeit erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="a627e-115">Apps that use reflection or related dynamic features may break when trimmed because the linker doesn't know about this dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="a627e-116">Zur Kürzung solcher Apps muss der Linker über alle Typen informiert werden, die für die Reflektion im Code und in Paketen oder Frameworks erforderlich sind, von denen die App abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="a627e-116">To trim such apps, the linker must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span> 

<span data-ttu-id="a627e-117">Wenn Sie sicherstellen möchten, dass die gekürzte App nach der Bereitstellung ordnungsgemäß funktioniert, ist es wichtig, bei der Entwicklung häufig die Releasebuilds der App zu testen.</span><span class="sxs-lookup"><span data-stu-id="a627e-117">To ensure the trimmed app works correctly once deployed, it's important to test Release builds of the app frequently while developing.</span></span>

<span data-ttu-id="a627e-118">Das Verknüpfen von Blazor-Apps kann mithilfe der folgenden MSBuild-Funktionen konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="a627e-118">Linking for Blazor apps can be configured using these MSBuild features:</span></span>

* <span data-ttu-id="a627e-119">Globales Konfigurieren der Verknüpfung mit einer [MSBuild-Eigenschaft](#control-linking-with-an-msbuild-property)</span><span class="sxs-lookup"><span data-stu-id="a627e-119">Configure linking globally with a [MSBuild property](#control-linking-with-an-msbuild-property).</span></span>
* <span data-ttu-id="a627e-120">Steuern der Verknüpfung für unterschiedliche Assemblys mit einer [Konfigurationsdatei](#control-linking-with-a-configuration-file)</span><span class="sxs-lookup"><span data-stu-id="a627e-120">Control linking on a per-assembly basis with a [configuration file](#control-linking-with-a-configuration-file).</span></span>

## <a name="control-linking-with-an-msbuild-property"></a><span data-ttu-id="a627e-121">Steuern der Verknüpfung mit einer MSBuild-Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="a627e-121">Control linking with an MSBuild property</span></span>

<span data-ttu-id="a627e-122">Die Verknüpfung wird aktiviert, wenn eine App in der `Release`-Konfiguration erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="a627e-122">Linking is enabled when an app is built in `Release` configuration.</span></span> <span data-ttu-id="a627e-123">Sie können dieses Verhalten ändern, indem Sie die MSBuild-Eigenschaft `BlazorWebAssemblyEnableLinking` in der Projektdatei konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="a627e-123">To change this, configure the `BlazorWebAssemblyEnableLinking` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyEnableLinking>false</BlazorWebAssemblyEnableLinking>
</PropertyGroup>
```

## <a name="control-linking-with-a-configuration-file"></a><span data-ttu-id="a627e-124">Steuern der Verknüpfung mit einer Konfigurationsdatei.</span><span class="sxs-lookup"><span data-stu-id="a627e-124">Control linking with a configuration file</span></span>

<span data-ttu-id="a627e-125">Sie können die Verknüpfung für unterschiedliche Assemblys steuern, indem Sie eine XML-Konfigurationsdatei bereitstellen und die Datei als MSBuild-Element in der Projektdatei festlegen:</span><span class="sxs-lookup"><span data-stu-id="a627e-125">Control linking on a per-assembly basis by providing an XML configuration file and specifying the file as a MSBuild item in the project file:</span></span>

```xml
<ItemGroup>
  <BlazorLinkerDescriptor Include="LinkerConfig.xml" />
</ItemGroup>
```

<span data-ttu-id="a627e-126">*LinkerConfig.xml:*</span><span class="sxs-lookup"><span data-stu-id="a627e-126">*LinkerConfig.xml*:</span></span>

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

<span data-ttu-id="a627e-127">Weitere Informationen finden Sie unter [Data Formats (GitHub-Repository zu mono/linker)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span><span class="sxs-lookup"><span data-stu-id="a627e-127">For more information and examples, see [Data Formats (mono/linker GitHub repository)](https://github.com/mono/linker/blob/master/docs/data-formats.md).</span></span>

## <a name="add-an-xml-linker-configuration-file-to-a-library"></a><span data-ttu-id="a627e-128">Hinzufügen einer XML-Linkerkonfigurationsdatei zu einer Bibliothek</span><span class="sxs-lookup"><span data-stu-id="a627e-128">Add an XML linker configuration file to a library</span></span>

<span data-ttu-id="a627e-129">Fügen Sie eine XML-Linkerkonfigurationsdatei als eingebettete Ressource zur Bibliothek hinzu, um den Linker für eine spezifische Bibliothek zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="a627e-129">To configure the linker for a specific library, add an XML linker configuration file into the library as an embedded resource.</span></span> <span data-ttu-id="a627e-130">Die eingebettete Ressource muss denselben Namen wie die Assembly aufweisen.</span><span class="sxs-lookup"><span data-stu-id="a627e-130">The embedded resource must have the same name as the assembly.</span></span>

<span data-ttu-id="a627e-131">Im folgenden Beispiel wird die Datei *LinkerConfig.xml* als eingebettete Ressource festgelegt, die denselben Namen wie die Assembly der Bibliothek aufweist:</span><span class="sxs-lookup"><span data-stu-id="a627e-131">In the following example, the *LinkerConfig.xml* file is specified as an embedded resource that has the same name as the library's assembly:</span></span>

```xml
<ItemGroup>
  <EmbeddedResource Include="LinkerConfig.xml">
    <LogicalName>$(MSBuildProjectName).xml</LogicalName>
  </EmbeddedResource>
</ItemGroup>
```

### <a name="configure-the-linker-for-internationalization"></a><span data-ttu-id="a627e-132">Konfigurieren des Linkers für die Internationalisierung</span><span class="sxs-lookup"><span data-stu-id="a627e-132">Configure the linker for internationalization</span></span>

<span data-ttu-id="a627e-133">Die Blazor-Linkerkonfiguration für Blazor-WebAssembly-Apps entfernt standardmäßig Internationalisierungsinformationen, mit Ausnahme von explizit angeforderten Gebietsschemas.</span><span class="sxs-lookup"><span data-stu-id="a627e-133">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="a627e-134">Durch die Entfernung dieser Assemblys wird die Größe der App minimiert.</span><span class="sxs-lookup"><span data-stu-id="a627e-134">Removing these assemblies minimizes the app's size.</span></span>

<span data-ttu-id="a627e-135">Legen Sie die MSBuild-Eigenschaft `<BlazorWebAssemblyI18NAssemblies>` in der Projektdatei fest, um zu steuern, welche I18N-Assemblys beibehalten werden:</span><span class="sxs-lookup"><span data-stu-id="a627e-135">To control which I18N assemblies are retained, set the `<BlazorWebAssemblyI18NAssemblies>` MSBuild property in the project file:</span></span>

```xml
<PropertyGroup>
  <BlazorWebAssemblyI18NAssemblies>{all|none|REGION1,REGION2,...}</BlazorWebAssemblyI18NAssemblies>
</PropertyGroup>
```

| <span data-ttu-id="a627e-136">Regionswert</span><span class="sxs-lookup"><span data-stu-id="a627e-136">Region Value</span></span>     | <span data-ttu-id="a627e-137">Mono-Regionsassembly</span><span class="sxs-lookup"><span data-stu-id="a627e-137">Mono region assembly</span></span>    |
| ---
<span data-ttu-id="a627e-138">title: 'Konfigurieren des Linkers für ASP.NET Core Blazor' author: description: 'Erfahren Sie, wie Sie den IL-Linker (Intermediate Language, Zwischensprache) beim Erstellen einer Blazor-App steuern.'</span><span class="sxs-lookup"><span data-stu-id="a627e-138">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="a627e-139">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="a627e-139">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a627e-140">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a627e-140">'Blazor'</span></span>
- <span data-ttu-id="a627e-141">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a627e-141">'Identity'</span></span>
- <span data-ttu-id="a627e-142">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a627e-142">'Let's Encrypt'</span></span>
- <span data-ttu-id="a627e-143">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a627e-143">'Razor'</span></span>
- <span data-ttu-id="a627e-144">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="a627e-144">'SignalR' uid:</span></span> 

-
<span data-ttu-id="a627e-145">title: 'Konfigurieren des Linkers für ASP.NET Core Blazor' author: description: 'Erfahren Sie, wie Sie den IL-Linker (Intermediate Language, Zwischensprache) beim Erstellen einer Blazor-App steuern.'</span><span class="sxs-lookup"><span data-stu-id="a627e-145">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="a627e-146">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="a627e-146">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a627e-147">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a627e-147">'Blazor'</span></span>
- <span data-ttu-id="a627e-148">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a627e-148">'Identity'</span></span>
- <span data-ttu-id="a627e-149">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a627e-149">'Let's Encrypt'</span></span>
- <span data-ttu-id="a627e-150">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a627e-150">'Razor'</span></span>
- <span data-ttu-id="a627e-151">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="a627e-151">'SignalR' uid:</span></span> 

-
<span data-ttu-id="a627e-152">title: 'Konfigurieren des Linkers für ASP.NET Core Blazor' author: description: 'Erfahren Sie, wie Sie den IL-Linker (Intermediate Language, Zwischensprache) beim Erstellen einer Blazor-App steuern.'</span><span class="sxs-lookup"><span data-stu-id="a627e-152">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="a627e-153">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="a627e-153">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a627e-154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a627e-154">'Blazor'</span></span>
- <span data-ttu-id="a627e-155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a627e-155">'Identity'</span></span>
- <span data-ttu-id="a627e-156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a627e-156">'Let's Encrypt'</span></span>
- <span data-ttu-id="a627e-157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a627e-157">'Razor'</span></span>
- <span data-ttu-id="a627e-158">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="a627e-158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="a627e-159">title: 'Konfigurieren des Linkers für ASP.NET Core Blazor' author: description: 'Erfahren Sie, wie Sie den IL-Linker (Intermediate Language, Zwischensprache) beim Erstellen einer Blazor-App steuern.'</span><span class="sxs-lookup"><span data-stu-id="a627e-159">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="a627e-160">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="a627e-160">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a627e-161">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a627e-161">'Blazor'</span></span>
- <span data-ttu-id="a627e-162">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a627e-162">'Identity'</span></span>
- <span data-ttu-id="a627e-163">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a627e-163">'Let's Encrypt'</span></span>
- <span data-ttu-id="a627e-164">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a627e-164">'Razor'</span></span>
- <span data-ttu-id="a627e-165">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="a627e-165">'SignalR' uid:</span></span> 

-
<span data-ttu-id="a627e-166">title: 'Konfigurieren des Linkers für ASP.NET Core Blazor' author: description: 'Erfahren Sie, wie Sie den IL-Linker (Intermediate Language, Zwischensprache) beim Erstellen einer Blazor-App steuern.'</span><span class="sxs-lookup"><span data-stu-id="a627e-166">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="a627e-167">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="a627e-167">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a627e-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a627e-168">'Blazor'</span></span>
- <span data-ttu-id="a627e-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a627e-169">'Identity'</span></span>
- <span data-ttu-id="a627e-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a627e-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="a627e-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a627e-171">'Razor'</span></span>
- <span data-ttu-id="a627e-172">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="a627e-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="a627e-173">title: 'Konfigurieren des Linkers für ASP.NET Core Blazor' author: description: 'Erfahren Sie, wie Sie den IL-Linker (Intermediate Language, Zwischensprache) beim Erstellen einer Blazor-App steuern.'</span><span class="sxs-lookup"><span data-stu-id="a627e-173">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="a627e-174">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="a627e-174">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a627e-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a627e-175">'Blazor'</span></span>
- <span data-ttu-id="a627e-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a627e-176">'Identity'</span></span>
- <span data-ttu-id="a627e-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a627e-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="a627e-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a627e-178">'Razor'</span></span>
- <span data-ttu-id="a627e-179">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="a627e-179">'SignalR' uid:</span></span> 

<span data-ttu-id="a627e-180">-------- | --- title: 'Konfigurieren des Linkers für ASP.NET Core Blazor' author: description: 'Erfahren Sie, wie Sie den IL-Linker (Intermediate Language, Zwischensprache) beim Erstellen einer Blazor-App steuern.'</span><span class="sxs-lookup"><span data-stu-id="a627e-180">-------- | --- title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="a627e-181">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="a627e-181">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a627e-182">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a627e-182">'Blazor'</span></span>
- <span data-ttu-id="a627e-183">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a627e-183">'Identity'</span></span>
- <span data-ttu-id="a627e-184">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a627e-184">'Let's Encrypt'</span></span>
- <span data-ttu-id="a627e-185">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a627e-185">'Razor'</span></span>
- <span data-ttu-id="a627e-186">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="a627e-186">'SignalR' uid:</span></span> 

-
<span data-ttu-id="a627e-187">title: 'Konfigurieren des Linkers für ASP.NET Core Blazor' author: description: 'Erfahren Sie, wie Sie den IL-Linker (Intermediate Language, Zwischensprache) beim Erstellen einer Blazor-App steuern.'</span><span class="sxs-lookup"><span data-stu-id="a627e-187">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="a627e-188">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="a627e-188">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a627e-189">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a627e-189">'Blazor'</span></span>
- <span data-ttu-id="a627e-190">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a627e-190">'Identity'</span></span>
- <span data-ttu-id="a627e-191">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a627e-191">'Let's Encrypt'</span></span>
- <span data-ttu-id="a627e-192">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a627e-192">'Razor'</span></span>
- <span data-ttu-id="a627e-193">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="a627e-193">'SignalR' uid:</span></span> 

-
<span data-ttu-id="a627e-194">title: 'Konfigurieren des Linkers für ASP.NET Core Blazor' author: description: 'Erfahren Sie, wie Sie den IL-Linker (Intermediate Language, Zwischensprache) beim Erstellen einer Blazor-App steuern.'</span><span class="sxs-lookup"><span data-stu-id="a627e-194">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="a627e-195">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="a627e-195">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a627e-196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a627e-196">'Blazor'</span></span>
- <span data-ttu-id="a627e-197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a627e-197">'Identity'</span></span>
- <span data-ttu-id="a627e-198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a627e-198">'Let's Encrypt'</span></span>
- <span data-ttu-id="a627e-199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a627e-199">'Razor'</span></span>
- <span data-ttu-id="a627e-200">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="a627e-200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="a627e-201">title: 'Konfigurieren des Linkers für ASP.NET Core Blazor' author: description: 'Erfahren Sie, wie Sie den IL-Linker (Intermediate Language, Zwischensprache) beim Erstellen einer Blazor-App steuern.'</span><span class="sxs-lookup"><span data-stu-id="a627e-201">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="a627e-202">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="a627e-202">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a627e-203">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a627e-203">'Blazor'</span></span>
- <span data-ttu-id="a627e-204">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a627e-204">'Identity'</span></span>
- <span data-ttu-id="a627e-205">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a627e-205">'Let's Encrypt'</span></span>
- <span data-ttu-id="a627e-206">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a627e-206">'Razor'</span></span>
- <span data-ttu-id="a627e-207">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="a627e-207">'SignalR' uid:</span></span> 

-
<span data-ttu-id="a627e-208">title: 'Konfigurieren des Linkers für ASP.NET Core Blazor' author: description: 'Erfahren Sie, wie Sie den IL-Linker (Intermediate Language, Zwischensprache) beim Erstellen einer Blazor-App steuern.'</span><span class="sxs-lookup"><span data-stu-id="a627e-208">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="a627e-209">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="a627e-209">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a627e-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a627e-210">'Blazor'</span></span>
- <span data-ttu-id="a627e-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a627e-211">'Identity'</span></span>
- <span data-ttu-id="a627e-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a627e-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="a627e-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a627e-213">'Razor'</span></span>
- <span data-ttu-id="a627e-214">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="a627e-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="a627e-215">title: 'Konfigurieren des Linkers für ASP.NET Core Blazor' author: description: 'Erfahren Sie, wie Sie den IL-Linker (Intermediate Language, Zwischensprache) beim Erstellen einer Blazor-App steuern.'</span><span class="sxs-lookup"><span data-stu-id="a627e-215">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="a627e-216">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="a627e-216">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a627e-217">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a627e-217">'Blazor'</span></span>
- <span data-ttu-id="a627e-218">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a627e-218">'Identity'</span></span>
- <span data-ttu-id="a627e-219">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a627e-219">'Let's Encrypt'</span></span>
- <span data-ttu-id="a627e-220">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a627e-220">'Razor'</span></span>
- <span data-ttu-id="a627e-221">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="a627e-221">'SignalR' uid:</span></span> 

-
<span data-ttu-id="a627e-222">title: 'Konfigurieren des Linkers für ASP.NET Core Blazor' author: description: 'Erfahren Sie, wie Sie den IL-Linker (Intermediate Language, Zwischensprache) beim Erstellen einer Blazor-App steuern.'</span><span class="sxs-lookup"><span data-stu-id="a627e-222">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="a627e-223">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="a627e-223">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a627e-224">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a627e-224">'Blazor'</span></span>
- <span data-ttu-id="a627e-225">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a627e-225">'Identity'</span></span>
- <span data-ttu-id="a627e-226">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a627e-226">'Let's Encrypt'</span></span>
- <span data-ttu-id="a627e-227">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a627e-227">'Razor'</span></span>
- <span data-ttu-id="a627e-228">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="a627e-228">'SignalR' uid:</span></span> 

-
<span data-ttu-id="a627e-229">title: 'Konfigurieren des Linkers für ASP.NET Core Blazor' author: description: 'Erfahren Sie, wie Sie den IL-Linker (Intermediate Language, Zwischensprache) beim Erstellen einer Blazor-App steuern.'</span><span class="sxs-lookup"><span data-stu-id="a627e-229">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="a627e-230">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="a627e-230">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a627e-231">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a627e-231">'Blazor'</span></span>
- <span data-ttu-id="a627e-232">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a627e-232">'Identity'</span></span>
- <span data-ttu-id="a627e-233">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a627e-233">'Let's Encrypt'</span></span>
- <span data-ttu-id="a627e-234">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a627e-234">'Razor'</span></span>
- <span data-ttu-id="a627e-235">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="a627e-235">'SignalR' uid:</span></span> 

-
<span data-ttu-id="a627e-236">title: 'Konfigurieren des Linkers für ASP.NET Core Blazor' author: description: 'Erfahren Sie, wie Sie den IL-Linker (Intermediate Language, Zwischensprache) beim Erstellen einer Blazor-App steuern.'</span><span class="sxs-lookup"><span data-stu-id="a627e-236">title: 'Configure the Linker for ASP.NET Core Blazor' author: description: 'Learn how to control the Intermediate Language (IL) Linker when building a Blazor app.'</span></span>
<span data-ttu-id="a627e-237">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="a627e-237">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="a627e-238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a627e-238">'Blazor'</span></span>
- <span data-ttu-id="a627e-239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a627e-239">'Identity'</span></span>
- <span data-ttu-id="a627e-240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a627e-240">'Let's Encrypt'</span></span>
- <span data-ttu-id="a627e-241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a627e-241">'Razor'</span></span>
- <span data-ttu-id="a627e-242">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="a627e-242">'SignalR' uid:</span></span> 

<span data-ttu-id="a627e-243">------------ | | `all`            | Alle Assemblys eingeschlossen | | `cjk`            | *I18N.CJK.dll*          | | `mideast`        | *I18N.MidEast.dll*      | | `none` (Standard) | Keine                    | | `other`          | *I18N.Other.dll*        | | `rare`           | *I18N.Rare.dll*         | | `west`           | *I18N.West.dll*         |</span><span class="sxs-lookup"><span data-stu-id="a627e-243">------------ | | `all`            | All assemblies included | | `cjk`            | *I18N.CJK.dll*          | | `mideast`        | *I18N.MidEast.dll*      | | `none` (default) | None                    | | `other`          | *I18N.Other.dll*        | | `rare`           | *I18N.Rare.dll*         | | `west`           | *I18N.West.dll*         |</span></span>

<span data-ttu-id="a627e-244">Verwenden Sie ein Komma, um mehrere Werte voneinander zu trennen (z. B. `mideast,west`).</span><span class="sxs-lookup"><span data-stu-id="a627e-244">Use a comma to separate multiple values (for example, `mideast,west`).</span></span>

<span data-ttu-id="a627e-245">Weitere Informationen finden Sie unter [I18N: Pnetlib-Framework-Bibliothek für die Internationalisierung (Mono/Mono-Github-Repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span><span class="sxs-lookup"><span data-stu-id="a627e-245">For more information, see [I18N: Pnetlib Internationalization Framework Library (mono/mono GitHub repository)](https://github.com/mono/mono/tree/master/mcs/class/I18N).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a627e-246">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a627e-246">Additional resources</span></span>

* <xref:performance/blazor/webassembly-best-practices#intermediate-language-il-linking>
