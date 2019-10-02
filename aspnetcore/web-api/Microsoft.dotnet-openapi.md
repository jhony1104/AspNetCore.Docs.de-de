---
title: Entwickeln von ASP.NET Core-Apps mit OpenAPI
author: ryanbrandenburg
description: Hier wird veranschaulicht, wie Sie das Tool „Microsoft.dotnet-openapi“ verwenden, um Verweise zu OpenAPI-Dateien hinzuzufügen.
ms.author: rybrande
ms.date: 08/26/2019
monikerRange: '>= aspnetcore-3.0'
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: a9b38bb7e69744d72867bf69cecf1fa92d7c15b3
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187365"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="76844-103">Entwickeln von ASP.NET Core-Apps mit OpenAPI-Tools</span><span class="sxs-lookup"><span data-stu-id="76844-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="76844-104">Von Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="76844-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="76844-105">`Microsoft.dotnet-openapi` ist ein globales .NET Core-Tool zum Verwalten von [OpenAPI](https://github.com/OAI/OpenAPI-Specification)-Verweisen innerhalb eines Projekts.</span><span class="sxs-lookup"><span data-stu-id="76844-105">`Microsoft.dotnet-openapi` is a .NET Core global tool for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="76844-106">Installation</span><span class="sxs-lookup"><span data-stu-id="76844-106">Installation</span></span>

<span data-ttu-id="76844-107">Führen Sie den folgenden Befehl aus, um `Microsoft.dotnet-openapi` zu installieren:</span><span class="sxs-lookup"><span data-stu-id="76844-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```console
dotnet tool install -g Microsoft.dotnet-openapi
```

<span data-ttu-id="76844-108">`Microsoft.dotnet-openapi` ist ein [globales .NET Core-Tool](/dotnet/core/tools/global-tools).</span><span class="sxs-lookup"><span data-stu-id="76844-108">`Microsoft.dotnet-openapi` is a [.NET Core Global Tool](/dotnet/core/tools/global-tools).</span></span>

## <a name="add"></a><span data-ttu-id="76844-109">Hinzufügen</span><span class="sxs-lookup"><span data-stu-id="76844-109">Add</span></span>

<span data-ttu-id="76844-110">Wenn Sie mit einem der Befehle auf dieser Seite einen OpenAPI-Verweis hinzufügen, wird ein `<OpenApiReference />`-Element ähnlich dem folgenden zur *CSPROJ*-Datei hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="76844-110">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />`  element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="76844-111">Der oben genannte Verweis ist erforderlich, damit die App den generierten Clientcode aufrufen kann.</span><span class="sxs-lookup"><span data-stu-id="76844-111">The preceding reference is required for the app to call the generated client code.</span></span>

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

### <a name="add-file"></a><span data-ttu-id="76844-112">Datei hinzufügen</span><span class="sxs-lookup"><span data-stu-id="76844-112">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="76844-113">Optionen</span><span class="sxs-lookup"><span data-stu-id="76844-113">Options</span></span>

| <span data-ttu-id="76844-114">Kurze Option</span><span class="sxs-lookup"><span data-stu-id="76844-114">Short option</span></span>| <span data-ttu-id="76844-115">Lange Option</span><span class="sxs-lookup"><span data-stu-id="76844-115">Long option</span></span>| <span data-ttu-id="76844-116">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="76844-116">Description</span></span> | <span data-ttu-id="76844-117">Beispiel</span><span class="sxs-lookup"><span data-stu-id="76844-117">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="76844-118">-v</span><span class="sxs-lookup"><span data-stu-id="76844-118">-v</span></span>|<span data-ttu-id="76844-119">--verbose</span><span class="sxs-lookup"><span data-stu-id="76844-119">--verbose</span></span> | <span data-ttu-id="76844-120">Zeigt eine ausführliche Ausgabe an.</span><span class="sxs-lookup"><span data-stu-id="76844-120">Show verbose output.</span></span> |<span data-ttu-id="76844-121">dotnet openapi add file *-v* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="76844-121">dotnet openapi add file *-v* .\OpenAPI.json</span></span> |
| <span data-ttu-id="76844-122">-p</span><span class="sxs-lookup"><span data-stu-id="76844-122">-p</span></span>|<span data-ttu-id="76844-123">--updateProject</span><span class="sxs-lookup"><span data-stu-id="76844-123">--updateProject</span></span> | <span data-ttu-id="76844-124">Das Projekt, das bearbeitet werden soll.</span><span class="sxs-lookup"><span data-stu-id="76844-124">The project to operate on.</span></span> |<span data-ttu-id="76844-125">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="76844-125">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="76844-126">-c</span><span class="sxs-lookup"><span data-stu-id="76844-126">-c</span></span>|<span data-ttu-id="76844-127">--code-generator</span><span class="sxs-lookup"><span data-stu-id="76844-127">--code-generator</span></span>| <span data-ttu-id="76844-128">Der Codegenerator, der auf den Verweis angewendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="76844-128">The code generator to apply to the reference.</span></span> <span data-ttu-id="76844-129">Die Optionen sind `NSwagCSharp` und `NSwagTypeScript`.</span><span class="sxs-lookup"><span data-stu-id="76844-129">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="76844-130">Wenn `--code-generator` nicht angegeben ist, werden standardmäßig `NSwagCSharp`-Tools verwendet.</span><span class="sxs-lookup"><span data-stu-id="76844-130">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="76844-131">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="76844-131">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="76844-132">-h</span><span class="sxs-lookup"><span data-stu-id="76844-132">-h</span></span>|<span data-ttu-id="76844-133">--help</span><span class="sxs-lookup"><span data-stu-id="76844-133">--help</span></span>|<span data-ttu-id="76844-134">Zeigt Hilfeinformationen an.</span><span class="sxs-lookup"><span data-stu-id="76844-134">Show help information</span></span>|<span data-ttu-id="76844-135">dotnet openapi add file --help</span><span class="sxs-lookup"><span data-stu-id="76844-135">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="76844-136">Argumente</span><span class="sxs-lookup"><span data-stu-id="76844-136">Arguments</span></span>

