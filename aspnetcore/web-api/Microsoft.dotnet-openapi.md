---
title: Entwickeln von ASP.NET Core-Apps mit OpenAPI
author: ryanbrandenburg
description: Hier wird veranschaulicht, wie Sie das Tool „Microsoft.dotnet-openapi“ verwenden, um Verweise zu OpenAPI-Dateien hinzuzufügen.
ms.author: rybrande
ms.date: 09/26/2019
monikerRange: '>= aspnetcore-3.0'
uid: web-api/Microsoft.dotnet-openapi
ms.openlocfilehash: 4be2846f0348788102672978a0487e646da434a0
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354750"
---
# <a name="develop-aspnet-core-apps-using-openapi-tools"></a><span data-ttu-id="1e448-103">Entwickeln von ASP.NET Core-Apps mit OpenAPI-Tools</span><span class="sxs-lookup"><span data-stu-id="1e448-103">Develop ASP.NET Core apps using OpenAPI tools</span></span>

<span data-ttu-id="1e448-104">Von Ryan Brandenburg</span><span class="sxs-lookup"><span data-stu-id="1e448-104">By Ryan Brandenburg</span></span>

<span data-ttu-id="1e448-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) ist ein [globales .NET Core-Tool](/dotnet/core/tools/global-tools) zum Verwalten von [OpenAPI](https://github.com/OAI/OpenAPI-Specification)-Verweisen innerhalb eines Projekts.</span><span class="sxs-lookup"><span data-stu-id="1e448-105">[Microsoft.dotnet-openapi](https://www.nuget.org/packages/Microsoft.dotnet-openapi) is a [.NET Core Global Tool](/dotnet/core/tools/global-tools) for managing [OpenAPI](https://github.com/OAI/OpenAPI-Specification) references within a project.</span></span>

## <a name="installation"></a><span data-ttu-id="1e448-106">Installation</span><span class="sxs-lookup"><span data-stu-id="1e448-106">Installation</span></span>

