---
title: gRPC-Dienste mit C#
author: juntaoluo
description: Erlernen Sie die grundlegenden Konzepte beim Schreiben von GrpC-Diensten mit C#.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
uid: grpc/basics
ms.openlocfilehash: e17a4561f2d4f8ceccb293a8a8c237de58e4ee3c
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310422"
---
# <a name="grpc-services-with-c"></a><span data-ttu-id="f222d-103">GrpC-Dienste mit C\#</span><span class="sxs-lookup"><span data-stu-id="f222d-103">gRPC services with C\#</span></span>

<span data-ttu-id="f222d-104">Dieses Dokument beschreibt die Konzepte, die zum Schreiben von [GrpC](https://grpc.io/docs/guides/) -apps in C#erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="f222d-104">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="f222d-105">Die hier behandelten Themen gelten sowohl für [C-Core](https://grpc.io/blog/grpc-stacks)-basierte als auch für ASP.net Core-basierte GrpC-apps.</span><span class="sxs-lookup"><span data-stu-id="f222d-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

## <a name="proto-file"></a><span data-ttu-id="f222d-106">Proto-Datei</span><span class="sxs-lookup"><span data-stu-id="f222d-106">proto file</span></span>

<span data-ttu-id="f222d-107">GrpC verwendet einen Vertrag First-Ansatz für die API-Entwicklung.</span><span class="sxs-lookup"><span data-stu-id="f222d-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="f222d-108">Protokollpuffer (protobuf) werden standardmäßig als IDL (Interface Design Language) verwendet.</span><span class="sxs-lookup"><span data-stu-id="f222d-108">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="f222d-109">Die Datei ". proto" enthält Folgendes:  *\**</span><span class="sxs-lookup"><span data-stu-id="f222d-109">The *\*.proto* file contains:</span></span>

* <span data-ttu-id="f222d-110">Die Definition des GrpC-Dienstanbieter.</span><span class="sxs-lookup"><span data-stu-id="f222d-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="f222d-111">Die zwischen Clients und Servern gesendeten Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="f222d-111">The messages sent between clients and servers.</span></span>

<span data-ttu-id="f222d-112">Weitere Informationen zur Syntax von protobuf-Dateien finden Sie in der [offiziellen Dokumentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span><span class="sxs-lookup"><span data-stu-id="f222d-112">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="f222d-113">Sehen Sie sich beispielsweise die Datei " *Gruß. proto* " an, die in den ersten Schritten [mit dem GrpC-Dienst](xref:tutorials/grpc/grpc-start)</span><span class="sxs-lookup"><span data-stu-id="f222d-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="f222d-114">Definiert einen `Greeter` Dienst.</span><span class="sxs-lookup"><span data-stu-id="f222d-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="f222d-115">Der `Greeter` Dienst definiert einen `SayHello` -Befehl.</span><span class="sxs-lookup"><span data-stu-id="f222d-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="f222d-116">`SayHello`sendet eine `HelloRequest` Nachricht und empfängt eine `HelloReply` Meldung:</span><span class="sxs-lookup"><span data-stu-id="f222d-116">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message:</span></span>

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="f222d-117">Hinzufügen einer. proto-Datei zu einer C\#-App</span><span class="sxs-lookup"><span data-stu-id="f222d-117">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="f222d-118">`<Protobuf>` Die  *\*Datei ". proto* " ist in einem Projekt enthalten, indem Sie der Element Gruppe hinzugefügt wird:</span><span class="sxs-lookup"><span data-stu-id="f222d-118">The *\*.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="f222d-119">C#Tool Unterstützung für. proto-Dateien</span><span class="sxs-lookup"><span data-stu-id="f222d-119">C# Tooling support for .proto files</span></span>

<span data-ttu-id="f222d-120">Das Tool Paket " [GrpC. Tools](https://www.nuget.org/packages/Grpc.Tools/) " ist erforderlich, C# um die Objekte aus  *\*". proto* "-Dateien zu generieren.</span><span class="sxs-lookup"><span data-stu-id="f222d-120">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *\*.proto* files.</span></span> <span data-ttu-id="f222d-121">Die generierten Assets (Dateien):</span><span class="sxs-lookup"><span data-stu-id="f222d-121">The generated assets (files):</span></span>

* <span data-ttu-id="f222d-122">Werden bei jeder Erstellung des Projekts Bedarfs abhängig generiert.</span><span class="sxs-lookup"><span data-stu-id="f222d-122">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="f222d-123">Werden dem Projekt nicht hinzugefügt oder in die Quell Code Verwaltung eingecheckt.</span><span class="sxs-lookup"><span data-stu-id="f222d-123">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="f222d-124">Ein buildartefakt, das im *obj* -Verzeichnis enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="f222d-124">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="f222d-125">Dieses Paket ist für die Server-und Client Projekte erforderlich.</span><span class="sxs-lookup"><span data-stu-id="f222d-125">This package is required by both the server and client projects.</span></span> <span data-ttu-id="f222d-126">Das `Grpc.AspNetCore` Metapaket enthält einen Verweis auf `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="f222d-126">The `Grpc.AspNetCore` metapackage includes a reference to `Grpc.Tools`.</span></span> <span data-ttu-id="f222d-127">Server Projekte können mithilfe `Grpc.AspNetCore` des Paket-Managers in Visual Studio oder durch Hinzufügen `<PackageReference>` einer zur Projektdatei hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="f222d-127">Server projects can add `Grpc.AspNetCore` using the Package Manager in Visual Studio or by adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

<span data-ttu-id="f222d-128">Client Projekte sollten direkt neben `Grpc.Tools` den anderen Paketen verweisen, die für die Verwendung des GrpC-Clients erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="f222d-128">Client projects should directly reference `Grpc.Tools` alongside the other packages required to use the gRPC client.</span></span> <span data-ttu-id="f222d-129">Das Toolpaket ist zur Laufzeit nicht erforderlich, sodass die Abhängigkeit mit `PrivateAssets="All"`markiert ist:</span><span class="sxs-lookup"><span data-stu-id="f222d-129">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a><span data-ttu-id="f222d-130">Generierte C# Assets</span><span class="sxs-lookup"><span data-stu-id="f222d-130">Generated C# assets</span></span>

<span data-ttu-id="f222d-131">Das Tool Paket generiert die C# Typen, die die in den enthaltenen  *\*. proto* -Dateien definierten Meldungen darstellen.</span><span class="sxs-lookup"><span data-stu-id="f222d-131">The tooling package generates the C# types representing the messages defined in the included *\*.proto* files.</span></span>

<span data-ttu-id="f222d-132">Bei serverseitigen Assets wird ein abstrakter Dienst Basistyp generiert.</span><span class="sxs-lookup"><span data-stu-id="f222d-132">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="f222d-133">Der Basistyp enthält die Definitionen aller in der *. proto* -Datei enthaltenen GrpC-Aufrufe.</span><span class="sxs-lookup"><span data-stu-id="f222d-133">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="f222d-134">Erstellen Sie eine konkrete Dienst Implementierung, die von diesem Basistyp abgeleitet ist und die Logik für die GrpC-Aufrufe implementiert.</span><span class="sxs-lookup"><span data-stu-id="f222d-134">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="f222d-135">Für das `greet.proto`, das zuvor beschriebene Beispiel, wird ein `GreeterBase` abstrakter Typ generiert, `SayHello` der eine virtuelle Methode enthält.</span><span class="sxs-lookup"><span data-stu-id="f222d-135">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="f222d-136">Eine konkrete Implementierung `GreeterService` überschreibt die-Methode und implementiert die Logik, die den GrpC-Befehl behandelt.</span><span class="sxs-lookup"><span data-stu-id="f222d-136">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="f222d-137">Bei Client seitigen Assets wird ein konkreter Clienttyp generiert.</span><span class="sxs-lookup"><span data-stu-id="f222d-137">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="f222d-138">Die GrpC-Aufrufe in der *. proto* -Datei werden in Methoden für den konkreten Typ übersetzt, der aufgerufen werden kann.</span><span class="sxs-lookup"><span data-stu-id="f222d-138">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="f222d-139">Für das `greet.proto`, das zuvor beschriebene Beispiel, wird ein `GreeterClient` konkreter Typ generiert.</span><span class="sxs-lookup"><span data-stu-id="f222d-139">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="f222d-140">Ruft `GreeterClient.SayHelloAsync` auf, um einen GrpC-Rückruf an den Server zu initiieren.</span><span class="sxs-lookup"><span data-stu-id="f222d-140">Call `GreeterClient.SayHelloAsync` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="f222d-141">Standardmäßig werden Server-und Client Objekte für jede `<Protobuf>`  *\** in der Element Gruppe enthaltene. proto-Datei generiert.</span><span class="sxs-lookup"><span data-stu-id="f222d-141">By default, server and client assets are generated for each *\*.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="f222d-142">Um sicherzustellen, dass nur die Server Objekte in einem Server Projekt generiert `GrpcServices` werden, wird das `Server`-Attribut auf festgelegt.</span><span class="sxs-lookup"><span data-stu-id="f222d-142">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="f222d-143">Entsprechend ist das-Attribut in Client `Client` Projekten auf festgelegt.</span><span class="sxs-lookup"><span data-stu-id="f222d-143">Similarly, the attribute is set to `Client` in client projects.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f222d-144">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f222d-144">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
