---
title: gRPC-Dienste mit C#
author: juntaoluo
description: Hier erlernen Sie die grundlegenden Konzepte beim Schreiben von gRPC-Diensten mit C#.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 07/03/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/basics
ms.openlocfilehash: a55ed90e7c854d1475b1f5d95347505fad0813ab
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774755"
---
# <a name="grpc-services-with-c"></a><span data-ttu-id="400dc-103">gRPC-Dienste mit C\#</span><span class="sxs-lookup"><span data-stu-id="400dc-103">gRPC services with C\#</span></span>

<span data-ttu-id="400dc-104">In diesem Artikel werden die Konzepte erläutert, die zum Schreiben von [gRPC](https://grpc.io/docs/guides/)-Apps in C# erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="400dc-104">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="400dc-105">Die hier behandelten Themen gelten für gRPC-Apps, die auf [C-Core](https://grpc.io/blog/grpc-stacks) und auf ASP.NET Core basieren.</span><span class="sxs-lookup"><span data-stu-id="400dc-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

## <a name="proto-file"></a><span data-ttu-id="400dc-106">PROTO-Datei</span><span class="sxs-lookup"><span data-stu-id="400dc-106">proto file</span></span>

<span data-ttu-id="400dc-107">gRPC verwendet einen Vertrag zuerst-Ansatz für die API-Entwicklung.</span><span class="sxs-lookup"><span data-stu-id="400dc-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="400dc-108">Protokollpuffer (protobuf) werden standardmäßig als IDL (Interface Definition Language) verwendet.</span><span class="sxs-lookup"><span data-stu-id="400dc-108">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="400dc-109">Die *\*.proto*-Datei enthält Folgendes:</span><span class="sxs-lookup"><span data-stu-id="400dc-109">The *\*.proto* file contains:</span></span>

* <span data-ttu-id="400dc-110">Die Definition des gRPC-Diensts</span><span class="sxs-lookup"><span data-stu-id="400dc-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="400dc-111">Die zwischen Clients und Servern übermittelten Nachrichten</span><span class="sxs-lookup"><span data-stu-id="400dc-111">The messages sent between clients and servers.</span></span>

<span data-ttu-id="400dc-112">Weitere Informationen zur Syntax der protobuf-Dateien finden Sie in der [offiziellen Dokumentation zu Protokollpuffern](https://developers.google.com/protocol-buffers/docs/proto3).</span><span class="sxs-lookup"><span data-stu-id="400dc-112">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="400dc-113">Sehen Sie sich beispielsweise die Datei *greet.proto* an, die in den [ersten Schritten mit dem gRPC-Dienst](xref:tutorials/grpc/grpc-start) verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="400dc-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="400dc-114">Die Datei definiert einen `Greeter`-Dienst.</span><span class="sxs-lookup"><span data-stu-id="400dc-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="400dc-115">Der `Greeter`-Dienst definiert einen `SayHello`-Aufruf.</span><span class="sxs-lookup"><span data-stu-id="400dc-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="400dc-116">`SayHello` sendet eine `HelloRequest`-Nachricht und empfängt eine `HelloReply`-Nachricht:</span><span class="sxs-lookup"><span data-stu-id="400dc-116">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message:</span></span>

[!code-protobuf[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Protos/greet.proto)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="400dc-117">Hinzufügen einer PROTO-Datei zu einer C\#-App</span><span class="sxs-lookup"><span data-stu-id="400dc-117">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="400dc-118">Die *\*.proto*-Datei wird in ein Projekt eingefügt, indem sie zur Elementgruppe `<Protobuf>` hinzugefügt wird:</span><span class="sxs-lookup"><span data-stu-id="400dc-118">The *\*.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="400dc-119">C#-Toolunterstützung für PROTO-Dateien</span><span class="sxs-lookup"><span data-stu-id="400dc-119">C# Tooling support for .proto files</span></span>

<span data-ttu-id="400dc-120">Das Toolpaket [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) ist zum Generieren der C#-Ressourcen aus *\*.proto*-Dateien erforderlich.</span><span class="sxs-lookup"><span data-stu-id="400dc-120">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *\*.proto* files.</span></span> <span data-ttu-id="400dc-121">Die generierten Ressourcen (Dateien):</span><span class="sxs-lookup"><span data-stu-id="400dc-121">The generated assets (files):</span></span>

* <span data-ttu-id="400dc-122">Werden jedes Mal nach Bedarf generiert, wenn der Buildvorgang für das Projekt ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="400dc-122">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="400dc-123">Werden nicht zum Projekt hinzugefügt oder in der Quellcodeverwaltung eingecheckt.</span><span class="sxs-lookup"><span data-stu-id="400dc-123">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="400dc-124">Sind Buildartefakte, die im *obj*-Verzeichnis enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="400dc-124">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="400dc-125">Dieses Paket ist für die Server- und Clientprojekte erforderlich.</span><span class="sxs-lookup"><span data-stu-id="400dc-125">This package is required by both the server and client projects.</span></span> <span data-ttu-id="400dc-126">Das Metapaket `Grpc.AspNetCore` enthält einen Verweis auf `Grpc.Tools`.</span><span class="sxs-lookup"><span data-stu-id="400dc-126">The `Grpc.AspNetCore` metapackage includes a reference to `Grpc.Tools`.</span></span> <span data-ttu-id="400dc-127">Serverprojekte können `Grpc.AspNetCore` mithilfe des Paket-Managers in Visual Studio oder durch Hinzufügen von `<PackageReference>` zur Projektdatei hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="400dc-127">Server projects can add `Grpc.AspNetCore` using the Package Manager in Visual Studio or by adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=12)]

<span data-ttu-id="400dc-128">Clientprojekte sollten direkt auf `Grpc.Tools` sowie auf die anderen zum Verwenden des gRPC-Clients erforderlichen Pakete verweisen.</span><span class="sxs-lookup"><span data-stu-id="400dc-128">Client projects should directly reference `Grpc.Tools` alongside the other packages required to use the gRPC client.</span></span> <span data-ttu-id="400dc-129">Das Toolpaket ist nicht zur Laufzeit erforderlich, darum ist die Abhängigkeit mit `PrivateAssets="All"` markiert:</span><span class="sxs-lookup"><span data-stu-id="400dc-129">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/GrpcGreeterClient.csproj?highlight=3&range=9-11)]