<span data-ttu-id="1e448-107">Führen Sie den folgenden Befehl aus, um `Microsoft.dotnet-openapi` zu installieren:</span><span class="sxs-lookup"><span data-stu-id="1e448-107">To install `Microsoft.dotnet-openapi`, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-openapi
```

## <a name="add"></a><span data-ttu-id="1e448-108">Hinzufügen</span><span class="sxs-lookup"><span data-stu-id="1e448-108">Add</span></span>

<span data-ttu-id="1e448-109">Wenn Sie mit einem der Befehle auf dieser Seite einen OpenAPI-Verweis hinzufügen, wird ein `<OpenApiReference />`-Element ähnlich dem folgenden zur *CSPROJ*-Datei hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="1e448-109">Adding an OpenAPI reference using any of the commands on this page adds an `<OpenApiReference />` element similar to the following to the *.csproj* file:</span></span>

```xml
<OpenApiReference Include="openapi.json" />
```

<span data-ttu-id="1e448-110">Der oben genannte Verweis ist erforderlich, damit die App den generierten Clientcode aufrufen kann.</span><span class="sxs-lookup"><span data-stu-id="1e448-110">The preceding reference is required for the app to call the generated client code.</span></span>

<!-- TODO: Restore after https://github.com/aspnet/AspNetCore/issues/12738
### Add Project

#### Options

| Short option | Long option | Description | Example |
|-------|------|-------|---------|
| -p|--project | The project to operate on. |dotnet openapi add project *--project .\Ref.csproj* ../Ref/ProjRef.csproj |

#### Arguments

|  Argument  | Description | Example |
|-------------|-------------|---------|
| source-file | The source to create a reference from. Must be a project file. |dotnet openapi add project *../Ref/ProjRef.csproj* | -->

### <a name="add-file"></a><span data-ttu-id="1e448-111">Datei hinzufügen</span><span class="sxs-lookup"><span data-stu-id="1e448-111">Add File</span></span>

#### <a name="options"></a><span data-ttu-id="1e448-112">Optionen</span><span class="sxs-lookup"><span data-stu-id="1e448-112">Options</span></span>

| <span data-ttu-id="1e448-113">Kurze Option</span><span class="sxs-lookup"><span data-stu-id="1e448-113">Short option</span></span>| <span data-ttu-id="1e448-114">Lange Option</span><span class="sxs-lookup"><span data-stu-id="1e448-114">Long option</span></span>| <span data-ttu-id="1e448-115">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="1e448-115">Description</span></span> | <span data-ttu-id="1e448-116">Beispiel</span><span class="sxs-lookup"><span data-stu-id="1e448-116">Example</span></span> |
|-------|------|-------|---------|
| <span data-ttu-id="1e448-117">-p</span><span class="sxs-lookup"><span data-stu-id="1e448-117">-p</span></span>|<span data-ttu-id="1e448-118">--updateProject</span><span class="sxs-lookup"><span data-stu-id="1e448-118">--updateProject</span></span> | <span data-ttu-id="1e448-119">Das Projekt, das bearbeitet werden soll.</span><span class="sxs-lookup"><span data-stu-id="1e448-119">The project to operate on.</span></span> |<span data-ttu-id="1e448-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="1e448-120">dotnet openapi add file *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="1e448-121">-c</span><span class="sxs-lookup"><span data-stu-id="1e448-121">-c</span></span>|<span data-ttu-id="1e448-122">--code-generator</span><span class="sxs-lookup"><span data-stu-id="1e448-122">--code-generator</span></span>| <span data-ttu-id="1e448-123">Der Codegenerator, der auf den Verweis angewendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="1e448-123">The code generator to apply to the reference.</span></span> <span data-ttu-id="1e448-124">Die Optionen sind `NSwagCSharp` und `NSwagTypeScript`.</span><span class="sxs-lookup"><span data-stu-id="1e448-124">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> <span data-ttu-id="1e448-125">Wenn `--code-generator` nicht angegeben ist, werden standardmäßig `NSwagCSharp`-Tools verwendet.</span><span class="sxs-lookup"><span data-stu-id="1e448-125">If `--code-generator` is not specified the tooling defaults to `NSwagCSharp`.</span></span>|<span data-ttu-id="1e448-126">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="1e448-126">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="1e448-127">-h</span><span class="sxs-lookup"><span data-stu-id="1e448-127">-h</span></span>|<span data-ttu-id="1e448-128">--help</span><span class="sxs-lookup"><span data-stu-id="1e448-128">--help</span></span>|<span data-ttu-id="1e448-129">Zeigt Hilfeinformationen an.</span><span class="sxs-lookup"><span data-stu-id="1e448-129">Show help information</span></span>|<span data-ttu-id="1e448-130">dotnet openapi add file --help</span><span class="sxs-lookup"><span data-stu-id="1e448-130">dotnet openapi add file --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="1e448-131">Argumente</span><span class="sxs-lookup"><span data-stu-id="1e448-131">Arguments</span></span>

|  <span data-ttu-id="1e448-132">Argument</span><span class="sxs-lookup"><span data-stu-id="1e448-132">Argument</span></span>  | <span data-ttu-id="1e448-133">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="1e448-133">Description</span></span> | <span data-ttu-id="1e448-134">Beispiel</span><span class="sxs-lookup"><span data-stu-id="1e448-134">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="1e448-135">source-file</span><span class="sxs-lookup"><span data-stu-id="1e448-135">source-file</span></span> | <span data-ttu-id="1e448-136">Die Quelle, aus der ein Verweis erstellt werden soll.</span><span class="sxs-lookup"><span data-stu-id="1e448-136">The source to create a reference from.</span></span> <span data-ttu-id="1e448-137">Es muss sich um eine OpenAPI-Datei handeln.</span><span class="sxs-lookup"><span data-stu-id="1e448-137">Must be an OpenAPI file.</span></span> |<span data-ttu-id="1e448-138">dotnet openapi add file *.\OpenAPI.json*</span><span class="sxs-lookup"><span data-stu-id="1e448-138">dotnet openapi add file *.\OpenAPI.json*</span></span> |

### <a name="add-url"></a><span data-ttu-id="1e448-139">URL hinzufügen</span><span class="sxs-lookup"><span data-stu-id="1e448-139">Add URL</span></span>

#### <a name="options"></a><span data-ttu-id="1e448-140">Optionen</span><span class="sxs-lookup"><span data-stu-id="1e448-140">Options</span></span>

| <span data-ttu-id="1e448-141">Kurze Option</span><span class="sxs-lookup"><span data-stu-id="1e448-141">Short option</span></span>| <span data-ttu-id="1e448-142">Lange Option</span><span class="sxs-lookup"><span data-stu-id="1e448-142">Long option</span></span>| <span data-ttu-id="1e448-143">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="1e448-143">Description</span></span> | <span data-ttu-id="1e448-144">Beispiel</span><span class="sxs-lookup"><span data-stu-id="1e448-144">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="1e448-145">-p</span><span class="sxs-lookup"><span data-stu-id="1e448-145">-p</span></span>|<span data-ttu-id="1e448-146">--updateProject</span><span class="sxs-lookup"><span data-stu-id="1e448-146">--updateProject</span></span> | <span data-ttu-id="1e448-147">Das Projekt, das bearbeitet werden soll.</span><span class="sxs-lookup"><span data-stu-id="1e448-147">The project to operate on.</span></span> |<span data-ttu-id="1e448-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="1e448-148">dotnet openapi add url *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="1e448-149">-o</span><span class="sxs-lookup"><span data-stu-id="1e448-149">-o</span></span>|<span data-ttu-id="1e448-150">--output-file</span><span class="sxs-lookup"><span data-stu-id="1e448-150">--output-file</span></span> | <span data-ttu-id="1e448-151">Speicherort für die lokale Kopie der OpenAPI-Datei.</span><span class="sxs-lookup"><span data-stu-id="1e448-151">Where to place the local copy of the OpenAPI file.</span></span> |<span data-ttu-id="1e448-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span><span class="sxs-lookup"><span data-stu-id="1e448-152">dotnet openapi add url `https://contoso.com/openapi.json` *--output-file myclient.json*</span></span> |
| <span data-ttu-id="1e448-153">-c</span><span class="sxs-lookup"><span data-stu-id="1e448-153">-c</span></span>|<span data-ttu-id="1e448-154">--code-generator</span><span class="sxs-lookup"><span data-stu-id="1e448-154">--code-generator</span></span>| <span data-ttu-id="1e448-155">Der Codegenerator, der auf den Verweis angewendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="1e448-155">The code generator to apply to the reference.</span></span> <span data-ttu-id="1e448-156">Die Optionen sind `NSwagCSharp` und `NSwagTypeScript`.</span><span class="sxs-lookup"><span data-stu-id="1e448-156">Options are `NSwagCSharp` and `NSwagTypeScript`.</span></span> |<span data-ttu-id="1e448-157">dotnet openapi add file .\OpenApi.json --code-generator</span><span class="sxs-lookup"><span data-stu-id="1e448-157">dotnet openapi add file .\OpenApi.json --code-generator</span></span>
| <span data-ttu-id="1e448-158">-h</span><span class="sxs-lookup"><span data-stu-id="1e448-158">-h</span></span>|<span data-ttu-id="1e448-159">--help</span><span class="sxs-lookup"><span data-stu-id="1e448-159">--help</span></span>|<span data-ttu-id="1e448-160">Zeigt Hilfeinformationen an.</span><span class="sxs-lookup"><span data-stu-id="1e448-160">Show help information</span></span>|<span data-ttu-id="1e448-161">dotnet openapi add url --help</span><span class="sxs-lookup"><span data-stu-id="1e448-161">dotnet openapi add url --help</span></span>|

