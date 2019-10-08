---
title: Entwickeln von ASP.NET Core-Apps mit OpenAPI
author: ryanbrandenburg
description: Hier wird veranschaulicht, wie Sie das Tool „Microsoft.dotnet-openapi“ verwenden, um Verweise zu OpenAPI-Dateien hinzuzufügen.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: f5eae9e871bc8efc30d500769adb845ff244a90c
ms.sourcegitcommit: e644258c95dd50a82284f107b9bf3becbc43b2b2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71317777"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="738c7-103">Entwickeln von ASP.NET Core-Apps mit OpenAPI-Tools</span><span class="sxs-lookup"><span data-stu-id="738c7-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="738c7-104">Von Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="738c7-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="738c7-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) ist ein [globales .NET Core-Tool](/dotnet/core/tools/global-tools) zum Verwalten von [OpenAPI](https://github.com/OAI/OpenAPI-Specification)-Verweisen innerhalb eines Projekts.</span><span class="sxs-lookup"><span data-stu-id="738c7-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="738c7-106">Installation</span><span class="sxs-lookup"><span data-stu-id="738c7-106">Installation</span></span>

<span data-ttu-id="738c7-107">Führen Sie den folgenden Befehl aus, um `Microsoft.dotnet-openapi` zu installieren:</span><span class="sxs-lookup"><span data-stu-id="738c7-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="738c7-108">Hinzufügen</span><span class="sxs-lookup"><span data-stu-id="738c7-108">Add</span></span>

<span data-ttu-id="738c7-109">Wenn Sie mit einem der Befehle auf dieser Seite einen OpenAPI-Verweis hinzufügen, wird ein `<OpenApiReference />`-Element ähnlich dem folgenden zur *CSPROJ*-Datei hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="738c7-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="738c7-110">Der oben genannte Verweis ist erforderlich, damit die App den generierten Clientcode aufrufen kann.</span><span class="sxs-lookup"><span data-stu-id="738c7-110">The preceding reference is required for the app to call the generated client code.</span></span>

<!-- TODO: Restore after https://github.com/aspnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -v|--verbose | Show verbose output. |dotnet openapi add project *-v* ../Ref/ProjRef.csproj |
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a><span data-ttu-id="738c7-111">Datei hinzufügen</span><span class="sxs-lookup"><span data-stu-id="738c7-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="738c7-112">Optionen</span><span class="sxs-lookup"><span data-stu-id="738c7-112">Options</span></span>

| <span data-ttu-id="738c7-113">Kurze Option</span><span class="sxs-lookup"><span data-stu-id="738c7-113">Short option</span></span>| <span data-ttu-id="738c7-114">Lange Option</span><span class="sxs-lookup"><span data-stu-id="738c7-114">Long option</span></span>| <span data-ttu-id="738c7-115">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="738c7-115">Description</span></span> | <span data-ttu-id="738c7-116">Beispiel</span><span class="sxs-lookup"><span data-stu-id="738c7-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="738c7-117">-v</span><span class="sxs-lookup"><span data-stu-id="738c7-117">-v</span></span>|<span data-ttu-id="738c7-118">--verbose</span><span class="sxs-lookup"><span data-stu-id="738c7-118">--verbose</span></span> | <span data-ttu-id="738c7-119">Zeigt eine ausführliche Ausgabe an.</span><span class="sxs-lookup"><span data-stu-id="738c7-119">Show verbose output.</span></span> |<span data-ttu-id="738c7-120">dotnet openapi add file *-v* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="738c7-120">dotnet openapi add file *-v* .\OpenAPI.json</span></span> |
| <span data-ttu-id="738c7-121">-p</span><span class="sxs-lookup"><span data-stu-id="738c7-121">-p</span></span>|<span data-ttu-id="738c7-122">--updateProject</span><span class="sxs-lookup"><span data-stu-id="738c7-122">--updateProject</span></span> | <span data-ttu-id="738c7-123">Das Projekt, das bearbeitet werden soll.</span><span class="sxs-lookup"><span data-stu-id="738c7-123">The project to operate on.</span></span> |<span data-ttu-id="738c7-124">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="738c7-124">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="738c7-125">-c</span><span class="sxs-lookup"><span data-stu-id="738c7-125">-c</span></span>|<span data-ttu-id="738c7-126">--code-generator</span><span class="sxs-lookup"><span data-stu-id="738c7-126">--code-generator</span></span>| <span data-ttu-id="738c7-127">Der Codegenerator, der auf den Verweis angewendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="738c7-127">The code generator to apply to the reference.</span></span> <span data-ttu-id="738c7-128">Die Optionen sind `NSwagCSharp` und `NSwagTypeScript`.</span><span class="sxs-lookup"><span data-stu-id="738c7-128">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="738c7-129">Wenn `--code-generator` nicht angegeben ist, werden standardmäßig `NSwagCSharp`-Tools verwendet.</span><span class="sxs-lookup"><span data-stu-id="738c7-129">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="738c7-130">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="738c7-130">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="738c7-131">-h</span><span class="sxs-lookup"><span data-stu-id="738c7-131">-h</span></span>|<span data-ttu-id="738c7-132">--help</span><span class="sxs-lookup"><span data-stu-id="738c7-132">--help</span></span>|<span data-ttu-id="738c7-133">Zeigt Hilfeinformationen an.</span><span class="sxs-lookup"><span data-stu-id="738c7-133">Show help information</span></span>|<span data-ttu-id="738c7-134">dotnet openapi add file --help</span><span class="sxs-lookup"><span data-stu-id="738c7-134">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="738c7-135">Argumente</span><span class="sxs-lookup"><span data-stu-id="738c7-135">Arguments</span></span>

|  <span data-ttu-id="738c7-136">Argument</span><span class="sxs-lookup"><span data-stu-id="738c7-136">Argument</span></span>  | <span data-ttu-id="738c7-137">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="738c7-137">Description</span></span> | <span data-ttu-id="738c7-138">Beispiel</span><span class="sxs-lookup"><span data-stu-id="738c7-138">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="738c7-139">source-file</span><span class="sxs-lookup"><span data-stu-id="738c7-139">source-file</span></span> | <span data-ttu-id="738c7-140">Die Quelle, aus der ein Verweis erstellt werden soll.</span><span class="sxs-lookup"><span data-stu-id="738c7-140">The source to create a reference from.</span></span> <span data-ttu-id="738c7-141">Es muss sich um eine OpenAPI-Datei handeln.</span><span class="sxs-lookup"><span data-stu-id="738c7-141">Must be an OpenAPI file.</span></span> |<span data-ttu-id="738c7-142">dotnet openapi add file *.\OpenAPI.json*</span><span class="sxs-lookup"><span data-stu-id="738c7-142">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="738c7-143">URL hinzufügen</span><span class="sxs-lookup"><span data-stu-id="738c7-143">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="738c7-144">Optionen</span><span class="sxs-lookup"><span data-stu-id="738c7-144">Options</span></span>

| <span data-ttu-id="738c7-145">Kurze Option</span><span class="sxs-lookup"><span data-stu-id="738c7-145">Short option</span></span>| <span data-ttu-id="738c7-146">Lange Option</span><span class="sxs-lookup"><span data-stu-id="738c7-146">Long option</span></span>| <span data-ttu-id="738c7-147">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="738c7-147">Description</span></span> | <span data-ttu-id="738c7-148">Beispiel</span><span class="sxs-lookup"><span data-stu-id="738c7-148">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="738c7-149">-v</span><span class="sxs-lookup"><span data-stu-id="738c7-149">-v</span></span>|<span data-ttu-id="738c7-150">--verbose</span><span class="sxs-lookup"><span data-stu-id="738c7-150">--verbose</span></span> | <span data-ttu-id="738c7-151">Zeigt eine ausführliche Ausgabe an.</span><span class="sxs-lookup"><span data-stu-id="738c7-151">Show verbose output.</span></span> |<span data-ttu-id="738c7-152">dotnet openapi add url *-v* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="738c7-152">dotnet openapi add url *-v* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="738c7-153">-p</span><span class="sxs-lookup"><span data-stu-id="738c7-153">-p</span></span>|<span data-ttu-id="738c7-154">--updateProject</span><span class="sxs-lookup"><span data-stu-id="738c7-154">--updateProject</span></span> | <span data-ttu-id="738c7-155">Das Projekt, das bearbeitet werden soll.</span><span class="sxs-lookup"><span data-stu-id="738c7-155">The project to operate on.</span></span> |<span data-ttu-id="738c7-156">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="738c7-156">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="738c7-157">-o</span><span class="sxs-lookup"><span data-stu-id="738c7-157">-o</span></span>|<span data-ttu-id="738c7-158">--output-file</span><span class="sxs-lookup"><span data-stu-id="738c7-158">--output-file</span></span> | <span data-ttu-id="738c7-159">Speicherort für die lokale Kopie der OpenAPI-Datei.</span><span class="sxs-lookup"><span data-stu-id="738c7-159">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="738c7-160">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span><span class="sxs-lookup"><span data-stu-id="738c7-160">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="738c7-161">-c</span><span class="sxs-lookup"><span data-stu-id="738c7-161">-c</span></span>|<span data-ttu-id="738c7-162">--code-generator</span><span class="sxs-lookup"><span data-stu-id="738c7-162">--code-generator</span></span>| <span data-ttu-id="738c7-163">Der Codegenerator, der auf den Verweis angewendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="738c7-163">The code generator to apply to the reference.</span></span> <span data-ttu-id="738c7-164">Die Optionen sind `NSwagCSharp` und `NSwagTypeScript`.</span><span class="sxs-lookup"><span data-stu-id="738c7-164">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="738c7-165">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="738c7-165">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="738c7-166">-h</span><span class="sxs-lookup"><span data-stu-id="738c7-166">-h</span></span>|<span data-ttu-id="738c7-167">--help</span><span class="sxs-lookup"><span data-stu-id="738c7-167">--help</span></span>|<span data-ttu-id="738c7-168">Zeigt Hilfeinformationen an.</span><span class="sxs-lookup"><span data-stu-id="738c7-168">Show help information</span></span>|<span data-ttu-id="738c7-169">dotnet openapi add url --help</span><span class="sxs-lookup"><span data-stu-id="738c7-169">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="738c7-170">Argumente</span><span class="sxs-lookup"><span data-stu-id="738c7-170">Arguments</span></span>

|  <span data-ttu-id="738c7-171">Argument</span><span class="sxs-lookup"><span data-stu-id="738c7-171">Argument</span></span>  | <span data-ttu-id="738c7-172">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="738c7-172">Description</span></span> | <span data-ttu-id="738c7-173">Beispiel</span><span class="sxs-lookup"><span data-stu-id="738c7-173">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="738c7-174">source-URL</span><span class="sxs-lookup"><span data-stu-id="738c7-174">source-URL</span></span> | <span data-ttu-id="738c7-175">Die Quelle, aus der ein Verweis erstellt werden soll.</span><span class="sxs-lookup"><span data-stu-id="738c7-175">The source to create a reference from.</span></span> <span data-ttu-id="738c7-176">Es muss sich um eine URL handeln.</span><span class="sxs-lookup"><span data-stu-id="738c7-176">Must be a URL.</span></span> |<span data-ttu-id="738c7-177">dotnet openapi add url `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="738c7-177">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="738c7-178">Remove</span><span class="sxs-lookup"><span data-stu-id="738c7-178">Remove</span></span>

<span data-ttu-id="738c7-179">Entfernt den OpenAPI-Verweis, der mit dem angegebenen Dateinamen übereinstimmt, aus der *CSPROJ*-Datei.</span><span class="sxs-lookup"><span data-stu-id="738c7-179">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="738c7-180">Wenn der OpenAPI-Verweis entfernt wird, werden keine Clients generiert.</span><span class="sxs-lookup"><span data-stu-id="738c7-180">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="738c7-181">Lokale *JSON*- und *YAML*-Dateien werden gelöscht.</span><span class="sxs-lookup"><span data-stu-id="738c7-181">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="738c7-182">Optionen</span><span class="sxs-lookup"><span data-stu-id="738c7-182">Options</span></span>

| <span data-ttu-id="738c7-183">Kurze Option</span><span class="sxs-lookup"><span data-stu-id="738c7-183">Short option</span></span>| <span data-ttu-id="738c7-184">Lange Option</span><span class="sxs-lookup"><span data-stu-id="738c7-184">Long option</span></span>| <span data-ttu-id="738c7-185">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="738c7-185">Description</span></span>| <span data-ttu-id="738c7-186">Beispiel</span><span class="sxs-lookup"><span data-stu-id="738c7-186">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="738c7-187">-v</span><span class="sxs-lookup"><span data-stu-id="738c7-187">-v</span></span>|<span data-ttu-id="738c7-188">--verbose</span><span class="sxs-lookup"><span data-stu-id="738c7-188">--verbose</span></span> | <span data-ttu-id="738c7-189">Zeigt eine ausführliche Ausgabe an.</span><span class="sxs-lookup"><span data-stu-id="738c7-189">Show verbose output.</span></span> |<span data-ttu-id="738c7-190">dotnet openapi remove *-v*</span><span class="sxs-lookup"><span data-stu-id="738c7-190">dotnet openapi remove *-v*</span></span>|
| <span data-ttu-id="738c7-191">-p</span><span class="sxs-lookup"><span data-stu-id="738c7-191">-p</span></span>|<span data-ttu-id="738c7-192">--updateProject</span><span class="sxs-lookup"><span data-stu-id="738c7-192">--updateProject</span></span> | <span data-ttu-id="738c7-193">Das Projekt, das bearbeitet werden soll.</span><span class="sxs-lookup"><span data-stu-id="738c7-193">The project to operate on.</span></span> |<span data-ttu-id="738c7-194">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="738c7-194">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="738c7-195">-h</span><span class="sxs-lookup"><span data-stu-id="738c7-195">-h</span></span>|<span data-ttu-id="738c7-196">--help</span><span class="sxs-lookup"><span data-stu-id="738c7-196">--help</span></span>|<span data-ttu-id="738c7-197">Zeigt Hilfeinformationen an.</span><span class="sxs-lookup"><span data-stu-id="738c7-197">Show help information</span></span>|<span data-ttu-id="738c7-198">dotnet openapi remove --help</span><span class="sxs-lookup"><span data-stu-id="738c7-198">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="738c7-199">Argumente</span><span class="sxs-lookup"><span data-stu-id="738c7-199">Arguments</span></span>

|  <span data-ttu-id="738c7-200">Argument</span><span class="sxs-lookup"><span data-stu-id="738c7-200">Argument</span></span>  | <span data-ttu-id="738c7-201">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="738c7-201">Description</span></span>| <span data-ttu-id="738c7-202">Beispiel</span><span class="sxs-lookup"><span data-stu-id="738c7-202">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="738c7-203">source-file</span><span class="sxs-lookup"><span data-stu-id="738c7-203">source-file</span></span> | <span data-ttu-id="738c7-204">Die Quelle, aus der der Verweis entfernt werden soll.</span><span class="sxs-lookup"><span data-stu-id="738c7-204">The source to remove the reference to.</span></span> |<span data-ttu-id="738c7-205">dotnet openapi remove *.\OpenAPI.json*</span><span class="sxs-lookup"><span data-stu-id="738c7-205">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="738c7-206">Aktualisieren</span><span class="sxs-lookup"><span data-stu-id="738c7-206">Refresh</span></span>

<span data-ttu-id="738c7-207">Aktualisiert die lokale Version einer Datei, die unter Verwendung der neuesten Download-URL heruntergeladen wurde.</span><span class="sxs-lookup"><span data-stu-id="738c7-207">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="738c7-208">Optionen</span><span class="sxs-lookup"><span data-stu-id="738c7-208">Options</span></span>

| <span data-ttu-id="738c7-209">Kurze Option</span><span class="sxs-lookup"><span data-stu-id="738c7-209">Short option</span></span>| <span data-ttu-id="738c7-210">Lange Option</span><span class="sxs-lookup"><span data-stu-id="738c7-210">Long option</span></span>| <span data-ttu-id="738c7-211">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="738c7-211">Description</span></span> | <span data-ttu-id="738c7-212">Beispiel</span><span class="sxs-lookup"><span data-stu-id="738c7-212">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="738c7-213">-v</span><span class="sxs-lookup"><span data-stu-id="738c7-213">-v</span></span>|<span data-ttu-id="738c7-214">--verbose</span><span class="sxs-lookup"><span data-stu-id="738c7-214">--verbose</span></span> | <span data-ttu-id="738c7-215">Zeigt eine ausführliche Ausgabe an.</span><span class="sxs-lookup"><span data-stu-id="738c7-215">Show verbose output.</span></span> | <span data-ttu-id="738c7-216">dotnet openapi refresh *-v* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="738c7-216">dotnet openapi refresh *-v* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="738c7-217">-p</span><span class="sxs-lookup"><span data-stu-id="738c7-217">-p</span></span>|<span data-ttu-id="738c7-218">--updateProject</span><span class="sxs-lookup"><span data-stu-id="738c7-218">--updateProject</span></span> | <span data-ttu-id="738c7-219">Das Projekt, das bearbeitet werden soll.</span><span class="sxs-lookup"><span data-stu-id="738c7-219">The project to operate on.</span></span> | <span data-ttu-id="738c7-220">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="738c7-220">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="738c7-221">-h</span><span class="sxs-lookup"><span data-stu-id="738c7-221">-h</span></span>|<span data-ttu-id="738c7-222">--help</span><span class="sxs-lookup"><span data-stu-id="738c7-222">--help</span></span>|<span data-ttu-id="738c7-223">Zeigt Hilfeinformationen an.</span><span class="sxs-lookup"><span data-stu-id="738c7-223">Show help information</span></span>|<span data-ttu-id="738c7-224">dotnet openapi refresh --help</span><span class="sxs-lookup"><span data-stu-id="738c7-224">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="738c7-225">Argumente</span><span class="sxs-lookup"><span data-stu-id="738c7-225">Arguments</span></span>

|  <span data-ttu-id="738c7-226">Argument</span><span class="sxs-lookup"><span data-stu-id="738c7-226">Argument</span></span>  | <span data-ttu-id="738c7-227">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="738c7-227">Description</span></span> | <span data-ttu-id="738c7-228">Beispiel</span><span class="sxs-lookup"><span data-stu-id="738c7-228">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="738c7-229">source-URL</span><span class="sxs-lookup"><span data-stu-id="738c7-229">source-URL</span></span> | <span data-ttu-id="738c7-230">Die URL, aus der der Verweis aktualisiert werden soll.</span><span class="sxs-lookup"><span data-stu-id="738c7-230">The URL to refresh the reference from.</span></span> | <span data-ttu-id="738c7-231">dotnet openapi refresh `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="738c7-231">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