|  <span data-ttu-id="76844-137">Argument</span><span class="sxs-lookup"><span data-stu-id="76844-137">Argument</span></span>  | <span data-ttu-id="76844-138">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="76844-138">Description</span></span> | <span data-ttu-id="76844-139">Beispiel</span><span class="sxs-lookup"><span data-stu-id="76844-139">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="76844-140">source-file</span><span class="sxs-lookup"><span data-stu-id="76844-140">source-file</span></span> | <span data-ttu-id="76844-141">Die Quelle, aus der ein Verweis erstellt werden soll.</span><span class="sxs-lookup"><span data-stu-id="76844-141">The source to create a reference from.</span></span> <span data-ttu-id="76844-142">Es muss sich um eine OpenAPI-Datei handeln.</span><span class="sxs-lookup"><span data-stu-id="76844-142">Must be an OpenAPI file.</span></span> |<span data-ttu-id="76844-143">dotnet openapi add file *.\OpenAPI.json*</span><span class="sxs-lookup"><span data-stu-id="76844-143">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="76844-144">URL hinzufügen</span><span class="sxs-lookup"><span data-stu-id="76844-144">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="76844-145">Optionen</span><span class="sxs-lookup"><span data-stu-id="76844-145">Options</span></span>

| <span data-ttu-id="76844-146">Kurze Option</span><span class="sxs-lookup"><span data-stu-id="76844-146">Short option</span></span>| <span data-ttu-id="76844-147">Lange Option</span><span class="sxs-lookup"><span data-stu-id="76844-147">Long option</span></span>| <span data-ttu-id="76844-148">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="76844-148">Description</span></span> | <span data-ttu-id="76844-149">Beispiel</span><span class="sxs-lookup"><span data-stu-id="76844-149">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="76844-150">-v</span><span class="sxs-lookup"><span data-stu-id="76844-150">-v</span></span>|<span data-ttu-id="76844-151">--verbose</span><span class="sxs-lookup"><span data-stu-id="76844-151">--verbose</span></span> | <span data-ttu-id="76844-152">Zeigt eine ausführliche Ausgabe an.</span><span class="sxs-lookup"><span data-stu-id="76844-152">Show verbose output.</span></span> |<span data-ttu-id="76844-153">dotnet openapi add url *-v* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="76844-153">dotnet openapi add url *-v* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="76844-154">-p</span><span class="sxs-lookup"><span data-stu-id="76844-154">-p</span></span>|<span data-ttu-id="76844-155">--updateProject</span><span class="sxs-lookup"><span data-stu-id="76844-155">--updateProject</span></span> | <span data-ttu-id="76844-156">Das Projekt, das bearbeitet werden soll.</span><span class="sxs-lookup"><span data-stu-id="76844-156">The project to operate on.</span></span> |<span data-ttu-id="76844-157">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="76844-157">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="76844-158">-o</span><span class="sxs-lookup"><span data-stu-id="76844-158">-o</span></span>|<span data-ttu-id="76844-159">--output-file</span><span class="sxs-lookup"><span data-stu-id="76844-159">--output-file</span></span> | <span data-ttu-id="76844-160">Speicherort für die lokale Kopie der OpenAPI-Datei.</span><span class="sxs-lookup"><span data-stu-id="76844-160">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="76844-161">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span><span class="sxs-lookup"><span data-stu-id="76844-161">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="76844-162">-c</span><span class="sxs-lookup"><span data-stu-id="76844-162">-c</span></span>|<span data-ttu-id="76844-163">--code-generator</span><span class="sxs-lookup"><span data-stu-id="76844-163">--code-generator</span></span>| <span data-ttu-id="76844-164">Der Codegenerator, der auf den Verweis angewendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="76844-164">The code generator to apply to the reference.</span></span> <span data-ttu-id="76844-165">Die Optionen sind `NSwagCSharp` und `NSwagTypeScript`.</span><span class="sxs-lookup"><span data-stu-id="76844-165">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="76844-166">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="76844-166">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="76844-167">-h</span><span class="sxs-lookup"><span data-stu-id="76844-167">-h</span></span>|<span data-ttu-id="76844-168">--help</span><span class="sxs-lookup"><span data-stu-id="76844-168">--help</span></span>|<span data-ttu-id="76844-169">Zeigt Hilfeinformationen an.</span><span class="sxs-lookup"><span data-stu-id="76844-169">Show help information</span></span>|<span data-ttu-id="76844-170">dotnet openapi add url --help</span><span class="sxs-lookup"><span data-stu-id="76844-170">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="76844-171">Argumente</span><span class="sxs-lookup"><span data-stu-id="76844-171">Arguments</span></span>