#### <a name="arguments"></a><span data-ttu-id="1e448-162">Argumente</span><span class="sxs-lookup"><span data-stu-id="1e448-162">Arguments</span></span>

|  <span data-ttu-id="1e448-163">Argument</span><span class="sxs-lookup"><span data-stu-id="1e448-163">Argument</span></span>  | <span data-ttu-id="1e448-164">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="1e448-164">Description</span></span> | <span data-ttu-id="1e448-165">Beispiel</span><span class="sxs-lookup"><span data-stu-id="1e448-165">Example</span></span> |
|-------------|-------------|---------|
| <span data-ttu-id="1e448-166">source-URL</span><span class="sxs-lookup"><span data-stu-id="1e448-166">source-URL</span></span> | <span data-ttu-id="1e448-167">Die Quelle, aus der ein Verweis erstellt werden soll.</span><span class="sxs-lookup"><span data-stu-id="1e448-167">The source to create a reference from.</span></span> <span data-ttu-id="1e448-168">Es muss sich um eine URL handeln.</span><span class="sxs-lookup"><span data-stu-id="1e448-168">Must be a URL.</span></span> |<span data-ttu-id="1e448-169">dotnet openapi add url `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="1e448-169">dotnet openapi add url `https://contoso.com/openapi.json`</span></span> |

