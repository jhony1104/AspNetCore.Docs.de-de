---
title: gRPC-Dienste mit C#
author: juntaoluo
description: Die grundlegenden Konzepte erfahren Sie, beim Schreiben von gRPC-Dienste mit C#.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/31/2019
uid: grpc/basics
ms.openlocfilehash: 7c5ecf21124414b21f5c36b76e90bde67ac1f958
ms.sourcegitcommit: 57a974556acd09363a58f38c26f74dc21e0d4339
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59672670"
---
# <a name="grpc-services-with-c"></a><span data-ttu-id="ed2ab-103">gRPC-Dienste mit C\#</span><span class="sxs-lookup"><span data-stu-id="ed2ab-103">gRPC services with C\#</span></span>

<span data-ttu-id="ed2ab-104">In diesem Dokument werden die Konzepte, die zum Schreiben benötigt [gRPC](https://grpc.io/docs/guides/) -apps in C#.</span><span class="sxs-lookup"><span data-stu-id="ed2ab-104">This document outlines the concepts needed to write [gRPC](https://grpc.io/docs/guides/) apps in C#.</span></span> <span data-ttu-id="ed2ab-105">Die hier behandelten Themen gelten für beide [C-Core](https://grpc.io/blog/grpc-stacks)-Basis und ASP.NET Core-basierte gRPC-apps.</span><span class="sxs-lookup"><span data-stu-id="ed2ab-105">The topics covered here apply to both [C-core](https://grpc.io/blog/grpc-stacks)-based and ASP.NET Core-based gRPC apps.</span></span>

## <a name="proto-file"></a><span data-ttu-id="ed2ab-106">Proto-Datei</span><span class="sxs-lookup"><span data-stu-id="ed2ab-106">proto file</span></span>

<span data-ttu-id="ed2ab-107">gRPC verwendet einen Vertrag zuerst-Ansatz für die API-Entwicklung.</span><span class="sxs-lookup"><span data-stu-id="ed2ab-107">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="ed2ab-108">Als Schnittstelle Design Language (IDL) werden standardmäßig Protokollpuffer (Protobuf) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="ed2ab-108">Protocol buffers (protobuf) are used as the Interface Design Language (IDL) by default.</span></span> <span data-ttu-id="ed2ab-109">Die *.proto* -Datei enthält:</span><span class="sxs-lookup"><span data-stu-id="ed2ab-109">The *.proto* file contains:</span></span>

* <span data-ttu-id="ed2ab-110">Die Definition des gRPC-Diensts.</span><span class="sxs-lookup"><span data-stu-id="ed2ab-110">The definition of the gRPC service.</span></span>
* <span data-ttu-id="ed2ab-111">Die Nachrichten zwischen Clients und Servern.</span><span class="sxs-lookup"><span data-stu-id="ed2ab-111">The messages sent between clients and servers.</span></span>

<span data-ttu-id="ed2ab-112">Weitere Informationen zur Syntax von Protobuf-Dateien finden Sie unter den [offizielle Dokumentation (Protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span><span class="sxs-lookup"><span data-stu-id="ed2ab-112">For more information on the syntax of protobuf files, see the [official documentation (protobuf)](https://developers.google.com/protocol-buffers/docs/proto3).</span></span>

<span data-ttu-id="ed2ab-113">Betrachten Sie beispielsweise die *greet.proto* -Basisdatei [erste Schritte mit gRPC Service](xref:tutorials/grpc/grpc-start):</span><span class="sxs-lookup"><span data-stu-id="ed2ab-113">For example, consider the *greet.proto* file used in [Get started with gRPC service](xref:tutorials/grpc/grpc-start):</span></span>

* <span data-ttu-id="ed2ab-114">Definiert eine `Greeter` Service.</span><span class="sxs-lookup"><span data-stu-id="ed2ab-114">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="ed2ab-115">Die `Greeter` Dienst definiert eine `SayHello` aufrufen.</span><span class="sxs-lookup"><span data-stu-id="ed2ab-115">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="ed2ab-116">`SayHello` sendet eine `HelloRequest` Nachricht und empfängt eine `HelloResponse` Nachricht:</span><span class="sxs-lookup"><span data-stu-id="ed2ab-116">`SayHello` sends a `HelloRequest` message and receives a `HelloResponse` message:</span></span>

[!code-proto[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/Protos/greet.proto)]

## <a name="add-a-proto-file-to-a-c-app"></a><span data-ttu-id="ed2ab-117">Fügen Sie eine .proto-Datei für eine C-\# app</span><span class="sxs-lookup"><span data-stu-id="ed2ab-117">Add a .proto file to a C\# app</span></span>

<span data-ttu-id="ed2ab-118">Die *.proto* Datei ist in einem Projekt enthalten, indem sie zum Hinzufügen der `<Protobuf>` Elementgruppe:</span><span class="sxs-lookup"><span data-stu-id="ed2ab-118">The *.proto* file is included in a project by adding it to the `<Protobuf>` item group:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-11)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="ed2ab-119">C#Toolunterstützung für .proto-Dateien</span><span class="sxs-lookup"><span data-stu-id="ed2ab-119">C# Tooling support for .proto files</span></span>

<span data-ttu-id="ed2ab-120">Das Paket mit Tools [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) ist erforderlich, um das Generieren der C# Objekte aus den *.proto* Dateien.</span><span class="sxs-lookup"><span data-stu-id="ed2ab-120">The tooling package [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) is required to generate the C# assets from *.proto* files.</span></span> <span data-ttu-id="ed2ab-121">Der generierten Assets (Dateien):</span><span class="sxs-lookup"><span data-stu-id="ed2ab-121">The generated assets (files):</span></span>

* <span data-ttu-id="ed2ab-122">Werden auf einen Bedarf jedes Mal generiert, die das Projekt erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="ed2ab-122">Are generated on an as-needed basis each time the project is built.</span></span>
* <span data-ttu-id="ed2ab-123">Nicht dem Projekt hinzugefügt oder in die quellcodeverwaltung eingecheckt.</span><span class="sxs-lookup"><span data-stu-id="ed2ab-123">Aren't added to the project or checked into source control.</span></span>
* <span data-ttu-id="ed2ab-124">Sind Sie ein buildartefakt, die innerhalb der *Obj* Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="ed2ab-124">Are a build artifact contained in the *obj* directory.</span></span>

<span data-ttu-id="ed2ab-125">Dieses Paket ist sowohl die Server- und Clientprojekte erforderlich.</span><span class="sxs-lookup"><span data-stu-id="ed2ab-125">This package is required by both the server and client projects.</span></span> <span data-ttu-id="ed2ab-126">`Grpc.Tools` kann hinzugefügt werden, indem Sie mithilfe des Paket-Managers in Visual Studio oder das Hinzufügen einer `<PackageReference>` zu der Projektdatei:</span><span class="sxs-lookup"><span data-stu-id="ed2ab-126">`Grpc.Tools` can be added by using the Package Manager in Visual Studio or adding a `<PackageReference>` to the project file:</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/GrpcGreeter.csproj?highlight=1&range=17)]

<span data-ttu-id="ed2ab-127">Das Tools-Paket ist nicht erforderlich, während der Laufzeit, damit die Abhängigkeit mit markiert ist `PrivateAssets="All"`.</span><span class="sxs-lookup"><span data-stu-id="ed2ab-127">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

## <a name="generated-c-assets"></a><span data-ttu-id="ed2ab-128">Generiert C# Assets</span><span class="sxs-lookup"><span data-stu-id="ed2ab-128">Generated C# assets</span></span>

<span data-ttu-id="ed2ab-129">Das Tools-Paket generiert die C# Typen, die die Nachrichten, die in den enthaltenen definierten darstellen *.proto* Dateien.</span><span class="sxs-lookup"><span data-stu-id="ed2ab-129">The tooling package generates the C# types representing the messages defined in the included *.proto* files.</span></span>

<span data-ttu-id="ed2ab-130">Für serverseitige Ressourcen ist eine abstrakte Basisklasse Diensttyp generiert.</span><span class="sxs-lookup"><span data-stu-id="ed2ab-130">For server-side assets, an abstract service base type is generated.</span></span> <span data-ttu-id="ed2ab-131">Der Basistyp enthält die Definitionen aller gRPC Aufrufe innerhalb der *.proto* Datei.</span><span class="sxs-lookup"><span data-stu-id="ed2ab-131">The base type contains the definitions of all the gRPC calls contained in the *.proto* file.</span></span> <span data-ttu-id="ed2ab-132">Erstellen Sie eine konkrete Implementierung, die von diesem Basistyp abgeleitet und implementiert die Logik für die gRPC-Aufrufe.</span><span class="sxs-lookup"><span data-stu-id="ed2ab-132">Create a concrete service implementation that derives from this base type and implements the logic for the gRPC calls.</span></span> <span data-ttu-id="ed2ab-133">Für die `greet.proto`, im Beispiel wird die zuvor beschriebenen einer abstraktes `GreeterBase` Typ, der eine virtuelle enthält `SayHello` Methode generiert.</span><span class="sxs-lookup"><span data-stu-id="ed2ab-133">For the `greet.proto`, the example described previously, an abstract `GreeterBase` type that contains a virtual `SayHello` method is generated.</span></span> <span data-ttu-id="ed2ab-134">Eine konkrete Implementierung `GreeterService` überschreibt die Methode, und implementiert die Logik den gRPC-Aufruf zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="ed2ab-134">A concrete implementation `GreeterService` overrides the method and implements the logic handling the gRPC call.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

<span data-ttu-id="ed2ab-135">Für die clientseitige Objekte wird ein konkretes Clienttyp generiert.</span><span class="sxs-lookup"><span data-stu-id="ed2ab-135">For client-side assets, a concrete client type is generated.</span></span> <span data-ttu-id="ed2ab-136">Die gRPC-Aufrufe in die *.proto* Datei werden in Methoden für den konkreten Typ, der aufgerufen werden kann, übersetzt.</span><span class="sxs-lookup"><span data-stu-id="ed2ab-136">The gRPC calls in the *.proto* file are translated into methods on the concrete type, which can be called.</span></span> <span data-ttu-id="ed2ab-137">Für die `greet.proto`, zuvor beschriebenen einen konkreten Beispiel `GreeterClient` Typ generiert wird.</span><span class="sxs-lookup"><span data-stu-id="ed2ab-137">For the `greet.proto`, the example described previously, a concrete `GreeterClient` type is generated.</span></span> <span data-ttu-id="ed2ab-138">Rufen Sie `GreeterClient.SayHello` eine gRPC-Aufrufs an den Server zu initiieren.</span><span class="sxs-lookup"><span data-stu-id="ed2ab-138">Call `GreeterClient.SayHello` to initiate a gRPC call to the server.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/samples/GrpcGreeterClient/Program.cs?highlight=5-8&name=snippet)]

