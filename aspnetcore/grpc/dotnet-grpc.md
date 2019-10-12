---
title: Verwalten von protobuf-verweisen mit dotnet-GrpC
author: juntaoluo
description: Erfahren Sie mehr über das Hinzufügen, aktualisieren, entfernen und Auflisten von protobuf-verweisen mit dem globalen Tool dotnet-GrpC.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/24/2019
uid: grpc/dotnet-grpc
ms.openlocfilehash: ebd57419be24f7f4ed9765e36cf14189be8438b1
ms.sourcegitcommit: 020c3760492efed71b19e476f25392dda5dd7388
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2019
ms.locfileid: "72290046"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a><span data-ttu-id="10afb-103">Verwalten von protobuf-verweisen mit dotnet-GrpC</span><span class="sxs-lookup"><span data-stu-id="10afb-103">Manage Protobuf references with dotnet-grpc</span></span>

<span data-ttu-id="10afb-104">Von [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="10afb-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="10afb-105">`dotnet-grpc` ist ein globales .net Core-Tool zum Verwalten von protobuf-verweisen innerhalb eines .net-GrpC-Projekts.</span><span class="sxs-lookup"><span data-stu-id="10afb-105">`dotnet-grpc` is a .NET Core Global Tool for managing Protobuf references within a .NET gRPC project.</span></span> <span data-ttu-id="10afb-106">Das Tool kann zum Hinzufügen, aktualisieren, entfernen und Auflisten von protobuf-verweisen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="10afb-106">The tool can be used to add, refresh, remove, and list Protobuf references.</span></span>

## <a name="installation"></a><span data-ttu-id="10afb-107">Installation</span><span class="sxs-lookup"><span data-stu-id="10afb-107">Installation</span></span>

<span data-ttu-id="10afb-108">Führen Sie den folgenden Befehl aus, um das [.net Core-Tool](/dotnet/core/tools/global-tools)"`dotnet-grpc`" zu installieren:</span><span class="sxs-lookup"><span data-stu-id="10afb-108">To install the `dotnet-grpc` [.NET Core Global Tool](/dotnet/core/tools/global-tools), run the following command:</span></span>

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a><span data-ttu-id="10afb-109">Hinzufügen von Verweisen</span><span class="sxs-lookup"><span data-stu-id="10afb-109">Add references</span></span>

<span data-ttu-id="10afb-110">`dotnet-grpc` kann verwendet werden, um protobuf-Verweise als `<Protobuf />`-Elemente der *csproj* -Datei hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="10afb-110">`dotnet-grpc` can be used to add Protobuf references as `<Protobuf />` items to the *.csproj* file:</span></span>

```xml
<Protobuf Include="..\Proto\count.proto" GrpcServices="Server" Link="Protos\count.proto" />
```

<span data-ttu-id="10afb-111">Die protobuf-Verweise werden verwendet, um C# die Client-und/oder Server Objekte zu generieren.</span><span class="sxs-lookup"><span data-stu-id="10afb-111">The Protobuf references are used to generate the C# client and/or server assets.</span></span> <span data-ttu-id="10afb-112">Das `dotnet-grpc`tool kann folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="10afb-112">The `dotnet-grpc`tool can:</span></span>

* <span data-ttu-id="10afb-113">Erstellen Sie einen protobuf-Verweis aus lokalen Dateien auf dem Datenträger.</span><span class="sxs-lookup"><span data-stu-id="10afb-113">Create a Protobuf reference from local files on disk.</span></span>
* <span data-ttu-id="10afb-114">Erstellen Sie einen protobuf-Verweis aus einer Remote Datei, die durch eine URL angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="10afb-114">Create a Protobuf reference from a remote file specified by a URL.</span></span>
* <span data-ttu-id="10afb-115">Stellen Sie sicher, dass dem Projekt die richtigen GrpC-Paketabhängigkeiten hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="10afb-115">Ensure the correct gRPC package dependencies are added to the project.</span></span>

<span data-ttu-id="10afb-116">Beispielsweise wird das Paket "`Grpc.AspNetCore`" einer Web-App hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="10afb-116">For example, the `Grpc.AspNetCore` package is added to a web app.</span></span> <span data-ttu-id="10afb-117">`Grpc.AspNetCore` enthält Unterstützung für GrpC-Server und Client Bibliotheken und-Tools.</span><span class="sxs-lookup"><span data-stu-id="10afb-117">`Grpc.AspNetCore` contains gRPC server and client libraries and tooling support.</span></span> <span data-ttu-id="10afb-118">Alternativ dazu werden die Pakete "`Grpc.Net.Client`", "`Grpc.Tools`" und "`Google.Protobuf`", die nur die Unterstützung von GrpC-Client Bibliotheken und-Tools enthalten, einer Konsolen-app hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="10afb-118">Alternatively, the `Grpc.Net.Client`, `Grpc.Tools` and `Google.Protobuf` packages, which contain only the gRPC client libraries and tooling support, are added to a Console app.</span></span>

### <a name="add-file"></a><span data-ttu-id="10afb-119">Datei hinzufügen</span><span class="sxs-lookup"><span data-stu-id="10afb-119">Add file</span></span>

<span data-ttu-id="10afb-120">Der Befehl "`add-file`" wird verwendet, um lokale Dateien auf dem Datenträger als protobuf-Verweise hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="10afb-120">The `add-file` command is used to add local files on disk as Protobuf references.</span></span> <span data-ttu-id="10afb-121">Die bereitgestellten Dateipfade:</span><span class="sxs-lookup"><span data-stu-id="10afb-121">The file paths provided:</span></span>

* <span data-ttu-id="10afb-122">Kann relativ zum aktuellen Verzeichnis oder zu absoluten Pfaden sein.</span><span class="sxs-lookup"><span data-stu-id="10afb-122">Can be relative to the current directory or absolute paths.</span></span>
* <span data-ttu-id="10afb-123">Kann Platzhalter für die Musterbasierte Datei [Globalisierung](https://wikipedia.org/wiki/Glob_(programming))enthalten.</span><span class="sxs-lookup"><span data-stu-id="10afb-123">May contain wild cards for pattern-based file [globbing](https://wikipedia.org/wiki/Glob_(programming)).</span></span>

<span data-ttu-id="10afb-124">Wenn Dateien außerhalb des Projektverzeichnisses liegen, wird ein `Link`-Element hinzugefügt, um die Datei unter dem Ordner `Protos` in Visual Studio anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="10afb-124">If any files are outside the project directory, a `Link` element is added to display the file under the folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="10afb-125">Verwendung</span><span class="sxs-lookup"><span data-stu-id="10afb-125">Usage</span></span>

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a><span data-ttu-id="10afb-126">Argumente</span><span class="sxs-lookup"><span data-stu-id="10afb-126">Arguments</span></span>

| <span data-ttu-id="10afb-127">Argument</span><span class="sxs-lookup"><span data-stu-id="10afb-127">Argument</span></span> | <span data-ttu-id="10afb-128">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="10afb-128">Description</span></span> |
|-|-|
| <span data-ttu-id="10afb-129">files</span><span class="sxs-lookup"><span data-stu-id="10afb-129">files</span></span> | <span data-ttu-id="10afb-130">Die protobuf-Datei Verweise.</span><span class="sxs-lookup"><span data-stu-id="10afb-130">The protobuf file references.</span></span> <span data-ttu-id="10afb-131">Hierbei kann es sich um einen Pfad zu globmuster für lokale protobuf-Dateien handeln.</span><span class="sxs-lookup"><span data-stu-id="10afb-131">These can be a path to glob for local protobuf files.</span></span> |

#### <a name="options"></a><span data-ttu-id="10afb-132">Optionen</span><span class="sxs-lookup"><span data-stu-id="10afb-132">Options</span></span>

| <span data-ttu-id="10afb-133">Kurze Option</span><span class="sxs-lookup"><span data-stu-id="10afb-133">Short option</span></span> | <span data-ttu-id="10afb-134">Lange Option</span><span class="sxs-lookup"><span data-stu-id="10afb-134">Long option</span></span> | <span data-ttu-id="10afb-135">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="10afb-135">Description</span></span> |
|-|-|-|
| <span data-ttu-id="10afb-136">-p</span><span class="sxs-lookup"><span data-stu-id="10afb-136">-p</span></span> | <span data-ttu-id="10afb-137">--Projekt</span><span class="sxs-lookup"><span data-stu-id="10afb-137">--project</span></span> | <span data-ttu-id="10afb-138">Der Pfad zu der Projektdatei, die verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="10afb-138">The path to the project file to operate on.</span></span> <span data-ttu-id="10afb-139">Wenn keine Datei angegeben ist, durchsucht der Befehl das aktuelle Verzeichnis nach einem.</span><span class="sxs-lookup"><span data-stu-id="10afb-139">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="10afb-140">-s</span><span class="sxs-lookup"><span data-stu-id="10afb-140">-s</span></span> | <span data-ttu-id="10afb-141">--Dienste</span><span class="sxs-lookup"><span data-stu-id="10afb-141">--services</span></span> | <span data-ttu-id="10afb-142">Der Typ der GrpC-Dienste, die generiert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="10afb-142">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="10afb-143">Wenn `Default` angegeben wird, wird `Both` für Webprojekte verwendet, und `Client` wird für nicht-Webprojekte verwendet.</span><span class="sxs-lookup"><span data-stu-id="10afb-143">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="10afb-144">Akzeptierte Werte sind `Both`, `Client`, `Default`, `None`, `Server`.</span><span class="sxs-lookup"><span data-stu-id="10afb-144">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="10afb-145">-i</span><span class="sxs-lookup"><span data-stu-id="10afb-145">-i</span></span> | <span data-ttu-id="10afb-146">--Weitere Import-dirs</span><span class="sxs-lookup"><span data-stu-id="10afb-146">--additional-import-dirs</span></span> | <span data-ttu-id="10afb-147">Zusätzliche Verzeichnisse, die beim Auflösen von Importen für die protobuf-Dateien verwendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="10afb-147">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="10afb-148">Dies ist eine durch Semikolons getrennte Liste von Pfaden.</span><span class="sxs-lookup"><span data-stu-id="10afb-148">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="10afb-149">--Zugriff</span><span class="sxs-lookup"><span data-stu-id="10afb-149">--access</span></span> | <span data-ttu-id="10afb-150">Der Zugriffsmodifizierer für C# die generierten Klassen.</span><span class="sxs-lookup"><span data-stu-id="10afb-150">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="10afb-151">Der Standardwert ist `Public`sein.</span><span class="sxs-lookup"><span data-stu-id="10afb-151">The default value is `Public`.</span></span> <span data-ttu-id="10afb-152">Akzeptierte Werte sind `Internal` und `Public`.</span><span class="sxs-lookup"><span data-stu-id="10afb-152">Accepted values are `Internal` and `Public`.</span></span>

### <a name="add-url"></a><span data-ttu-id="10afb-153">URL hinzufügen</span><span class="sxs-lookup"><span data-stu-id="10afb-153">Add URL</span></span>

<span data-ttu-id="10afb-154">Der Befehl "`add-url`" wird verwendet, um eine durch eine Quell-URL als protobuf-Referenz angegebene Remote Datei hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="10afb-154">The `add-url` command is used to add a remote file specified by an source URL as Protobuf reference.</span></span> <span data-ttu-id="10afb-155">Ein Dateipfad muss angegeben werden, um anzugeben, wo die Remote Datei heruntergeladen werden soll.</span><span class="sxs-lookup"><span data-stu-id="10afb-155">A file path must be provided to specify where to download the remote file.</span></span> <span data-ttu-id="10afb-156">Der Dateipfad kann relativ zum aktuellen Verzeichnis oder als absoluter Pfad sein.</span><span class="sxs-lookup"><span data-stu-id="10afb-156">The file path can be relative to the current directory or an absolute path.</span></span> <span data-ttu-id="10afb-157">Wenn sich der Dateipfad außerhalb des Projektverzeichnisses befindet, wird ein `Link`-Element hinzugefügt, um die Datei unter dem virtuellen Ordner `Protos` in Visual Studio anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="10afb-157">If the file path is outside the project directory, a `Link` element is added to display the file under the virtual folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="10afb-158">Verwendung</span><span class="sxs-lookup"><span data-stu-id="10afb-158">Usage</span></span>

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a><span data-ttu-id="10afb-159">Argumente</span><span class="sxs-lookup"><span data-stu-id="10afb-159">Arguments</span></span>

| <span data-ttu-id="10afb-160">Argument</span><span class="sxs-lookup"><span data-stu-id="10afb-160">Argument</span></span> | <span data-ttu-id="10afb-161">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="10afb-161">Description</span></span> |
|-|-|
| <span data-ttu-id="10afb-162">url</span><span class="sxs-lookup"><span data-stu-id="10afb-162">url</span></span> | <span data-ttu-id="10afb-163">Die URL zu einer Remote-protobuf-Datei.</span><span class="sxs-lookup"><span data-stu-id="10afb-163">The URL to a remote protobuf file.</span></span> |

#### <a name="options"></a><span data-ttu-id="10afb-164">Optionen</span><span class="sxs-lookup"><span data-stu-id="10afb-164">Options</span></span>

| <span data-ttu-id="10afb-165">Kurze Option</span><span class="sxs-lookup"><span data-stu-id="10afb-165">Short option</span></span> | <span data-ttu-id="10afb-166">Lange Option</span><span class="sxs-lookup"><span data-stu-id="10afb-166">Long option</span></span> | <span data-ttu-id="10afb-167">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="10afb-167">Description</span></span> |
|-|-|-|
| <span data-ttu-id="10afb-168">-o</span><span class="sxs-lookup"><span data-stu-id="10afb-168">-o</span></span> | <span data-ttu-id="10afb-169">--Ausgabe</span><span class="sxs-lookup"><span data-stu-id="10afb-169">--output</span></span> | <span data-ttu-id="10afb-170">Gibt den Download Pfad für die Remote-protobuf-Datei an.</span><span class="sxs-lookup"><span data-stu-id="10afb-170">Specifies the download path for the remote protobuf file.</span></span> <span data-ttu-id="10afb-171">Diese Option muss angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="10afb-171">This is a required option.</span></span>
| <span data-ttu-id="10afb-172">-p</span><span class="sxs-lookup"><span data-stu-id="10afb-172">-p</span></span> | <span data-ttu-id="10afb-173">--Projekt</span><span class="sxs-lookup"><span data-stu-id="10afb-173">--project</span></span> | <span data-ttu-id="10afb-174">Der Pfad zu der Projektdatei, die verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="10afb-174">The path to the project file to operate on.</span></span> <span data-ttu-id="10afb-175">Wenn keine Datei angegeben ist, durchsucht der Befehl das aktuelle Verzeichnis nach einem.</span><span class="sxs-lookup"><span data-stu-id="10afb-175">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="10afb-176">-s</span><span class="sxs-lookup"><span data-stu-id="10afb-176">-s</span></span> | <span data-ttu-id="10afb-177">--Dienste</span><span class="sxs-lookup"><span data-stu-id="10afb-177">--services</span></span> | <span data-ttu-id="10afb-178">Der Typ der GrpC-Dienste, die generiert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="10afb-178">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="10afb-179">Wenn `Default` angegeben wird, wird `Both` für Webprojekte verwendet, und `Client` wird für nicht-Webprojekte verwendet.</span><span class="sxs-lookup"><span data-stu-id="10afb-179">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="10afb-180">Akzeptierte Werte sind `Both`, `Client`, `Default`, `None`, `Server`.</span><span class="sxs-lookup"><span data-stu-id="10afb-180">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="10afb-181">-i</span><span class="sxs-lookup"><span data-stu-id="10afb-181">-i</span></span> | <span data-ttu-id="10afb-182">--Weitere Import-dirs</span><span class="sxs-lookup"><span data-stu-id="10afb-182">--additional-import-dirs</span></span> | <span data-ttu-id="10afb-183">Zusätzliche Verzeichnisse, die beim Auflösen von Importen für die protobuf-Dateien verwendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="10afb-183">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="10afb-184">Dies ist eine durch Semikolons getrennte Liste von Pfaden.</span><span class="sxs-lookup"><span data-stu-id="10afb-184">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="10afb-185">--Zugriff</span><span class="sxs-lookup"><span data-stu-id="10afb-185">--access</span></span> | <span data-ttu-id="10afb-186">Der Zugriffsmodifizierer für C# die generierten Klassen.</span><span class="sxs-lookup"><span data-stu-id="10afb-186">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="10afb-187">Der Standardwert ist `Public`sein.</span><span class="sxs-lookup"><span data-stu-id="10afb-187">Default value is `Public`.</span></span> <span data-ttu-id="10afb-188">Akzeptierte Werte sind `Internal` und `Public`.</span><span class="sxs-lookup"><span data-stu-id="10afb-188">Accepted values are `Internal` and `Public`.</span></span>

## <a name="remove"></a><span data-ttu-id="10afb-189">Remove</span><span class="sxs-lookup"><span data-stu-id="10afb-189">Remove</span></span>

<span data-ttu-id="10afb-190">Der Befehl "`remove`" wird verwendet, um protobuf-Verweise aus der *csproj* -Datei zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="10afb-190">The `remove` command is used to remove Protobuf references from the *.csproj* file.</span></span> <span data-ttu-id="10afb-191">Der Befehl akzeptiert Pfad Argumente und Quell-URLs als Argumente.</span><span class="sxs-lookup"><span data-stu-id="10afb-191">The command accepts path arguments and source URLs as arguments.</span></span> <span data-ttu-id="10afb-192">Das Tool:</span><span class="sxs-lookup"><span data-stu-id="10afb-192">The tool:</span></span>

* <span data-ttu-id="10afb-193">Entfernt nur den protobuf-Verweis.</span><span class="sxs-lookup"><span data-stu-id="10afb-193">Only removes the Protobuf reference.</span></span>
* <span data-ttu-id="10afb-194">Die *. proto* -Datei wird nicht gelöscht, auch wenn Sie ursprünglich von einer Remote-URL heruntergeladen wurde.</span><span class="sxs-lookup"><span data-stu-id="10afb-194">Does not delete the *.proto* file, even if it was originally downloaded from a remote URL.</span></span>

### <a name="usage"></a><span data-ttu-id="10afb-195">Verwendung</span><span class="sxs-lookup"><span data-stu-id="10afb-195">Usage</span></span>

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a><span data-ttu-id="10afb-196">Argumente</span><span class="sxs-lookup"><span data-stu-id="10afb-196">Arguments</span></span>

| <span data-ttu-id="10afb-197">Argument</span><span class="sxs-lookup"><span data-stu-id="10afb-197">Argument</span></span> | <span data-ttu-id="10afb-198">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="10afb-198">Description</span></span> |
|-|-|
| <span data-ttu-id="10afb-199">references</span><span class="sxs-lookup"><span data-stu-id="10afb-199">references</span></span> | <span data-ttu-id="10afb-200">Die URLs oder Dateipfade der zu entfernenden protobuf-Verweise.</span><span class="sxs-lookup"><span data-stu-id="10afb-200">The URLs or file paths of the protobuf references to remove.</span></span> |

### <a name="options"></a><span data-ttu-id="10afb-201">Optionen</span><span class="sxs-lookup"><span data-stu-id="10afb-201">Options</span></span>

| <span data-ttu-id="10afb-202">Kurze Option</span><span class="sxs-lookup"><span data-stu-id="10afb-202">Short option</span></span> | <span data-ttu-id="10afb-203">Lange Option</span><span class="sxs-lookup"><span data-stu-id="10afb-203">Long option</span></span> | <span data-ttu-id="10afb-204">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="10afb-204">Description</span></span> |
|-|-|-|
| <span data-ttu-id="10afb-205">-p</span><span class="sxs-lookup"><span data-stu-id="10afb-205">-p</span></span> | <span data-ttu-id="10afb-206">--Projekt</span><span class="sxs-lookup"><span data-stu-id="10afb-206">--project</span></span> | <span data-ttu-id="10afb-207">Der Pfad zu der Projektdatei, die verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="10afb-207">The path to the project file to operate on.</span></span> <span data-ttu-id="10afb-208">Wenn keine Datei angegeben ist, durchsucht der Befehl das aktuelle Verzeichnis nach einem.</span><span class="sxs-lookup"><span data-stu-id="10afb-208">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="refresh"></a><span data-ttu-id="10afb-209">Aktualisieren</span><span class="sxs-lookup"><span data-stu-id="10afb-209">Refresh</span></span>

<span data-ttu-id="10afb-210">Der Befehl "`refresh`" wird zum Aktualisieren eines Remote Verweises mit dem neuesten Inhalt aus der Quell-URL verwendet.</span><span class="sxs-lookup"><span data-stu-id="10afb-210">The `refresh` command is used to update a remote reference with the latest content from the source URL.</span></span> <span data-ttu-id="10afb-211">Sowohl der Download Dateipfad als auch die Quell-URL können zum Angeben des zu aktualisierenden Verweises verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="10afb-211">Both the download file path and the source URL can be used to specify the reference to be updated.</span></span> <span data-ttu-id="10afb-212">Hinweis:</span><span class="sxs-lookup"><span data-stu-id="10afb-212">Note:</span></span>

* <span data-ttu-id="10afb-213">Die Hashwerte für den Dateiinhalt werden verglichen, um zu bestimmen, ob die lokale Datei aktualisiert werden soll.</span><span class="sxs-lookup"><span data-stu-id="10afb-213">The hashes of the file contents are compared to determine whether the local file should be updated.</span></span>
* <span data-ttu-id="10afb-214">Es werden keine Zeitstempel Informationen verglichen.</span><span class="sxs-lookup"><span data-stu-id="10afb-214">No timestamp information is compared.</span></span>

<span data-ttu-id="10afb-215">Wenn ein Update erforderlich ist, ersetzt das Tool immer die lokale Datei durch die Remote Datei.</span><span class="sxs-lookup"><span data-stu-id="10afb-215">The tool always replaces the local file with the remote file if an update is needed.</span></span>

### <a name="usage"></a><span data-ttu-id="10afb-216">Verwendung</span><span class="sxs-lookup"><span data-stu-id="10afb-216">Usage</span></span>

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a><span data-ttu-id="10afb-217">Argumente</span><span class="sxs-lookup"><span data-stu-id="10afb-217">Arguments</span></span>

| <span data-ttu-id="10afb-218">Argument</span><span class="sxs-lookup"><span data-stu-id="10afb-218">Argument</span></span> | <span data-ttu-id="10afb-219">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="10afb-219">Description</span></span> |
|-|-|
| <span data-ttu-id="10afb-220">references</span><span class="sxs-lookup"><span data-stu-id="10afb-220">references</span></span> | <span data-ttu-id="10afb-221">Die URLs oder Dateipfade zu Remote-protobuf-verweisen, die aktualisiert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="10afb-221">The URLs or file paths to remote protobuf references that should be updated.</span></span> <span data-ttu-id="10afb-222">Lassen Sie dieses Argument leer, um alle Remote Verweise zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="10afb-222">Leave this argument empty to refresh all remote references.</span></span> |

### <a name="options"></a><span data-ttu-id="10afb-223">Optionen</span><span class="sxs-lookup"><span data-stu-id="10afb-223">Options</span></span>

| <span data-ttu-id="10afb-224">Kurze Option</span><span class="sxs-lookup"><span data-stu-id="10afb-224">Short option</span></span> | <span data-ttu-id="10afb-225">Lange Option</span><span class="sxs-lookup"><span data-stu-id="10afb-225">Long option</span></span> | <span data-ttu-id="10afb-226">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="10afb-226">Description</span></span> |
|-|-|-|
| <span data-ttu-id="10afb-227">-p</span><span class="sxs-lookup"><span data-stu-id="10afb-227">-p</span></span> | <span data-ttu-id="10afb-228">--Projekt</span><span class="sxs-lookup"><span data-stu-id="10afb-228">--project</span></span> | <span data-ttu-id="10afb-229">Der Pfad zu der Projektdatei, die verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="10afb-229">The path to the project file to operate on.</span></span> <span data-ttu-id="10afb-230">Wenn keine Datei angegeben ist, durchsucht der Befehl das aktuelle Verzeichnis nach einem.</span><span class="sxs-lookup"><span data-stu-id="10afb-230">If a file is not specified, the command searches the current directory for one.</span></span>
| | <span data-ttu-id="10afb-231">--Dry-Run</span><span class="sxs-lookup"><span data-stu-id="10afb-231">--dry-run</span></span> | <span data-ttu-id="10afb-232">Gibt eine Liste der Dateien aus, die aktualisiert werden, ohne dass neue Inhalte heruntergeladen werden.</span><span class="sxs-lookup"><span data-stu-id="10afb-232">Outputs a list of files that would be updated without downloading any new content.</span></span>

## <a name="list"></a><span data-ttu-id="10afb-233">Liste</span><span class="sxs-lookup"><span data-stu-id="10afb-233">List</span></span>

<span data-ttu-id="10afb-234">Der Befehl "`list`" wird verwendet, um alle protobuf-Verweise in der Projektdatei anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="10afb-234">The `list` command is used to display all the Protobuf references in the project file.</span></span> <span data-ttu-id="10afb-235">Wenn alle Werte einer Spalte Standardwerte sind, darf die Spalte weggelassen werden.</span><span class="sxs-lookup"><span data-stu-id="10afb-235">If all values of a column are default values, the column may be omitted.</span></span>

### <a name="usage"></a><span data-ttu-id="10afb-236">Verwendung</span><span class="sxs-lookup"><span data-stu-id="10afb-236">Usage</span></span>

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a><span data-ttu-id="10afb-237">Optionen</span><span class="sxs-lookup"><span data-stu-id="10afb-237">Options</span></span>

| <span data-ttu-id="10afb-238">Kurze Option</span><span class="sxs-lookup"><span data-stu-id="10afb-238">Short option</span></span> | <span data-ttu-id="10afb-239">Lange Option</span><span class="sxs-lookup"><span data-stu-id="10afb-239">Long option</span></span> | <span data-ttu-id="10afb-240">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="10afb-240">Description</span></span> |
|-|-|-|
| <span data-ttu-id="10afb-241">-p</span><span class="sxs-lookup"><span data-stu-id="10afb-241">-p</span></span> | <span data-ttu-id="10afb-242">--Projekt</span><span class="sxs-lookup"><span data-stu-id="10afb-242">--project</span></span> | <span data-ttu-id="10afb-243">Der Pfad zu der Projektdatei, die verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="10afb-243">The path to the project file to operate on.</span></span> <span data-ttu-id="10afb-244">Wenn keine Datei angegeben ist, durchsucht der Befehl das aktuelle Verzeichnis nach einem.</span><span class="sxs-lookup"><span data-stu-id="10afb-244">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="10afb-245">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="10afb-245">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