## <a name="remove"></a><span data-ttu-id="1e448-170">Remove</span><span class="sxs-lookup"><span data-stu-id="1e448-170">Remove</span></span>

<span data-ttu-id="1e448-171">Entfernt den OpenAPI-Verweis, der mit dem angegebenen Dateinamen übereinstimmt, aus der *CSPROJ*-Datei.</span><span class="sxs-lookup"><span data-stu-id="1e448-171">Removes the OpenAPI reference matching the given filename from the *.csproj* file.</span></span> <span data-ttu-id="1e448-172">Wenn der OpenAPI-Verweis entfernt wird, werden keine Clients generiert.</span><span class="sxs-lookup"><span data-stu-id="1e448-172">When the OpenAPI reference is removed, clients won't be generated.</span></span> <span data-ttu-id="1e448-173">Lokale *JSON*- und *YAML*-Dateien werden gelöscht.</span><span class="sxs-lookup"><span data-stu-id="1e448-173">Local *.json* and *.yaml* files are deleted.</span></span>

### <a name="options"></a><span data-ttu-id="1e448-174">Optionen</span><span class="sxs-lookup"><span data-stu-id="1e448-174">Options</span></span>

| <span data-ttu-id="1e448-175">Kurze Option</span><span class="sxs-lookup"><span data-stu-id="1e448-175">Short option</span></span>| <span data-ttu-id="1e448-176">Lange Option</span><span class="sxs-lookup"><span data-stu-id="1e448-176">Long option</span></span>| <span data-ttu-id="1e448-177">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="1e448-177">Description</span></span>| <span data-ttu-id="1e448-178">Beispiel</span><span class="sxs-lookup"><span data-stu-id="1e448-178">Example</span></span> |
|-------|------|------------|---------|
| <span data-ttu-id="1e448-179">-p</span><span class="sxs-lookup"><span data-stu-id="1e448-179">-p</span></span>|<span data-ttu-id="1e448-180">--updateProject</span><span class="sxs-lookup"><span data-stu-id="1e448-180">--updateProject</span></span> | <span data-ttu-id="1e448-181">Das Projekt, das bearbeitet werden soll.</span><span class="sxs-lookup"><span data-stu-id="1e448-181">The project to operate on.</span></span> |<span data-ttu-id="1e448-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span><span class="sxs-lookup"><span data-stu-id="1e448-182">dotnet openapi remove *--updateProject .\Ref.csproj* .\OpenAPI.json</span></span> |
| <span data-ttu-id="1e448-183">-h</span><span class="sxs-lookup"><span data-stu-id="1e448-183">-h</span></span>|<span data-ttu-id="1e448-184">--help</span><span class="sxs-lookup"><span data-stu-id="1e448-184">--help</span></span>|<span data-ttu-id="1e448-185">Zeigt Hilfeinformationen an.</span><span class="sxs-lookup"><span data-stu-id="1e448-185">Show help information</span></span>|<span data-ttu-id="1e448-186">dotnet openapi remove --help</span><span class="sxs-lookup"><span data-stu-id="1e448-186">dotnet openapi remove --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="1e448-187">Argumente</span><span class="sxs-lookup"><span data-stu-id="1e448-187">Arguments</span></span>

