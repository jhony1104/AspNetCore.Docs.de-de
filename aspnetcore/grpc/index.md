---
title: Einführung in gRPC in ASP.NET Core
author: juntaoluo
description: Informationen zu gRPC-Diensten mit dem Kestrel-Server und dem ASP.NET Core-Stapel
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 02/26/2019
uid: grpc/index
ms.openlocfilehash: dd1c42744bfda965df91ea1fcc0b71814317b969
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65085563"
---
# <a name="introduction-to-grpc-on-aspnet-core"></a><span data-ttu-id="bef04-103">Einführung in gRPC in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bef04-103">Introduction to gRPC on ASP.NET Core</span></span>

<span data-ttu-id="bef04-104">Von [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="bef04-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="bef04-105">[gRPC](https://grpc.io/docs/guides/) ist ein sprachunabhängiges RPC-Framework mit hoher Leistung (Remote Procedure Call, Remoteprozeduraufruf).</span><span class="sxs-lookup"><span data-stu-id="bef04-105">[gRPC](https://grpc.io/docs/guides/) is a language agnostic, high-performance Remote Procedure Call (RPC) framework.</span></span> <span data-ttu-id="bef04-106">Weitere grundlegende Informationen zu gRPC finden Sie in der [gRPC-Dokumentation](https://grpc.io/docs/).</span><span class="sxs-lookup"><span data-stu-id="bef04-106">For more on gRPC fundamentals, see the [gRPC documentation page](https://grpc.io/docs/).</span></span>

<span data-ttu-id="bef04-107">Im Folgenden sind die Hauptvorteile von gRPC aufgelistet:</span><span class="sxs-lookup"><span data-stu-id="bef04-107">The main benefits of gRPC are:</span></span>
* <span data-ttu-id="bef04-108">Ein modernes, hochleistungsfähiges und reduziertes Framework für Remoteprozeduraufrufe</span><span class="sxs-lookup"><span data-stu-id="bef04-108">Modern high-performance lightweight RPC framework.</span></span>
* <span data-ttu-id="bef04-109">Contract First-API-Entwicklung mit standardmäßiger Verwendung von Protokollpuffern, wodurch sprachunabhängige Implementierungen möglich sind</span><span class="sxs-lookup"><span data-stu-id="bef04-109">Contract-first API development, using Protocol Buffers by default, allowing for language agnostic implementations.</span></span>
* <span data-ttu-id="bef04-110">Für viele Sprachen verfügbare Tools zur Generierung stark typisierter Server und Clients</span><span class="sxs-lookup"><span data-stu-id="bef04-110">Tooling available for many languages to generate strongly-typed servers and clients.</span></span>
* <span data-ttu-id="bef04-111">Unterstützung von Clients, Servern und bidirektionalen Streamingaufrufen</span><span class="sxs-lookup"><span data-stu-id="bef04-111">Supports client, server, and bi-directional streaming calls.</span></span>
* <span data-ttu-id="bef04-112">Reduzierte Netzwerkauslastung mit binärer Protobuf-Serialisierung</span><span class="sxs-lookup"><span data-stu-id="bef04-112">Reduced network usage with Protobuf binary serialization.</span></span>

<span data-ttu-id="bef04-113">gRPC ist für die folgenden Komponenten besonders geeignet:</span><span class="sxs-lookup"><span data-stu-id="bef04-113">These benefits make gRPC ideal for:</span></span>
* <span data-ttu-id="bef04-114">Einfache Microservices, bei denen Effizienz essentiell ist</span><span class="sxs-lookup"><span data-stu-id="bef04-114">Lightweight microservices where efficiency is critical.</span></span>
* <span data-ttu-id="bef04-115">Mehrsprachige Systeme, bei denen mehrere Sprachen für die Entwicklung erforderlich sind</span><span class="sxs-lookup"><span data-stu-id="bef04-115">Polyglot systems where multiple languages are required for development.</span></span>
* <span data-ttu-id="bef04-116">Point-to-Point-Dienste, die in Echtzeit Streaminganforderungen oder -antworten verarbeiten müssen</span><span class="sxs-lookup"><span data-stu-id="bef04-116">Point-to-point real-time services that need to handle streaming requests or responses.</span></span>

<span data-ttu-id="bef04-117">Eine C#-Implementierung ist derzeit auf der offiziellen [gRPC-Seite](https://grpc.io/docs/quickstart/csharp.html) verfügbar. Die aktuelle Implementierung basiert auf der nativen Bibliothek, die in C geschrieben ist (gRPC [C-core](https://grpc.io/blog/grpc-stacks) (Visualisierung von gRPC-Sprachstapeln)).</span><span class="sxs-lookup"><span data-stu-id="bef04-117">While a C# implementation is currently available on the official [gRPC page](https://grpc.io/docs/quickstart/csharp.html), the current implementation relies on the native library written in C (gRPC [C-core](https://grpc.io/blog/grpc-stacks)).</span></span> <span data-ttu-id="bef04-118">Wir arbeiten derzeit an der Bereitstellung einer neuen Implementierung, die vollständig verwaltet ist und die auf dem Kestrel-HTTP-Server sowie auf dem ASP.NET Core-Stapel basiert.</span><span class="sxs-lookup"><span data-stu-id="bef04-118">Work is currently in progress to provide a new implementation based on the Kestrel HTTP server and the ASP.NET Core stack that is fully managed.</span></span> <span data-ttu-id="bef04-119">In den folgenden Dokumenten finden Sie eine Einführung in die Erstellung von gRPC-Diensten mit dieser neuen Implementierung.</span><span class="sxs-lookup"><span data-stu-id="bef04-119">The following documents provide an introduction to building gRPC services with this new implementation.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bef04-120">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="bef04-120">Additional resources</span></span>

* <xref:grpc/basics>
* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/aspnetcore>
* <xref:grpc/migration>