<span data-ttu-id="ed2ab-139">Standardmäßig werden Server und Client-Ressourcen für die einzelnen generiert *.proto* enthaltene Datei die `<Protobuf>` Elementgruppe.</span><span class="sxs-lookup"><span data-stu-id="ed2ab-139">By default, server and client assets are generated for each *.proto* file included in the `<Protobuf>` item group.</span></span> <span data-ttu-id="ed2ab-140">Um sicherzustellen, dass nur die Serverressourcen werden in einem Serverprojekt generiert die `GrpcServices` -Attributsatz auf `Server`.</span><span class="sxs-lookup"><span data-stu-id="ed2ab-140">To ensure only the server assets are generated in a server project, the `GrpcServices` attribute is set to `Server`.</span></span>

[!code-xml[](~/tutorials/grpc/grpc-start/samples/GrpcGreeter/GrpcGreeter.csproj?highlight=2&range=7-11)]

<span data-ttu-id="ed2ab-141">Auf ähnliche Weise wird das Attribut festgelegt, um `Client` in clientprojekten.</span><span class="sxs-lookup"><span data-stu-id="ed2ab-141">Similarly, the attribute is set to `Client` in client projects.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ed2ab-142">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="ed2ab-142">Additional resources</span></span>

* <xref:grpc/index>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/migration>