|  <span data-ttu-id="1e448-188">Argument</span><span class="sxs-lookup"><span data-stu-id="1e448-188">Argument</span></span>  | <span data-ttu-id="1e448-189">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="1e448-189">Description</span></span>| <span data-ttu-id="1e448-190">Beispiel</span><span class="sxs-lookup"><span data-stu-id="1e448-190">Example</span></span> |
| ------------|------------|---------|
| <span data-ttu-id="1e448-191">source-file</span><span class="sxs-lookup"><span data-stu-id="1e448-191">source-file</span></span> | <span data-ttu-id="1e448-192">Die Quelle, aus der der Verweis entfernt werden soll.</span><span class="sxs-lookup"><span data-stu-id="1e448-192">The source to remove the reference to.</span></span> |<span data-ttu-id="1e448-193">dotnet openapi remove *.\OpenAPI.json*</span><span class="sxs-lookup"><span data-stu-id="1e448-193">dotnet openapi remove *.\OpenAPI.json*</span></span> |

## <a name="refresh"></a><span data-ttu-id="1e448-194">Aktualisieren</span><span class="sxs-lookup"><span data-stu-id="1e448-194">Refresh</span></span>

<span data-ttu-id="1e448-195">Aktualisiert die lokale Version einer Datei, die unter Verwendung der neuesten Download-URL heruntergeladen wurde.</span><span class="sxs-lookup"><span data-stu-id="1e448-195">Refreshes the local version of a file that was downloaded using the latest content from the download URL.</span></span>

### <a name="options"></a><span data-ttu-id="1e448-196">Optionen</span><span class="sxs-lookup"><span data-stu-id="1e448-196">Options</span></span>

| <span data-ttu-id="1e448-197">Kurze Option</span><span class="sxs-lookup"><span data-stu-id="1e448-197">Short option</span></span>| <span data-ttu-id="1e448-198">Lange Option</span><span class="sxs-lookup"><span data-stu-id="1e448-198">Long option</span></span>| <span data-ttu-id="1e448-199">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="1e448-199">Description</span></span> | <span data-ttu-id="1e448-200">Beispiel</span><span class="sxs-lookup"><span data-stu-id="1e448-200">Example</span></span> |
|-------|------|-------------|---------|
| <span data-ttu-id="1e448-201">-p</span><span class="sxs-lookup"><span data-stu-id="1e448-201">-p</span></span>|<span data-ttu-id="1e448-202">--updateProject</span><span class="sxs-lookup"><span data-stu-id="1e448-202">--updateProject</span></span> | <span data-ttu-id="1e448-203">Das Projekt, das bearbeitet werden soll.</span><span class="sxs-lookup"><span data-stu-id="1e448-203">The project to operate on.</span></span> | <span data-ttu-id="1e448-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="1e448-204">dotnet openapi refresh *--updateProject .\Ref.csproj* `https://contoso.com/openapi.json`</span></span> |
| <span data-ttu-id="1e448-205">-h</span><span class="sxs-lookup"><span data-stu-id="1e448-205">-h</span></span>|<span data-ttu-id="1e448-206">--help</span><span class="sxs-lookup"><span data-stu-id="1e448-206">--help</span></span>|<span data-ttu-id="1e448-207">Zeigt Hilfeinformationen an.</span><span class="sxs-lookup"><span data-stu-id="1e448-207">Show help information</span></span>|<span data-ttu-id="1e448-208">dotnet openapi refresh --help</span><span class="sxs-lookup"><span data-stu-id="1e448-208">dotnet openapi refresh --help</span></span>|

### <a name="arguments"></a><span data-ttu-id="1e448-209">Argumente</span><span class="sxs-lookup"><span data-stu-id="1e448-209">Arguments</span></span>

|  <span data-ttu-id="1e448-210">Argument</span><span class="sxs-lookup"><span data-stu-id="1e448-210">Argument</span></span>  | <span data-ttu-id="1e448-211">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="1e448-211">Description</span></span> | <span data-ttu-id="1e448-212">Beispiel</span><span class="sxs-lookup"><span data-stu-id="1e448-212">Example</span></span> |
| ------------|-------------|---------|
| <span data-ttu-id="1e448-213">source-URL</span><span class="sxs-lookup"><span data-stu-id="1e448-213">source-URL</span></span> | <span data-ttu-id="1e448-214">Die URL, aus der der Verweis aktualisiert werden soll.</span><span class="sxs-lookup"><span data-stu-id="1e448-214">The URL to refresh the reference from.</span></span> | <span data-ttu-id="1e448-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span><span class="sxs-lookup"><span data-stu-id="1e448-215">dotnet openapi refresh `https://contoso.com/openapi.json`</span></span> |
