---
title: Wählen zwischen ASP.NET 4.x und ASP.NET Core
author: rick-anderson
description: Erklärt ASP.NET Core vs. ASP.NET 4.x und wie man sich für eines von beiden entscheidet.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: 1fb81d5a54cf332ca473af8fbe1841813a127be7
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775881"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a><span data-ttu-id="4fa4a-103">Wählen zwischen ASP.NET 4.x und ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4fa4a-103">Choose between ASP.NET 4.x and ASP.NET Core</span></span>

<span data-ttu-id="4fa4a-104">ASP.NET Core ist eine Neugestaltung von ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="4fa4a-104">ASP.NET Core is a redesign of ASP.NET 4.x.</span></span> <span data-ttu-id="4fa4a-105">Dieser Artikel listet die Unterschiede auf.</span><span class="sxs-lookup"><span data-stu-id="4fa4a-105">This article lists the differences between them.</span></span>

## <a name="aspnet-core"></a><span data-ttu-id="4fa4a-106">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4fa4a-106">ASP.NET Core</span></span>

<span data-ttu-id="4fa4a-107">ASP.NET Core ist ein plattformübergreifendes Open-Source-Framework zum Erstellen moderner, cloudbasierter Web-Apps unter Windows, macOS oder Linux.</span><span class="sxs-lookup"><span data-stu-id="4fa4a-107">ASP.NET Core is an open-source, cross-platform framework for building modern, cloud-based web apps on Windows, macOS, or Linux.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a><span data-ttu-id="4fa4a-108">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="4fa4a-108">ASP.NET 4.x</span></span>

<span data-ttu-id="4fa4a-109">ASP.NET 4.x ist ein ausgereiftes Framework, das sämtliche Dienste bietet, die zum Erstellen erstklassiger serverbasierter Web-Apps unter Windows für Unternehmen erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="4fa4a-109">ASP.NET 4.x is a mature framework that provides the services needed to build enterprise-grade, server-based web apps on Windows.</span></span>

## <a name="framework-selection"></a><span data-ttu-id="4fa4a-110">Auswahl des Frameworks</span><span class="sxs-lookup"><span data-stu-id="4fa4a-110">Framework selection</span></span>

<span data-ttu-id="4fa4a-111">Die folgende Tabelle vergleicht ASP.NET Core mit ASP.NET 4.x.</span><span class="sxs-lookup"><span data-stu-id="4fa4a-111">The following table compares ASP.NET Core to ASP.NET 4.x.</span></span>