|  <span data-ttu-id="76844-172">Argument</span><span class="sxs-lookup"><span data-stu-id="76844-172">Argument</span></span>  | <span data-ttu-id="76844-173">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="76844-173">Description</span></span> | <span data-ttu-id="76844-174">Beispiel</span><span class="sxs-lookup"><span data-stu-id="76844-174">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="76844-175">source-URL</span><span class="sxs-lookup"><span data-stu-id="76844-175">source-URL</span></span> | <span data-ttu-id="76844-176">Die Quelle, aus der ein Verweis erstellt werden soll.</span><span class="sxs-lookup"><span data-stu-id="76844-176">The source to create a reference from.</span></span> <span data-ttu-id="76844-177">Es muss sich um eine URL handeln.</span><span class="sxs-lookup"><span data-stu-id="76844-177">Must be a URL.</span></span> |<span data-ttu-id="76844-178">dotnet openapi add url `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="76844-178">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="76844-179">Remove</span><span class="sxs-lookup"><span data-stu-id="76844-179">Remove</span></span>

<span data-ttu-id="76844-180">Entfernt den OpenAPI-Verweis, der mit dem angegebenen Dateinamen übereinstimmt, aus der *CSPROJ*-Datei.</span><span class="sxs-lookup"><span data-stu-id="76844-180">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="76844-181">Wenn der OpenAPI-Verweis entfernt wird, werden keine Clients generiert.</span><span class="sxs-lookup"><span data-stu-id="76844-181">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="76844-182">Lokale *JSON*- und *YAML*-Dateien werden gelöscht.</span><span class="sxs-lookup"><span data-stu-id="76844-182">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="76844-183">Optionen</span><span class="sxs-lookup"><span data-stu-id="76844-183">Options</span></span>

| <span data-ttu-id="76844-184">Kurze Option</span><span class="sxs-lookup"><span data-stu-id="76844-184">Short option</span></span>| <span data-ttu-id="76844-185">Lange Option</span><span class="sxs-lookup"><span data-stu-id="76844-185">Long option</span></span>| <span data-ttu-id="76844-186">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="76844-186">Description</span></span>| <span data-ttu-id="76844-187">Beispiel</span><span class="sxs-lookup"><span data-stu-id="76844-187">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="76844-188">-v</span><span class="sxs-lookup"><span data-stu-id="76844-188">-v</span></span>|<span data-ttu-id="76844-189">--verbose</span><span class="sxs-lookup"><span data-stu-id="76844-189">--verbose</span></span> | <span data-ttu-id="76844-190">Zeigt eine ausführliche Ausgabe an.</span><span class="sxs-lookup"><span data-stu-id="76844-190">Show verbose output.</span></span> |<span data-ttu-id="76844-191">dotnet openapi remove *-v*</span><span class="sxs-lookup"><span data-stu-id="76844-191">dotnet openapi remove *-v*</span></span>|
| <span data-ttu-id="76844-192">-p</span><span class="sxs-lookup"><span data-stu-id="76844-192">-p</span></span>|<span data-ttu-id="76844-193">--updateProject</span><span class="sxs-lookup"><span data-stu-id="76844-193">--updateProject</span></span> | <span data-ttu-id="76844-194">Das Projekt, das bearbeitet werden soll.</span><span class="sxs-lookup"><span data-stu-id="76844-194">The project to operate on.</span></span> |<span data-ttu-id="76844-195">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="76844-195">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="76844-196">-h</span><span class="sxs-lookup"><span data-stu-id="76844-196">-h</span></span>|<span data-ttu-id="76844-197">--help</span><span class="sxs-lookup"><span data-stu-id="76844-197">--help</span></span>|<span data-ttu-id="76844-198">Zeigt Hilfeinformationen an.</span><span class="sxs-lookup"><span data-stu-id="76844-198">Show help information</span></span>|<span data-ttu-id="76844-199">dotnet openapi remove --help</span><span class="sxs-lookup"><span data-stu-id="76844-199">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="76844-200">Argumente</span><span class="sxs-lookup"><span data-stu-id="76844-200">Arguments</span></span>

|  <span data-ttu-id="76844-201">Argument</span><span class="sxs-lookup"><span data-stu-id="76844-201">Argument</span></span>  | <span data-ttu-id="76844-202">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="76844-202">Description</span></span>| <span data-ttu-id="76844-203">Beispiel</span><span class="sxs-lookup"><span data-stu-id="76844-203">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="76844-204">source-file</span><span class="sxs-lookup"><span data-stu-id="76844-204">source-file</span></span> | <span data-ttu-id="76844-205">Die Quelle, aus der der Verweis entfernt werden soll.</span><span class="sxs-lookup"><span data-stu-id="76844-205">The source to remove the reference to.</span></span> |<span data-ttu-id="76844-206">dotnet openapi remove *.\OpenAPI.json*</span><span class="sxs-lookup"><span data-stu-id="76844-206">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="76844-207">Aktualisieren</span><span class="sxs-lookup"><span data-stu-id="76844-207">Refresh</span></span>

<span data-ttu-id="76844-208">Aktualisiert die lokale Version einer Datei, die unter Verwendung der neuesten Download-URL heruntergeladen wurde.</span><span class="sxs-lookup"><span data-stu-id="76844-208">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="76844-209">Optionen</span><span class="sxs-lookup"><span data-stu-id="76844-209">Options</span></span>

| <span data-ttu-id="76844-210">Kurze Option</span><span class="sxs-lookup"><span data-stu-id="76844-210">Short option</span></span>| <span data-ttu-id="76844-211">Lange Option</span><span class="sxs-lookup"><span data-stu-id="76844-211">Long option</span></span>| <span data-ttu-id="76844-212">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="76844-212">Description</span></span> | <span data-ttu-id="76844-213">Beispiel</span><span class="sxs-lookup"><span data-stu-id="76844-213">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="76844-214">-v</span><span class="sxs-lookup"><span data-stu-id="76844-214">-v</span></span>|<span data-ttu-id="76844-215">--verbose</span><span class="sxs-lookup"><span data-stu-id="76844-215">--verbose</span></span> | <span data-ttu-id="76844-216">Zeigt eine ausführliche Ausgabe an.</span><span class="sxs-lookup"><span data-stu-id="76844-216">Show verbose output.</span></span> | <span data-ttu-id="76844-217">dotnet openapi refresh *-v* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="76844-217">dotnet openapi refresh *-v* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="76844-218">-p</span><span class="sxs-lookup"><span data-stu-id="76844-218">-p</span></span>|<span data-ttu-id="76844-219">--updateProject</span><span class="sxs-lookup"><span data-stu-id="76844-219">--updateProject</span></span> | <span data-ttu-id="76844-220">Das Projekt, das bearbeitet werden soll.</span><span class="sxs-lookup"><span data-stu-id="76844-220">The project to operate on.</span></span> | <span data-ttu-id="76844-221">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="76844-221">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="76844-222">-h</span><span class="sxs-lookup"><span data-stu-id="76844-222">-h</span></span>|<span data-ttu-id="76844-223">--help</span><span class="sxs-lookup"><span data-stu-id="76844-223">--help</span></span>|<span data-ttu-id="76844-224">Zeigt Hilfeinformationen an.</span><span class="sxs-lookup"><span data-stu-id="76844-224">Show help information</span></span>|<span data-ttu-id="76844-225">dotnet openapi refresh --help</span><span class="sxs-lookup"><span data-stu-id="76844-225">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="76844-226">Argumente</span><span class="sxs-lookup"><span data-stu-id="76844-226">Arguments</span></span>

|  <span data-ttu-id="76844-227">Argument</span><span class="sxs-lookup"><span data-stu-id="76844-227">Argument</span></span>  | <span data-ttu-id="76844-228">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="76844-228">Description</span></span> | <span data-ttu-id="76844-229">Beispiel</span><span class="sxs-lookup"><span data-stu-id="76844-229">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="76844-230">source-URL</span><span class="sxs-lookup"><span data-stu-id="76844-230">source-URL</span></span> | <span data-ttu-id="76844-231">Die URL, aus der der Verweis aktualisiert werden soll.</span><span class="sxs-lookup"><span data-stu-id="76844-231">The URL to refresh the reference from.</span></span> | <span data-ttu-id="76844-232">dotnet openapi refresh `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="76844-232">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