## <a name="generated-c-assets"></a><span data-ttu-id="400dc-130">Generierte C#-Ressourcen</span><span class="sxs-lookup"><span data-stu-id="400dc-130">Generated C# assets</span></span>

<span data-ttu-id="400dc-131">Das Toolpaket generiert die C#-Typen, die die Nachrichten darstellen, die in den enthaltenen *\*.proto*-Dateien definiert werden.</span><span class="sxs-lookup"><span data-stu-id="400dc-131">The tooling package generates the C# types representing the messages defined in the included *\*.proto* files.</span></span>

<span data-ttu-id="400dc-132">Für serverseitige Ressourcen wird ein abstrakter Dienstbasistyp generiert.</span><span class="sxs-lookup"><span data-stu-id="400dc-132">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="400dc-133">Der Basistyp enthält die Definitionen für alle gRPC-Aufrufe, die in der *PROTO*-Datei enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="400dc-133">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="400dc-134">Erstellen Sie eine konkrete Dienstimplementierung, die von diesem Basistyp abgeleitet wird und die Logik für die gRPC-Aufrufe implementiert.</span><span class="sxs-lookup"><span data-stu-id="400dc-134">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="400dc-135">Für das zuvor beschriebene Beispiel `greet.proto` wird ein abstrakter `GreeterBase`-Typ generiert, der eine virtuelle `SayHello`-Methode enthält.</span><span class="sxs-lookup"><span data-stu-id="400dc-135">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="400dc-136">Eine konkrete `GreeterService`-Implementierung überschreibt die Methode und implementiert die Logik, die den gRPC-Aufruf verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="400dc-136">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="400dc-137">Für clientseitige Ressourcen wird ein konkreter Clienttyp generiert.</span><span class="sxs-lookup"><span data-stu-id="400dc-137">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="400dc-138">Die gRPC-Aufrufe in der *PROTO*-Datei werden in Methoden für den konkreten Typ übersetzt, die aufgerufen werden können.</span><span class="sxs-lookup"><span data-stu-id="400dc-138">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="400dc-139">Für das zuvor beschriebene Beispiel `greet.proto` wird ein konkreter `GreeterClient`-Typ generiert.</span><span class="sxs-lookup"><span data-stu-id="400dc-139">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="400dc-140">Rufen Sie `GreeterClient.SayHelloAsync` auf, um einen gRPC-Aufruf an den Server zu initiieren.</span><span class="sxs-lookup"><span data-stu-id="400dc-140">Call `GreeterClient.SayHelloAsync` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet)]

<span data-ttu-id="400dc-141">Server- und Clientressourcen werden standardmäßig für alle *\*.proto*-Dateien generiert, die in der Elementgruppe `<Protobuf>` enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="400dc-141">By default, server and client assets are generated for each *\*.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="400dc-142">Das `GrpcServices`-Attribut wird auf `Server` festgelegt, um sicherzustellen, dass nur die Serverressourcen in einem Serverprojekt generiert werden.</span><span class="sxs-lookup"><span data-stu-id="400dc-142">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-9)]

<span data-ttu-id="400dc-143">Entsprechend wird das Attribut bei Clientprojekten auf `Client` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="400dc-143">Similarly, the attribute is set to `Client` in client projects.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a><span data-ttu-id="400dc-144">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="400dc-144">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