| <span data-ttu-id="4fa4a-112">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4fa4a-112">ASP.NET Core</span></span> | <span data-ttu-id="4fa4a-113">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="4fa4a-113">ASP.NET 4.x</span></span> |
|---|---|
|<span data-ttu-id="4fa4a-114">Entwickeln für Windows, macOS oder Linux</span><span class="sxs-lookup"><span data-stu-id="4fa4a-114">Build for Windows, macOS, or Linux</span></span>|<span data-ttu-id="4fa4a-115">Entwickeln für Windows</span><span class="sxs-lookup"><span data-stu-id="4fa4a-115">Build for Windows</span></span>|
|<span data-ttu-id="4fa4a-116">[Razor Pages](xref:razor-pages/index) werden für das Erstellen einer Webbenutzeroberfläche mit ASP.NET Core 2.x empfohlen.</span><span class="sxs-lookup"><span data-stu-id="4fa4a-116">[Razor Pages](xref:razor-pages/index) is the recommended approach to create a Web UI as of ASP.NET Core 2.x.</span></span> <span data-ttu-id="4fa4a-117">Weitere Informationen finden Sie unter [MVC](xref:mvc/overview), [Web-API](xref:tutorials/first-web-api) und [SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="4fa4a-117">See also [MVC](xref:mvc/overview), [Web API](xref:tutorials/first-web-api), and [SignalR](xref:signalr/introduction).</span></span>|<span data-ttu-id="4fa4a-118">Verwenden von [Web Forms](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [Web-API](/aspnet/web-api/), [Webhooks](/aspnet/webhooks/) oder [Web Pages](/aspnet/web-pages)</span><span class="sxs-lookup"><span data-stu-id="4fa4a-118">Use [Web Forms](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [Web API](/aspnet/web-api/), [WebHooks](/aspnet/webhooks/), or [Web Pages](/aspnet/web-pages)</span></span>|
|<span data-ttu-id="4fa4a-119">Mehrere Versionen pro Computer</span><span class="sxs-lookup"><span data-stu-id="4fa4a-119">Multiple versions per machine</span></span>|<span data-ttu-id="4fa4a-120">Eine Version pro Computer</span><span class="sxs-lookup"><span data-stu-id="4fa4a-120">One version per machine</span></span>|
|<span data-ttu-id="4fa4a-121">Entwickeln mit [Visual Studio](https://visualstudio.microsoft.com/vs/), [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/) oder [Visual Studio Code](https://code.visualstudio.com/) unter Verwendung von C# oder F#</span><span class="sxs-lookup"><span data-stu-id="4fa4a-121">Develop with [Visual Studio](https://visualstudio.microsoft.com/vs/), [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/), or [Visual Studio Code](https://code.visualstudio.com/) using C# or F#</span></span>|<span data-ttu-id="4fa4a-122">Entwickeln mit [Visual Studio](https://visualstudio.microsoft.com/vs/) unter Verwendung von C#, VB oder F#</span><span class="sxs-lookup"><span data-stu-id="4fa4a-122">Develop with [Visual Studio](https://visualstudio.microsoft.com/vs/) using C#, VB, or F#</span></span>|
|<span data-ttu-id="4fa4a-123">Höhere Leistung als ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="4fa4a-123">Higher performance than ASP.NET 4.x</span></span>|<span data-ttu-id="4fa4a-124">Gute Leistung</span><span class="sxs-lookup"><span data-stu-id="4fa4a-124">Good performance</span></span>|
|[<span data-ttu-id="4fa4a-125">Verwenden der .NET Core Runtime</span><span class="sxs-lookup"><span data-stu-id="4fa4a-125">Use .NET Core runtime</span></span>](/dotnet/standard/choosing-core-framework-server)|<span data-ttu-id="4fa4a-126">Verwenden der .NET Framework-Laufzeit</span><span class="sxs-lookup"><span data-stu-id="4fa4a-126">Use .NET Framework runtime</span></span>|

<span data-ttu-id="4fa4a-127">Weitere Informationen über Unterstützung für ASP.NET Core 2.x in .NET Framework finden Sie unter [ASP.NET Core targeting .NET Framework](xref:index#target-framework).</span><span class="sxs-lookup"><span data-stu-id="4fa4a-127">See [ASP.NET Core targeting .NET Framework](xref:index#target-framework) for information on ASP.NET Core 2.x support on .NET Framework.</span></span>

## <a name="aspnet-core-scenarios"></a><span data-ttu-id="4fa4a-128">ASP.NET Core-Szenarien</span><span class="sxs-lookup"><span data-stu-id="4fa4a-128">ASP.NET Core scenarios</span></span>

* [<span data-ttu-id="4fa4a-129">Websites</span><span class="sxs-lookup"><span data-stu-id="4fa4a-129">Websites</span></span>](xref:tutorials/first-mvc-app/index)
* [<span data-ttu-id="4fa4a-130">APIs</span><span class="sxs-lookup"><span data-stu-id="4fa4a-130">APIs</span></span>](xref:tutorials/first-web-api)
* [<span data-ttu-id="4fa4a-131">Echtzeit</span><span class="sxs-lookup"><span data-stu-id="4fa4a-131">Real-time</span></span>](xref:signalr/introduction)
* [<span data-ttu-id="4fa4a-132">Bereitstellen einer ASP.NET Core-App in Azure</span><span class="sxs-lookup"><span data-stu-id="4fa4a-132">Deploy an ASP.NET Core app to Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a><span data-ttu-id="4fa4a-133">ASP.NET 4.x-Szenarios</span><span class="sxs-lookup"><span data-stu-id="4fa4a-133">ASP.NET 4.x scenarios</span></span>

* [<span data-ttu-id="4fa4a-134">Websites</span><span class="sxs-lookup"><span data-stu-id="4fa4a-134">Websites</span></span>](/aspnet/mvc)
* [<span data-ttu-id="4fa4a-135">APIs</span><span class="sxs-lookup"><span data-stu-id="4fa4a-135">APIs</span></span>](/aspnet/web-api)
* [<span data-ttu-id="4fa4a-136">Echtzeit</span><span class="sxs-lookup"><span data-stu-id="4fa4a-136">Real-time</span></span>](/aspnet/signalr)
* [<span data-ttu-id="4fa4a-137">Erstellen einer ASP.NET 4.x-Web-App in Azure</span><span class="sxs-lookup"><span data-stu-id="4fa4a-137">Create an ASP.NET 4.x web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a><span data-ttu-id="4fa4a-138">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="4fa4a-138">Additional resources</span></span>

* [<span data-ttu-id="4fa4a-139">Einführung in ASP.NET</span><span class="sxs-lookup"><span data-stu-id="4fa4a-139">Introduction to ASP.NET</span></span>](/aspnet/overview)
* [<span data-ttu-id="4fa4a-140">Einführung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4fa4a-140">Introduction to ASP.NET Core</span></span>](xref:index)
* <xref:host-and-deploy/azure-apps/index>
