---
title: Verwalten von Protobuf-Verweisen mit dotnet-grpc
author: juntaoluo
description: Erfahren Sie mehr über das Hinzufügen, aktualisieren, entfernen und Auflisten von protobuf-verweisen mit dem globalen Tool dotnet-GrpC.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
uid: grpc/dotnet-grpc
ms.openlocfilehash: 994597c854a95bb33de1686ab025cb3744cf6845
ms.sourcegitcommit: e71b6a85b0e94a600af607107e298f932924c849
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72519037"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a><span data-ttu-id="431c7-103">Verwalten von Protobuf-Verweisen mit dotnet-grpc</span><span class="sxs-lookup"><span data-stu-id="431c7-103">Manage Protobuf references with dotnet-grpc</span></span>

<span data-ttu-id="431c7-104">Von [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="431c7-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="431c7-105">`dotnet-grpc` ist ein globales .net Core-Tool zum Verwalten von [protobuf-verweisen ( *. proto*)](xref:grpc/basics#proto-file) in einem .net-GrpC-Projekt.</span><span class="sxs-lookup"><span data-stu-id="431c7-105">`dotnet-grpc` is a .NET Core Global Tool for managing [Protobuf (*.proto*)](xref:grpc/basics#proto-file) references within a .NET gRPC project.</span></span> <span data-ttu-id="431c7-106">Das Tool kann zum Hinzufügen, aktualisieren, entfernen und Auflisten von protobuf-verweisen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="431c7-106">The tool can be used to add, refresh, remove, and list Protobuf references.</span></span>

## <a name="installation"></a><span data-ttu-id="431c7-107">Installation</span><span class="sxs-lookup"><span data-stu-id="431c7-107">Installation</span></span>

<span data-ttu-id="431c7-108">Führen Sie den folgenden Befehl aus, um das [.net Core-Tool](/dotnet/core/tools/global-tools)"`dotnet-grpc`" zu installieren:</span><span class="sxs-lookup"><span data-stu-id="431c7-108">To install the `dotnet-grpc` [.NET Core Global Tool](/dotnet/core/tools/global-tools), run the following command:</span></span>

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a><span data-ttu-id="431c7-109">Hinzufügen von Verweisen</span><span class="sxs-lookup"><span data-stu-id="431c7-109">Add references</span></span>

<span data-ttu-id="431c7-110">`dotnet-grpc` kann verwendet werden, um protobuf-Verweise als `<Protobuf />`-Elemente der *csproj* -Datei hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="431c7-110">`dotnet-grpc` can be used to add Protobuf references as `<Protobuf />` items to the *.csproj* file:</span></span>

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

<span data-ttu-id="431c7-111">Die protobuf-Verweise werden verwendet, um C# die Client-und/oder Server Objekte zu generieren.</span><span class="sxs-lookup"><span data-stu-id="431c7-111">The Protobuf references are used to generate the C# client and/or server assets.</span></span> <span data-ttu-id="431c7-112">Das Tool "`dotnet-grpc`" kann folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="431c7-112">The `dotnet-grpc` tool can:</span></span>

* <span data-ttu-id="431c7-113">Erstellen Sie einen protobuf-Verweis aus lokalen Dateien auf dem Datenträger.</span><span class="sxs-lookup"><span data-stu-id="431c7-113">Create a Protobuf reference from local files on disk.</span></span>
* <span data-ttu-id="431c7-114">Erstellen Sie einen protobuf-Verweis aus einer Remote Datei, die durch eine URL angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="431c7-114">Create a Protobuf reference from a remote file specified by a URL.</span></span>
* <span data-ttu-id="431c7-115">Stellen Sie sicher, dass dem Projekt die richtigen GrpC-Paketabhängigkeiten hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="431c7-115">Ensure the correct gRPC package dependencies are added to the project.</span></span>

<span data-ttu-id="431c7-116">Beispielsweise wird das Paket "`Grpc.AspNetCore`" einer Web-App hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="431c7-116">For example, the `Grpc.AspNetCore` package is added to a web app.</span></span> <span data-ttu-id="431c7-117">`Grpc.AspNetCore` enthält Unterstützung für GrpC-Server und Client Bibliotheken und-Tools.</span><span class="sxs-lookup"><span data-stu-id="431c7-117">`Grpc.AspNetCore` contains gRPC server and client libraries and tooling support.</span></span> <span data-ttu-id="431c7-118">Alternativ dazu werden die Pakete "`Grpc.Net.Client`", "`Grpc.Tools`" und "`Google.Protobuf`", die nur die Unterstützung von GrpC-Client Bibliotheken und-Tools enthalten, einer Konsolen-app hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="431c7-118">Alternatively, the `Grpc.Net.Client`, `Grpc.Tools` and `Google.Protobuf` packages, which contain only the gRPC client libraries and tooling support, are added to a Console app.</span></span>

### <a name="add-file"></a><span data-ttu-id="431c7-119">Datei hinzufügen</span><span class="sxs-lookup"><span data-stu-id="431c7-119">Add file</span></span>

<span data-ttu-id="431c7-120">Der Befehl "`add-file`" wird verwendet, um lokale Dateien auf dem Datenträger als protobuf-Verweise hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="431c7-120">The `add-file` command is used to add local files on disk as Protobuf references.</span></span> <span data-ttu-id="431c7-121">Die bereitgestellten Dateipfade:</span><span class="sxs-lookup"><span data-stu-id="431c7-121">The file paths provided:</span></span>

* <span data-ttu-id="431c7-122">Kann relativ zum aktuellen Verzeichnis oder zu absoluten Pfaden sein.</span><span class="sxs-lookup"><span data-stu-id="431c7-122">Can be relative to the current directory or absolute paths.</span></span>
* <span data-ttu-id="431c7-123">Kann Platzhalter für die Musterbasierte Datei [Globalisierung](https://wikipedia.org/wiki/Glob_(programming))enthalten.</span><span class="sxs-lookup"><span data-stu-id="431c7-123">May contain wild cards for pattern-based file [globbing](https://wikipedia.org/wiki/Glob_(programming)).</span></span>

<span data-ttu-id="431c7-124">Wenn Dateien außerhalb des Projektverzeichnisses liegen, wird ein `Link`-Element hinzugefügt, um die Datei unter dem Ordner `Protos` in Visual Studio anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="431c7-124">If any files are outside the project directory, a `Link` element is added to display the file under the folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="431c7-125">Verwendung</span><span class="sxs-lookup"><span data-stu-id="431c7-125">Usage</span></span>

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a><span data-ttu-id="431c7-126">Argumente</span><span class="sxs-lookup"><span data-stu-id="431c7-126">Arguments</span></span>

| <span data-ttu-id="431c7-127">Argument</span><span class="sxs-lookup"><span data-stu-id="431c7-127">Argument</span></span> | <span data-ttu-id="431c7-128">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="431c7-128">Description</span></span> |
|-|-|
| <span data-ttu-id="431c7-129">Dateien</span><span class="sxs-lookup"><span data-stu-id="431c7-129">files</span></span> | <span data-ttu-id="431c7-130">Die protobuf-Datei Verweise.</span><span class="sxs-lookup"><span data-stu-id="431c7-130">The protobuf file references.</span></span> <span data-ttu-id="431c7-131">Hierbei kann es sich um einen Pfad zu globmuster für lokale protobuf-Dateien handeln.</span><span class="sxs-lookup"><span data-stu-id="431c7-131">These can be a path to glob for local protobuf files.</span></span> |

#### <a name="options"></a><span data-ttu-id="431c7-132">Optionen</span><span class="sxs-lookup"><span data-stu-id="431c7-132">Options</span></span>

| <span data-ttu-id="431c7-133">Kurze Option</span><span class="sxs-lookup"><span data-stu-id="431c7-133">Short option</span></span> | <span data-ttu-id="431c7-134">Lange Option</span><span class="sxs-lookup"><span data-stu-id="431c7-134">Long option</span></span> | <span data-ttu-id="431c7-135">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="431c7-135">Description</span></span> |
|-|-|-|
| <span data-ttu-id="431c7-136">-p</span><span class="sxs-lookup"><span data-stu-id="431c7-136">-p</span></span> | <span data-ttu-id="431c7-137">--Projekt</span><span class="sxs-lookup"><span data-stu-id="431c7-137">--project</span></span> | <span data-ttu-id="431c7-138">Der Pfad zu der Projektdatei, die verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="431c7-138">The path to the project file to operate on.</span></span> <span data-ttu-id="431c7-139">Wenn keine Datei angegeben ist, durchsucht der Befehl das aktuelle Verzeichnis nach einem.</span><span class="sxs-lookup"><span data-stu-id="431c7-139">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="431c7-140">-s</span><span class="sxs-lookup"><span data-stu-id="431c7-140">-s</span></span> | <span data-ttu-id="431c7-141">--Dienste</span><span class="sxs-lookup"><span data-stu-id="431c7-141">--services</span></span> | <span data-ttu-id="431c7-142">Der Typ der GrpC-Dienste, die generiert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="431c7-142">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="431c7-143">Wenn `Default` angegeben wird, wird `Both` für Webprojekte verwendet, und `Client` wird für nicht-Webprojekte verwendet.</span><span class="sxs-lookup"><span data-stu-id="431c7-143">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="431c7-144">Akzeptierte Werte sind `Both`, `Client`, `Default`, `None`, `Server`.</span><span class="sxs-lookup"><span data-stu-id="431c7-144">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="431c7-145">-i</span><span class="sxs-lookup"><span data-stu-id="431c7-145">-i</span></span> | <span data-ttu-id="431c7-146">--Weitere Import-dirs</span><span class="sxs-lookup"><span data-stu-id="431c7-146">--additional-import-dirs</span></span> | <span data-ttu-id="431c7-147">Zusätzliche Verzeichnisse, die beim Auflösen von Importen für die protobuf-Dateien verwendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="431c7-147">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="431c7-148">Dies ist eine durch Semikolons getrennte Liste von Pfaden.</span><span class="sxs-lookup"><span data-stu-id="431c7-148">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="431c7-149">--Zugriff</span><span class="sxs-lookup"><span data-stu-id="431c7-149">--access</span></span> | <span data-ttu-id="431c7-150">Der Zugriffsmodifizierer für C# die generierten Klassen.</span><span class="sxs-lookup"><span data-stu-id="431c7-150">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="431c7-151">Der Standardwert ist `Public`sein.</span><span class="sxs-lookup"><span data-stu-id="431c7-151">The default value is `Public`.</span></span> <span data-ttu-id="431c7-152">Akzeptierte Werte sind `Internal` und `Public`.</span><span class="sxs-lookup"><span data-stu-id="431c7-152">Accepted values are `Internal` and `Public`.</span></span>

### <a name="add-url"></a><span data-ttu-id="431c7-153">URL hinzufügen</span><span class="sxs-lookup"><span data-stu-id="431c7-153">Add URL</span></span>

<span data-ttu-id="431c7-154">Der Befehl "`add-url`" wird verwendet, um eine durch eine Quell-URL als protobuf-Referenz angegebene Remote Datei hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="431c7-154">The `add-url` command is used to add a remote file specified by an source URL as Protobuf reference.</span></span> <span data-ttu-id="431c7-155">Ein Dateipfad muss angegeben werden, um anzugeben, wo die Remote Datei heruntergeladen werden soll.</span><span class="sxs-lookup"><span data-stu-id="431c7-155">A file path must be provided to specify where to download the remote file.</span></span> <span data-ttu-id="431c7-156">Der Dateipfad kann relativ zum aktuellen Verzeichnis oder als absoluter Pfad sein.</span><span class="sxs-lookup"><span data-stu-id="431c7-156">The file path can be relative to the current directory or an absolute path.</span></span> <span data-ttu-id="431c7-157">Wenn sich der Dateipfad außerhalb des Projektverzeichnisses befindet, wird ein `Link`-Element hinzugefügt, um die Datei unter dem virtuellen Ordner `Protos` in Visual Studio anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="431c7-157">If the file path is outside the project directory, a `Link` element is added to display the file under the virtual folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="431c7-158">Verwendung</span><span class="sxs-lookup"><span data-stu-id="431c7-158">Usage</span></span>

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a><span data-ttu-id="431c7-159">Argumente</span><span class="sxs-lookup"><span data-stu-id="431c7-159">Arguments</span></span>

| <span data-ttu-id="431c7-160">Argument</span><span class="sxs-lookup"><span data-stu-id="431c7-160">Argument</span></span> | <span data-ttu-id="431c7-161">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="431c7-161">Description</span></span> |
|-|-|
| <span data-ttu-id="431c7-162">url</span><span class="sxs-lookup"><span data-stu-id="431c7-162">url</span></span> | <span data-ttu-id="431c7-163">Die URL zu einer Remote-protobuf-Datei.</span><span class="sxs-lookup"><span data-stu-id="431c7-163">The URL to a remote protobuf file.</span></span> |

#### <a name="options"></a><span data-ttu-id="431c7-164">Optionen</span><span class="sxs-lookup"><span data-stu-id="431c7-164">Options</span></span>

| <span data-ttu-id="431c7-165">Kurze Option</span><span class="sxs-lookup"><span data-stu-id="431c7-165">Short option</span></span> | <span data-ttu-id="431c7-166">Lange Option</span><span class="sxs-lookup"><span data-stu-id="431c7-166">Long option</span></span> | <span data-ttu-id="431c7-167">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="431c7-167">Description</span></span> |
|-|-|-|
| <span data-ttu-id="431c7-168">-o</span><span class="sxs-lookup"><span data-stu-id="431c7-168">-o</span></span> | <span data-ttu-id="431c7-169">--Ausgabe</span><span class="sxs-lookup"><span data-stu-id="431c7-169">--output</span></span> | <span data-ttu-id="431c7-170">Gibt den Download Pfad für die Remote-protobuf-Datei an.</span><span class="sxs-lookup"><span data-stu-id="431c7-170">Specifies the download path for the remote protobuf file.</span></span> <span data-ttu-id="431c7-171">Diese Option muss angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="431c7-171">This is a required option.</span></span>
| <span data-ttu-id="431c7-172">-p</span><span class="sxs-lookup"><span data-stu-id="431c7-172">-p</span></span> | <span data-ttu-id="431c7-173">--Projekt</span><span class="sxs-lookup"><span data-stu-id="431c7-173">--project</span></span> | <span data-ttu-id="431c7-174">Der Pfad zu der Projektdatei, die verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="431c7-174">The path to the project file to operate on.</span></span> <span data-ttu-id="431c7-175">Wenn keine Datei angegeben ist, durchsucht der Befehl das aktuelle Verzeichnis nach einem.</span><span class="sxs-lookup"><span data-stu-id="431c7-175">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="431c7-176">-s</span><span class="sxs-lookup"><span data-stu-id="431c7-176">-s</span></span> | <span data-ttu-id="431c7-177">--Dienste</span><span class="sxs-lookup"><span data-stu-id="431c7-177">--services</span></span> | <span data-ttu-id="431c7-178">Der Typ der GrpC-Dienste, die generiert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="431c7-178">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="431c7-179">Wenn `Default` angegeben wird, wird `Both` für Webprojekte verwendet, und `Client` wird für nicht-Webprojekte verwendet.</span><span class="sxs-lookup"><span data-stu-id="431c7-179">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="431c7-180">Akzeptierte Werte sind `Both`, `Client`, `Default`, `None`, `Server`.</span><span class="sxs-lookup"><span data-stu-id="431c7-180">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="431c7-181">-i</span><span class="sxs-lookup"><span data-stu-id="431c7-181">-i</span></span> | <span data-ttu-id="431c7-182">--Weitere Import-dirs</span><span class="sxs-lookup"><span data-stu-id="431c7-182">--additional-import-dirs</span></span> | <span data-ttu-id="431c7-183">Zusätzliche Verzeichnisse, die beim Auflösen von Importen für die protobuf-Dateien verwendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="431c7-183">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="431c7-184">Dies ist eine durch Semikolons getrennte Liste von Pfaden.</span><span class="sxs-lookup"><span data-stu-id="431c7-184">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="431c7-185">--Zugriff</span><span class="sxs-lookup"><span data-stu-id="431c7-185">--access</span></span> | <span data-ttu-id="431c7-186">Der Zugriffsmodifizierer für C# die generierten Klassen.</span><span class="sxs-lookup"><span data-stu-id="431c7-186">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="431c7-187">Der Standardwert ist `Public`sein.</span><span class="sxs-lookup"><span data-stu-id="431c7-187">Default value is `Public`.</span></span> <span data-ttu-id="431c7-188">Akzeptierte Werte sind `Internal` und `Public`.</span><span class="sxs-lookup"><span data-stu-id="431c7-188">Accepted values are `Internal` and `Public`.</span></span>

## <a name="remove"></a><span data-ttu-id="431c7-189">Remove</span><span class="sxs-lookup"><span data-stu-id="431c7-189">Remove</span></span>

<span data-ttu-id="431c7-190">Der Befehl "`remove`" wird verwendet, um protobuf-Verweise aus der *csproj* -Datei zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="431c7-190">The `remove` command is used to remove Protobuf references from the *.csproj* file.</span></span> <span data-ttu-id="431c7-191">Der Befehl akzeptiert Pfad Argumente und Quell-URLs als Argumente.</span><span class="sxs-lookup"><span data-stu-id="431c7-191">The command accepts path arguments and source URLs as arguments.</span></span> <span data-ttu-id="431c7-192">Das Tool:</span><span class="sxs-lookup"><span data-stu-id="431c7-192">The tool:</span></span>

* <span data-ttu-id="431c7-193">Entfernt nur den protobuf-Verweis.</span><span class="sxs-lookup"><span data-stu-id="431c7-193">Only removes the Protobuf reference.</span></span>
* <span data-ttu-id="431c7-194">Die *. proto* -Datei wird nicht gelöscht, auch wenn Sie ursprünglich von einer Remote-URL heruntergeladen wurde.</span><span class="sxs-lookup"><span data-stu-id="431c7-194">Does not delete the *.proto* file, even if it was originally downloaded from a remote URL.</span></span>

### <a name="usage"></a><span data-ttu-id="431c7-195">Verwendung</span><span class="sxs-lookup"><span data-stu-id="431c7-195">Usage</span></span>

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a><span data-ttu-id="431c7-196">Argumente</span><span class="sxs-lookup"><span data-stu-id="431c7-196">Arguments</span></span>

| <span data-ttu-id="431c7-197">Argument</span><span class="sxs-lookup"><span data-stu-id="431c7-197">Argument</span></span> | <span data-ttu-id="431c7-198">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="431c7-198">Description</span></span> |
|-|-|
| <span data-ttu-id="431c7-199">Referenzen</span><span class="sxs-lookup"><span data-stu-id="431c7-199">references</span></span> | <span data-ttu-id="431c7-200">Die URLs oder Dateipfade der zu entfernenden protobuf-Verweise.</span><span class="sxs-lookup"><span data-stu-id="431c7-200">The URLs or file paths of the protobuf references to remove.</span></span> |

### <a name="options"></a><span data-ttu-id="431c7-201">Optionen</span><span class="sxs-lookup"><span data-stu-id="431c7-201">Options</span></span>

| <span data-ttu-id="431c7-202">Kurze Option</span><span class="sxs-lookup"><span data-stu-id="431c7-202">Short option</span></span> | <span data-ttu-id="431c7-203">Lange Option</span><span class="sxs-lookup"><span data-stu-id="431c7-203">Long option</span></span> | <span data-ttu-id="431c7-204">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="431c7-204">Description</span></span> |
|-|-|-|
| <span data-ttu-id="431c7-205">-p</span><span class="sxs-lookup"><span data-stu-id="431c7-205">-p</span></span> | <span data-ttu-id="431c7-206">--Projekt</span><span class="sxs-lookup"><span data-stu-id="431c7-206">--project</span></span> | <span data-ttu-id="431c7-207">Der Pfad zu der Projektdatei, die verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="431c7-207">The path to the project file to operate on.</span></span> <span data-ttu-id="431c7-208">Wenn keine Datei angegeben ist, durchsucht der Befehl das aktuelle Verzeichnis nach einem.</span><span class="sxs-lookup"><span data-stu-id="431c7-208">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="refresh"></a><span data-ttu-id="431c7-209">Aktualisieren</span><span class="sxs-lookup"><span data-stu-id="431c7-209">Refresh</span></span>

<span data-ttu-id="431c7-210">Der Befehl "`refresh`" wird zum Aktualisieren eines Remote Verweises mit dem neuesten Inhalt aus der Quell-URL verwendet.</span><span class="sxs-lookup"><span data-stu-id="431c7-210">The `refresh` command is used to update a remote reference with the latest content from the source URL.</span></span> <span data-ttu-id="431c7-211">Sowohl der Download Dateipfad als auch die Quell-URL können zum Angeben des zu aktualisierenden Verweises verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="431c7-211">Both the download file path and the source URL can be used to specify the reference to be updated.</span></span> <span data-ttu-id="431c7-212">Hinweis:</span><span class="sxs-lookup"><span data-stu-id="431c7-212">Note:</span></span>

* <span data-ttu-id="431c7-213">Die Hashwerte für den Dateiinhalt werden verglichen, um zu bestimmen, ob die lokale Datei aktualisiert werden soll.</span><span class="sxs-lookup"><span data-stu-id="431c7-213">The hashes of the file contents are compared to determine whether the local file should be updated.</span></span>
* <span data-ttu-id="431c7-214">Es werden keine Zeitstempel Informationen verglichen.</span><span class="sxs-lookup"><span data-stu-id="431c7-214">No timestamp information is compared.</span></span>

<span data-ttu-id="431c7-215">Wenn ein Update erforderlich ist, ersetzt das Tool immer die lokale Datei durch die Remote Datei.</span><span class="sxs-lookup"><span data-stu-id="431c7-215">The tool always replaces the local file with the remote file if an update is needed.</span></span>

### <a name="usage"></a><span data-ttu-id="431c7-216">Verwendung</span><span class="sxs-lookup"><span data-stu-id="431c7-216">Usage</span></span>

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a><span data-ttu-id="431c7-217">Argumente</span><span class="sxs-lookup"><span data-stu-id="431c7-217">Arguments</span></span>

| <span data-ttu-id="431c7-218">Argument</span><span class="sxs-lookup"><span data-stu-id="431c7-218">Argument</span></span> | <span data-ttu-id="431c7-219">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="431c7-219">Description</span></span> |
|-|-|
| <span data-ttu-id="431c7-220">Referenzen</span><span class="sxs-lookup"><span data-stu-id="431c7-220">references</span></span> | <span data-ttu-id="431c7-221">Die URLs oder Dateipfade zu Remote-protobuf-verweisen, die aktualisiert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="431c7-221">The URLs or file paths to remote protobuf references that should be updated.</span></span> <span data-ttu-id="431c7-222">Lassen Sie dieses Argument leer, um alle Remote Verweise zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="431c7-222">Leave this argument empty to refresh all remote references.</span></span> |

### <a name="options"></a><span data-ttu-id="431c7-223">Optionen</span><span class="sxs-lookup"><span data-stu-id="431c7-223">Options</span></span>

| <span data-ttu-id="431c7-224">Kurze Option</span><span class="sxs-lookup"><span data-stu-id="431c7-224">Short option</span></span> | <span data-ttu-id="431c7-225">Lange Option</span><span class="sxs-lookup"><span data-stu-id="431c7-225">Long option</span></span> | <span data-ttu-id="431c7-226">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="431c7-226">Description</span></span> |
|-|-|-|
| <span data-ttu-id="431c7-227">-p</span><span class="sxs-lookup"><span data-stu-id="431c7-227">-p</span></span> | <span data-ttu-id="431c7-228">--Projekt</span><span class="sxs-lookup"><span data-stu-id="431c7-228">--project</span></span> | <span data-ttu-id="431c7-229">Der Pfad zu der Projektdatei, die verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="431c7-229">The path to the project file to operate on.</span></span> <span data-ttu-id="431c7-230">Wenn keine Datei angegeben ist, durchsucht der Befehl das aktuelle Verzeichnis nach einem.</span><span class="sxs-lookup"><span data-stu-id="431c7-230">If a file is not specified, the command searches the current directory for one.</span></span>
| | <span data-ttu-id="431c7-231">--Dry-Run</span><span class="sxs-lookup"><span data-stu-id="431c7-231">--dry-run</span></span> | <span data-ttu-id="431c7-232">Gibt eine Liste der Dateien aus, die aktualisiert werden, ohne dass neue Inhalte heruntergeladen werden.</span><span class="sxs-lookup"><span data-stu-id="431c7-232">Outputs a list of files that would be updated without downloading any new content.</span></span>

## <a name="list"></a><span data-ttu-id="431c7-233">Liste</span><span class="sxs-lookup"><span data-stu-id="431c7-233">List</span></span>

<span data-ttu-id="431c7-234">Der Befehl "`list`" wird verwendet, um alle protobuf-Verweise in der Projektdatei anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="431c7-234">The `list` command is used to display all the Protobuf references in the project file.</span></span> <span data-ttu-id="431c7-235">Wenn alle Werte einer Spalte Standardwerte sind, darf die Spalte weggelassen werden.</span><span class="sxs-lookup"><span data-stu-id="431c7-235">If all values of a column are default values, the column may be omitted.</span></span>

### <a name="usage"></a><span data-ttu-id="431c7-236">Verwendung</span><span class="sxs-lookup"><span data-stu-id="431c7-236">Usage</span></span>

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a><span data-ttu-id="431c7-237">Optionen</span><span class="sxs-lookup"><span data-stu-id="431c7-237">Options</span></span>

| <span data-ttu-id="431c7-238">Kurze Option</span><span class="sxs-lookup"><span data-stu-id="431c7-238">Short option</span></span> | <span data-ttu-id="431c7-239">Lange Option</span><span class="sxs-lookup"><span data-stu-id="431c7-239">Long option</span></span> | <span data-ttu-id="431c7-240">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="431c7-240">Description</span></span> |
|-|-|-|
| <span data-ttu-id="431c7-241">-p</span><span class="sxs-lookup"><span data-stu-id="431c7-241">-p</span></span> | <span data-ttu-id="431c7-242">--Projekt</span><span class="sxs-lookup"><span data-stu-id="431c7-242">--project</span></span> | <span data-ttu-id="431c7-243">Der Pfad zu der Projektdatei, die verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="431c7-243">The path to the project file to operate on.</span></span> <span data-ttu-id="431c7-244">Wenn keine Datei angegeben ist, durchsucht der Befehl das aktuelle Verzeichnis nach einem.</span><span class="sxs-lookup"><span data-stu-id="431c7-244">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="431c7-245">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="431c7-245">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
