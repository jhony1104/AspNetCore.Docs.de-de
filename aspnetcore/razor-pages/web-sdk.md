---
title: ASP.net Core-Web-SDK
author: Rick-Anderson
description: Übersicht über Microsoft. net. SDK. Web.
ms.author: riande
ms.date: 01/25/2020
no-loc:
- Blazor
uid: razor-pages/web-sdk
ms.openlocfilehash: 6a9d531efd2188aed525c949bb124914c31119db
ms.sourcegitcommit: fe41cff0b99f3920b727286944e5b652ca301640
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76869765"
---
# <a name="aspnet-core-web-sdk"></a><span data-ttu-id="63f3a-103">ASP.net Core-Web-SDK</span><span class="sxs-lookup"><span data-stu-id="63f3a-103">ASP.NET Core Web SDK</span></span>

### <a name="overview"></a><span data-ttu-id="63f3a-104">Übersicht über</span><span class="sxs-lookup"><span data-stu-id="63f3a-104">Overview</span></span>

<span data-ttu-id="63f3a-105">`Microsoft.NET.Sdk.Web` ist ein [MSBuild-Projekt-SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) zum Erstellen von ASP.net Core-apps.</span><span class="sxs-lookup"><span data-stu-id="63f3a-105">`Microsoft.NET.Sdk.Web` is an [MSBuild project SDK](https://docs.microsoft.com/visualstudio/msbuild/how-to-use-project-sdk) for building ASP.NET Core apps.</span></span> <span data-ttu-id="63f3a-106">Es ist möglich, eine ASP.net Core-App ohne dieses SDK zu erstellen, aber das Web-SDK ist:</span><span class="sxs-lookup"><span data-stu-id="63f3a-106">It's possible to build an ASP.NET Core app without this SDK, however, the Web SDK is:</span></span>

* <span data-ttu-id="63f3a-107">Sie sind auf die Bereitstellung eines erstklassigen Erlebnisses zugeschnitten.</span><span class="sxs-lookup"><span data-stu-id="63f3a-107">Tailored towards providing a first-class experience.</span></span>
* <span data-ttu-id="63f3a-108">Das empfohlene Ziel für die meisten Benutzer.</span><span class="sxs-lookup"><span data-stu-id="63f3a-108">The recommended target for most users.</span></span>

<span data-ttu-id="63f3a-109">Verwenden Sie das Web. SDK in einem Projekt:</span><span class="sxs-lookup"><span data-stu-id="63f3a-109">Use the Web.SDK in a project:</span></span>

  ```xml
  <Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
  </Project>
  ```

<span data-ttu-id="63f3a-110">Mit dem Web SDK aktivierte Funktionen:</span><span class="sxs-lookup"><span data-stu-id="63f3a-110">Features enabled by using the Web SDK:</span></span>

* <span data-ttu-id="63f3a-111">Projekte, die auf .net Core 3,0 oder höher abzielen, verweisen implizit:</span><span class="sxs-lookup"><span data-stu-id="63f3a-111">Projects targeting .NET Core 3.0 or later implicitly reference:</span></span>

  * <span data-ttu-id="63f3a-112">Der [ASP.net Core freigegebenen Frameworks](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="63f3a-112">The [ASP.NET Core shared framework](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="63f3a-113">[Analyse](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) Tools, die für die ASP.net Core-apps entwickelt wurden.</span><span class="sxs-lookup"><span data-stu-id="63f3a-113">[Analyzers](/visualstudio/extensibility/getting-started-with-roslyn-analyzers) designed for building ASP.NET Core apps.</span></span>
* <span data-ttu-id="63f3a-114">Das Web-SDK importiert MSBuild-Ziele, die die Verwendung von Veröffentlichungs Profilen und die Veröffentlichung mit Webbereitstellung ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="63f3a-114">The Web SDK imports MSBuild targets that enable the use of publish profiles and publishing using WebDeploy.</span></span>

### <a name="properties"></a><span data-ttu-id="63f3a-115">Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="63f3a-115">Properties</span></span>

| <span data-ttu-id="63f3a-116">Die Eigenschaften-</span><span class="sxs-lookup"><span data-stu-id="63f3a-116">Property</span></span> | <span data-ttu-id="63f3a-117">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="63f3a-117">Description</span></span> |
| -------- | ----------- |
| `DisableImplicitFrameworkReferences` | <span data-ttu-id="63f3a-118">Deaktiviert den impliziten Verweis auf den `Microsoft.AspNetCore.App` freigegebenen Frameworks.</span><span class="sxs-lookup"><span data-stu-id="63f3a-118">Disables implicit reference to the `Microsoft.AspNetCore.App` shared framework.</span></span> |
| `DisableImplicitAspNetCoreAnalyzers` | <span data-ttu-id="63f3a-119">Deaktiviert den impliziten Verweis auf ASP.net Core-Analyzers.</span><span class="sxs-lookup"><span data-stu-id="63f3a-119">Disables implicit reference to ASP.NET Core analyzers.</span></span> |
| `DisableImplicitComponentsAnalyzers` | <span data-ttu-id="63f3a-120">Deaktiviert den impliziten Verweis auf Razor-Komponenten Analysen beim Entwickeln von Blazor (Server)-Anwendungen.</span><span class="sxs-lookup"><span data-stu-id="63f3a-120">Disables implicit reference to Razor Components analyzers when building Blazor (server) applications.</span></span> |
