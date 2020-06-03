---
title: Verwalten von Protobuf-Verweisen mit dotnet-grpc
author: juntaoluo
description: Erfahren Sie mehr über das Hinzufügen, Aktualisieren, Entfernen und Auflisten von Protobuf-Verweisen mit dem globalen Tool „dotnet-grpc“.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/dotnet-grpc
ms.openlocfilehash: 0990013947be2cee5045deac92efc3c6bcf12e03
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768834"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a><span data-ttu-id="92f83-103">Verwalten von Protobuf-Verweisen mit dotnet-grpc</span><span class="sxs-lookup"><span data-stu-id="92f83-103">Manage Protobuf references with dotnet-grpc</span></span>

<span data-ttu-id="92f83-104">Von [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="92f83-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="92f83-105">`dotnet-grpc` ist ein globales .NET Core-Tool zum Verwalten von [Protobuf ( *.proto*)](xref:grpc/basics#proto-file)-Verweisen in einem .NET gRPC-Projekt.</span><span class="sxs-lookup"><span data-stu-id="92f83-105">`dotnet-grpc` is a .NET Core Global Tool for managing [Protobuf (*.proto*)](xref:grpc/basics#proto-file) references within a .NET gRPC project.</span></span> <span data-ttu-id="92f83-106">Das Tool kann zum Hinzufügen, Aktualisieren, Entfernen und Auflisten von Protobuf-Verweisen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="92f83-106">The tool can be used to add, refresh, remove, and list Protobuf references.</span></span>

## <a name="installation"></a><span data-ttu-id="92f83-107">Installation</span><span class="sxs-lookup"><span data-stu-id="92f83-107">Installation</span></span>

<span data-ttu-id="92f83-108">Zur Installation des `dotnet-grpc` [globales .NET Core-Tool](/dotnet/core/tools/global-tools), führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="92f83-108">To install the `dotnet-grpc` [.NET Core Global Tool](/dotnet/core/tools/global-tools), run the following command:</span></span>

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a><span data-ttu-id="92f83-109">Hinzufügen von Verweisen</span><span class="sxs-lookup"><span data-stu-id="92f83-109">Add references</span></span>

<span data-ttu-id="92f83-110">`dotnet-grpc` kann verwendet werden, um Protobuf-Verweise als `<Protobuf />`-Elemente zur *CSPROJ*-Datei hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="92f83-110">`dotnet-grpc` can be used to add Protobuf references as `<Protobuf />` items to the *.csproj* file:</span></span>

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

<span data-ttu-id="92f83-111">Die Protobuf-Verweise werden zum Generieren der C#-Client- und/oder -Serverobjekte verwendet.</span><span class="sxs-lookup"><span data-stu-id="92f83-111">The Protobuf references are used to generate the C# client and/or server assets.</span></span> <span data-ttu-id="92f83-112">Das `dotnet-grpc`-Tool kann Folgendes:</span><span class="sxs-lookup"><span data-stu-id="92f83-112">The `dotnet-grpc` tool can:</span></span>

* <span data-ttu-id="92f83-113">Protobuf-Verweise aus lokalen Dateien auf dem Datenträger erstellen.</span><span class="sxs-lookup"><span data-stu-id="92f83-113">Create a Protobuf reference from local files on disk.</span></span>
* <span data-ttu-id="92f83-114">Einen Protobuf-Verweis aus einer Remotedatei erstellen, die durch eine URL angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="92f83-114">Create a Protobuf reference from a remote file specified by a URL.</span></span>
* <span data-ttu-id="92f83-115">Sicherstellen, dass die richtigen gRPC-Paketabhängigkeiten zum Projekt hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="92f83-115">Ensure the correct gRPC package dependencies are added to the project.</span></span>

<span data-ttu-id="92f83-116">Das `Grpc.AspNetCore`-Paket wird z. B. zu einer Web-App hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="92f83-116">For example, the `Grpc.AspNetCore` package is added to a web app.</span></span> <span data-ttu-id="92f83-117">`Grpc.AspNetCore` enthält gRPC-Server- und -Clientbibliotheken sowie Toolunterstützung.</span><span class="sxs-lookup"><span data-stu-id="92f83-117">`Grpc.AspNetCore` contains gRPC server and client libraries and tooling support.</span></span> <span data-ttu-id="92f83-118">Alternativ werden die Pakete `Grpc.Net.Client`, `Grpc.Tools` und `Google.Protobuf`, die nur die gRPC-Clientbibliotheken und die Toolunterstützung enthalten, zu einer Konsolen-App hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="92f83-118">Alternatively, the `Grpc.Net.Client`, `Grpc.Tools` and `Google.Protobuf` packages, which contain only the gRPC client libraries and tooling support, are added to a Console app.</span></span>

### <a name="add-file"></a><span data-ttu-id="92f83-119">Datei hinzufügen</span><span class="sxs-lookup"><span data-stu-id="92f83-119">Add file</span></span>

<span data-ttu-id="92f83-120">Der Befehl `add-file` wird verwendet, um lokale Dateien auf dem Datenträger als Protobuf-Verweise hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="92f83-120">The `add-file` command is used to add local files on disk as Protobuf references.</span></span> <span data-ttu-id="92f83-121">Die bereitgestellten Dateipfade:</span><span class="sxs-lookup"><span data-stu-id="92f83-121">The file paths provided:</span></span>

* <span data-ttu-id="92f83-122">Kann relativ zum aktuellen Verzeichnis oder zu absoluten Pfaden sein.</span><span class="sxs-lookup"><span data-stu-id="92f83-122">Can be relative to the current directory or absolute paths.</span></span>
* <span data-ttu-id="92f83-123">Kann Platzhalter für musterbasiertes Datei-[Globbing](https://wikipedia.org/wiki/Glob_(programming)) enthalten.</span><span class="sxs-lookup"><span data-stu-id="92f83-123">May contain wild cards for pattern-based file [globbing](https://wikipedia.org/wiki/Glob_(programming)).</span></span>

<span data-ttu-id="92f83-124">Wenn sich Dateien außerhalb des Projektverzeichnisses befinden, wird ein `Link`-Element hinzugefügt, um die Datei unter dem Ordner `Protos` in Visual Studio anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="92f83-124">If any files are outside the project directory, a `Link` element is added to display the file under the folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="92f83-125">Verwendung</span><span class="sxs-lookup"><span data-stu-id="92f83-125">Usage</span></span>

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a><span data-ttu-id="92f83-126">Argumente</span><span class="sxs-lookup"><span data-stu-id="92f83-126">Arguments</span></span>

| <span data-ttu-id="92f83-127">Argument</span><span class="sxs-lookup"><span data-stu-id="92f83-127">Argument</span></span> | <span data-ttu-id="92f83-128">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="92f83-128">Description</span></span> |
|-|-|
| <span data-ttu-id="92f83-129">Dateien</span><span class="sxs-lookup"><span data-stu-id="92f83-129">files</span></span> | <span data-ttu-id="92f83-130">Die Protobuf-Dateiverweise.</span><span class="sxs-lookup"><span data-stu-id="92f83-130">The protobuf file references.</span></span> <span data-ttu-id="92f83-131">Dies kann ein Pfad zu „Glob“ für lokale Protobuf-Dateien sein.</span><span class="sxs-lookup"><span data-stu-id="92f83-131">These can be a path to glob for local protobuf files.</span></span> |

#### <a name="options"></a><span data-ttu-id="92f83-132">Optionen</span><span class="sxs-lookup"><span data-stu-id="92f83-132">Options</span></span>

| <span data-ttu-id="92f83-133">Kurze Option</span><span class="sxs-lookup"><span data-stu-id="92f83-133">Short option</span></span> | <span data-ttu-id="92f83-134">Lange Option</span><span class="sxs-lookup"><span data-stu-id="92f83-134">Long option</span></span> | <span data-ttu-id="92f83-135">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="92f83-135">Description</span></span> |
|-|-|-|
| <span data-ttu-id="92f83-136">-p</span><span class="sxs-lookup"><span data-stu-id="92f83-136">-p</span></span> | <span data-ttu-id="92f83-137">--project</span><span class="sxs-lookup"><span data-stu-id="92f83-137">--project</span></span> | <span data-ttu-id="92f83-138">Der Pfad zu der Projektdatei, mit der gearbeitet werden soll.</span><span class="sxs-lookup"><span data-stu-id="92f83-138">The path to the project file to operate on.</span></span> <span data-ttu-id="92f83-139">Wenn keine Datei angegeben ist, sucht der Befehl im aktuellen Verzeichnis nach einer Projektdatei.</span><span class="sxs-lookup"><span data-stu-id="92f83-139">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="92f83-140">-S</span><span class="sxs-lookup"><span data-stu-id="92f83-140">-s</span></span> | <span data-ttu-id="92f83-141">--services</span><span class="sxs-lookup"><span data-stu-id="92f83-141">--services</span></span> | <span data-ttu-id="92f83-142">Der Typ der gRPC-Dienste, die generiert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="92f83-142">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="92f83-143">Wenn `Default` angegeben ist, wird `Both` für Webprojekte und `Client` für Nicht-Webprojekte verwendet.</span><span class="sxs-lookup"><span data-stu-id="92f83-143">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="92f83-144">Zulässige Werte sind `Both`, `Client`, `Default`, `None`, `Server`.</span><span class="sxs-lookup"><span data-stu-id="92f83-144">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="92f83-145">-i</span><span class="sxs-lookup"><span data-stu-id="92f83-145">-i</span></span> | <span data-ttu-id="92f83-146">--additional-import-dirs</span><span class="sxs-lookup"><span data-stu-id="92f83-146">--additional-import-dirs</span></span> | <span data-ttu-id="92f83-147">Zusätzliche Verzeichnisse, die bei der Auflösung von Importen für die Protobuf-Dateien verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="92f83-147">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="92f83-148">Dies ist eine durch Semikolons getrennte Liste von Pfaden.</span><span class="sxs-lookup"><span data-stu-id="92f83-148">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="92f83-149">--access</span><span class="sxs-lookup"><span data-stu-id="92f83-149">--access</span></span> | <span data-ttu-id="92f83-150">Der Zugriffsmodifizierer, der für die generierten C#-Klassen verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="92f83-150">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="92f83-151">Der Standardwert ist `Public`.</span><span class="sxs-lookup"><span data-stu-id="92f83-151">The default value is `Public`.</span></span> <span data-ttu-id="92f83-152">Zulässige Werte sind `Internal` und `Public`.</span><span class="sxs-lookup"><span data-stu-id="92f83-152">Accepted values are `Internal` and `Public`.</span></span>

### <a name="add-url"></a><span data-ttu-id="92f83-153">URL hinzufügen</span><span class="sxs-lookup"><span data-stu-id="92f83-153">Add URL</span></span>

<span data-ttu-id="92f83-154">Der Befehl `add-url` wird verwendet, um eine Remotedatei hinzuzufügen, die durch eine Quell-URL als Protobuf-Verweis angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="92f83-154">The `add-url` command is used to add a remote file specified by an source URL as Protobuf reference.</span></span> <span data-ttu-id="92f83-155">Es muss ein Dateipfad angegeben werden, um anzugeben, wo die Remotedatei heruntergeladen werden soll.</span><span class="sxs-lookup"><span data-stu-id="92f83-155">A file path must be provided to specify where to download the remote file.</span></span> <span data-ttu-id="92f83-156">Der Dateipfad kann relativ zum aktuellen Verzeichnis oder ein absoluter Pfad sein.</span><span class="sxs-lookup"><span data-stu-id="92f83-156">The file path can be relative to the current directory or an absolute path.</span></span> <span data-ttu-id="92f83-157">Wenn der Dateipfad außerhalb des Projektverzeichnisses liegt, wird ein `Link`-Element hinzugefügt, um die Datei unter dem virtuellen Ordner `Protos` in Visual Studio anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="92f83-157">If the file path is outside the project directory, a `Link` element is added to display the file under the virtual folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="92f83-158">Verwendung</span><span class="sxs-lookup"><span data-stu-id="92f83-158">Usage</span></span>

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a><span data-ttu-id="92f83-159">Argumente</span><span class="sxs-lookup"><span data-stu-id="92f83-159">Arguments</span></span>

| <span data-ttu-id="92f83-160">Argument</span><span class="sxs-lookup"><span data-stu-id="92f83-160">Argument</span></span> | <span data-ttu-id="92f83-161">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="92f83-161">Description</span></span> |
|-|-|
| <span data-ttu-id="92f83-162">url</span><span class="sxs-lookup"><span data-stu-id="92f83-162">url</span></span> | <span data-ttu-id="92f83-163">Die URL zu einer Protobuf-Remotedatei.</span><span class="sxs-lookup"><span data-stu-id="92f83-163">The URL to a remote protobuf file.</span></span> |

#### <a name="options"></a><span data-ttu-id="92f83-164">Optionen</span><span class="sxs-lookup"><span data-stu-id="92f83-164">Options</span></span>

| <span data-ttu-id="92f83-165">Kurze Option</span><span class="sxs-lookup"><span data-stu-id="92f83-165">Short option</span></span> | <span data-ttu-id="92f83-166">Lange Option</span><span class="sxs-lookup"><span data-stu-id="92f83-166">Long option</span></span> | <span data-ttu-id="92f83-167">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="92f83-167">Description</span></span> |
|-|-|-|
| <span data-ttu-id="92f83-168">-o</span><span class="sxs-lookup"><span data-stu-id="92f83-168">-o</span></span> | <span data-ttu-id="92f83-169">--output</span><span class="sxs-lookup"><span data-stu-id="92f83-169">--output</span></span> | <span data-ttu-id="92f83-170">Gibt den Downloadpfad für die Protobuf-Remotedatei an.</span><span class="sxs-lookup"><span data-stu-id="92f83-170">Specifies the download path for the remote protobuf file.</span></span> <span data-ttu-id="92f83-171">Diese Option muss angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="92f83-171">This is a required option.</span></span>
| <span data-ttu-id="92f83-172">-p</span><span class="sxs-lookup"><span data-stu-id="92f83-172">-p</span></span> | <span data-ttu-id="92f83-173">--project</span><span class="sxs-lookup"><span data-stu-id="92f83-173">--project</span></span> | <span data-ttu-id="92f83-174">Der Pfad zu der Projektdatei, mit der gearbeitet werden soll.</span><span class="sxs-lookup"><span data-stu-id="92f83-174">The path to the project file to operate on.</span></span> <span data-ttu-id="92f83-175">Wenn keine Datei angegeben ist, sucht der Befehl im aktuellen Verzeichnis nach einer Projektdatei.</span><span class="sxs-lookup"><span data-stu-id="92f83-175">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="92f83-176">-S</span><span class="sxs-lookup"><span data-stu-id="92f83-176">-s</span></span> | <span data-ttu-id="92f83-177">--services</span><span class="sxs-lookup"><span data-stu-id="92f83-177">--services</span></span> | <span data-ttu-id="92f83-178">Der Typ der gRPC-Dienste, die generiert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="92f83-178">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="92f83-179">Wenn `Default` angegeben ist, wird `Both` für Webprojekte und `Client` für Nicht-Webprojekte verwendet.</span><span class="sxs-lookup"><span data-stu-id="92f83-179">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="92f83-180">Zulässige Werte sind `Both`, `Client`, `Default`, `None`, `Server`.</span><span class="sxs-lookup"><span data-stu-id="92f83-180">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="92f83-181">-i</span><span class="sxs-lookup"><span data-stu-id="92f83-181">-i</span></span> | <span data-ttu-id="92f83-182">--additional-import-dirs</span><span class="sxs-lookup"><span data-stu-id="92f83-182">--additional-import-dirs</span></span> | <span data-ttu-id="92f83-183">Zusätzliche Verzeichnisse, die bei der Auflösung von Importen für die Protobuf-Dateien verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="92f83-183">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="92f83-184">Dies ist eine durch Semikolons getrennte Liste von Pfaden.</span><span class="sxs-lookup"><span data-stu-id="92f83-184">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="92f83-185">--access</span><span class="sxs-lookup"><span data-stu-id="92f83-185">--access</span></span> | <span data-ttu-id="92f83-186">Der Zugriffsmodifizierer, der für die generierten C#-Klassen verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="92f83-186">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="92f83-187">Der Standardwert ist `Public`sein.</span><span class="sxs-lookup"><span data-stu-id="92f83-187">Default value is `Public`.</span></span> <span data-ttu-id="92f83-188">Zulässige Werte sind `Internal` und `Public`.</span><span class="sxs-lookup"><span data-stu-id="92f83-188">Accepted values are `Internal` and `Public`.</span></span>

## <a name="remove"></a><span data-ttu-id="92f83-189">Remove</span><span class="sxs-lookup"><span data-stu-id="92f83-189">Remove</span></span>

<span data-ttu-id="92f83-190">Der Befehl `remove` wird verwendet, um Protobuf-Verweise aus der *CSPROJ*-Datei zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="92f83-190">The `remove` command is used to remove Protobuf references from the *.csproj* file.</span></span> <span data-ttu-id="92f83-191">Der Befehl akzeptiert Pfadargumente und Quell-URLs als Argumente.</span><span class="sxs-lookup"><span data-stu-id="92f83-191">The command accepts path arguments and source URLs as arguments.</span></span> <span data-ttu-id="92f83-192">Folgendes gilt für das Tool:</span><span class="sxs-lookup"><span data-stu-id="92f83-192">The tool:</span></span>

* <span data-ttu-id="92f83-193">Es entfernt nur den Protobuf-Verweis.</span><span class="sxs-lookup"><span data-stu-id="92f83-193">Only removes the Protobuf reference.</span></span>
* <span data-ttu-id="92f83-194">Es löscht nicht die *PROTO*-Datei, auch wenn sie ursprünglich von einer Remote-URL heruntergeladen wurde.</span><span class="sxs-lookup"><span data-stu-id="92f83-194">Does not delete the *.proto* file, even if it was originally downloaded from a remote URL.</span></span>

### <a name="usage"></a><span data-ttu-id="92f83-195">Verwendung</span><span class="sxs-lookup"><span data-stu-id="92f83-195">Usage</span></span>

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a><span data-ttu-id="92f83-196">Argumente</span><span class="sxs-lookup"><span data-stu-id="92f83-196">Arguments</span></span>

| <span data-ttu-id="92f83-197">Argument</span><span class="sxs-lookup"><span data-stu-id="92f83-197">Argument</span></span> | <span data-ttu-id="92f83-198">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="92f83-198">Description</span></span> |
|-|-|
| <span data-ttu-id="92f83-199">Referenzen</span><span class="sxs-lookup"><span data-stu-id="92f83-199">references</span></span> | <span data-ttu-id="92f83-200">Die URLs oder Dateipfade der zu entfernenden Protobuf-Verweise.</span><span class="sxs-lookup"><span data-stu-id="92f83-200">The URLs or file paths of the protobuf references to remove.</span></span> |

### <a name="options"></a><span data-ttu-id="92f83-201">Optionen</span><span class="sxs-lookup"><span data-stu-id="92f83-201">Options</span></span>

| <span data-ttu-id="92f83-202">Kurze Option</span><span class="sxs-lookup"><span data-stu-id="92f83-202">Short option</span></span> | <span data-ttu-id="92f83-203">Lange Option</span><span class="sxs-lookup"><span data-stu-id="92f83-203">Long option</span></span> | <span data-ttu-id="92f83-204">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="92f83-204">Description</span></span> |
|-|-|-|
| <span data-ttu-id="92f83-205">-p</span><span class="sxs-lookup"><span data-stu-id="92f83-205">-p</span></span> | <span data-ttu-id="92f83-206">--project</span><span class="sxs-lookup"><span data-stu-id="92f83-206">--project</span></span> | <span data-ttu-id="92f83-207">Der Pfad zu der Projektdatei, mit der gearbeitet werden soll.</span><span class="sxs-lookup"><span data-stu-id="92f83-207">The path to the project file to operate on.</span></span> <span data-ttu-id="92f83-208">Wenn keine Datei angegeben ist, sucht der Befehl im aktuellen Verzeichnis nach einer Projektdatei.</span><span class="sxs-lookup"><span data-stu-id="92f83-208">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="refresh"></a><span data-ttu-id="92f83-209">Aktualisieren</span><span class="sxs-lookup"><span data-stu-id="92f83-209">Refresh</span></span>

<span data-ttu-id="92f83-210">Der Befehl `refresh` wird verwendet, um einen Remoteverweis mit dem neuesten Inhalt von der Quell-URL zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="92f83-210">The `refresh` command is used to update a remote reference with the latest content from the source URL.</span></span> <span data-ttu-id="92f83-211">Sowohl der Pfad der Downloaddatei als auch die Quell-URL können zur Angabe des zu aktualisierenden Verweises verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="92f83-211">Both the download file path and the source URL can be used to specify the reference to be updated.</span></span> <span data-ttu-id="92f83-212">Hinweis:</span><span class="sxs-lookup"><span data-stu-id="92f83-212">Note:</span></span>

* <span data-ttu-id="92f83-213">Die Hashes des Dateiinhalts werden verglichen, um festzustellen, ob die lokale Datei aktualisiert werden sollte.</span><span class="sxs-lookup"><span data-stu-id="92f83-213">The hashes of the file contents are compared to determine whether the local file should be updated.</span></span>
* <span data-ttu-id="92f83-214">Es werden keine Zeitstempelinformationen verglichen.</span><span class="sxs-lookup"><span data-stu-id="92f83-214">No timestamp information is compared.</span></span>

<span data-ttu-id="92f83-215">Das Tool ersetzt immer die lokale Datei durch die Remotedatei, wenn eine Aktualisierung erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="92f83-215">The tool always replaces the local file with the remote file if an update is needed.</span></span>

### <a name="usage"></a><span data-ttu-id="92f83-216">Verwendung</span><span class="sxs-lookup"><span data-stu-id="92f83-216">Usage</span></span>

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a><span data-ttu-id="92f83-217">Argumente</span><span class="sxs-lookup"><span data-stu-id="92f83-217">Arguments</span></span>

| <span data-ttu-id="92f83-218">Argument</span><span class="sxs-lookup"><span data-stu-id="92f83-218">Argument</span></span> | <span data-ttu-id="92f83-219">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="92f83-219">Description</span></span> |
|-|-|
| <span data-ttu-id="92f83-220">Referenzen</span><span class="sxs-lookup"><span data-stu-id="92f83-220">references</span></span> | <span data-ttu-id="92f83-221">Die URLs oder Dateipfade zu Protobuf-Remoteverweisen, die aktualisiert werden sollten.</span><span class="sxs-lookup"><span data-stu-id="92f83-221">The URLs or file paths to remote protobuf references that should be updated.</span></span> <span data-ttu-id="92f83-222">Lassen Sie dieses Argument leer, um alle Remoteverweise zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="92f83-222">Leave this argument empty to refresh all remote references.</span></span> |

### <a name="options"></a><span data-ttu-id="92f83-223">Optionen</span><span class="sxs-lookup"><span data-stu-id="92f83-223">Options</span></span>

| <span data-ttu-id="92f83-224">Kurze Option</span><span class="sxs-lookup"><span data-stu-id="92f83-224">Short option</span></span> | <span data-ttu-id="92f83-225">Lange Option</span><span class="sxs-lookup"><span data-stu-id="92f83-225">Long option</span></span> | <span data-ttu-id="92f83-226">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="92f83-226">Description</span></span> |
|-|-|-|
| <span data-ttu-id="92f83-227">-p</span><span class="sxs-lookup"><span data-stu-id="92f83-227">-p</span></span> | <span data-ttu-id="92f83-228">--project</span><span class="sxs-lookup"><span data-stu-id="92f83-228">--project</span></span> | <span data-ttu-id="92f83-229">Der Pfad zu der Projektdatei, mit der gearbeitet werden soll.</span><span class="sxs-lookup"><span data-stu-id="92f83-229">The path to the project file to operate on.</span></span> <span data-ttu-id="92f83-230">Wenn keine Datei angegeben ist, sucht der Befehl im aktuellen Verzeichnis nach einer Projektdatei.</span><span class="sxs-lookup"><span data-stu-id="92f83-230">If a file is not specified, the command searches the current directory for one.</span></span>
| | <span data-ttu-id="92f83-231">--dry-run</span><span class="sxs-lookup"><span data-stu-id="92f83-231">--dry-run</span></span> | <span data-ttu-id="92f83-232">Gibt eine Liste von Dateien aus, die ohne Herunterladen neuer Inhalte aktualisiert werden würden.</span><span class="sxs-lookup"><span data-stu-id="92f83-232">Outputs a list of files that would be updated without downloading any new content.</span></span>

## <a name="list"></a><span data-ttu-id="92f83-233">Liste</span><span class="sxs-lookup"><span data-stu-id="92f83-233">List</span></span>

<span data-ttu-id="92f83-234">Der Befehl `list` wird verwendet, um alle Protobuf-Verweise in der Projektdatei anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="92f83-234">The `list` command is used to display all the Protobuf references in the project file.</span></span> <span data-ttu-id="92f83-235">Wenn alle Werte einer Spalte Standardwerte sind, kann die Spalte ausgelassen werden.</span><span class="sxs-lookup"><span data-stu-id="92f83-235">If all values of a column are default values, the column may be omitted.</span></span>

### <a name="usage"></a><span data-ttu-id="92f83-236">Verwendung</span><span class="sxs-lookup"><span data-stu-id="92f83-236">Usage</span></span>

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a><span data-ttu-id="92f83-237">Optionen</span><span class="sxs-lookup"><span data-stu-id="92f83-237">Options</span></span>

| <span data-ttu-id="92f83-238">Kurze Option</span><span class="sxs-lookup"><span data-stu-id="92f83-238">Short option</span></span> | <span data-ttu-id="92f83-239">Lange Option</span><span class="sxs-lookup"><span data-stu-id="92f83-239">Long option</span></span> | <span data-ttu-id="92f83-240">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="92f83-240">Description</span></span> |
|-|-|-|
| <span data-ttu-id="92f83-241">-p</span><span class="sxs-lookup"><span data-stu-id="92f83-241">-p</span></span> | <span data-ttu-id="92f83-242">--project</span><span class="sxs-lookup"><span data-stu-id="92f83-242">--project</span></span> | <span data-ttu-id="92f83-243">Der Pfad zu der Projektdatei, mit der gearbeitet werden soll.</span><span class="sxs-lookup"><span data-stu-id="92f83-243">The path to the project file to operate on.</span></span> <span data-ttu-id="92f83-244">Wenn keine Datei angegeben ist, sucht der Befehl im aktuellen Verzeichnis nach einer Projektdatei.</span><span class="sxs-lookup"><span data-stu-id="92f83-244">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="92f83-245">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="92f83-245">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
