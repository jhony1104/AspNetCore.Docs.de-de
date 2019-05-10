---
title: Migrieren von "Microsoft.Extensions.Logging" 2.1 auf 2.2 oder 3.0
author: pakrym
description: Informationen Sie zum Migrieren einer ohne ASP.NET Core-Anwendung, die "Microsoft.Extensions.Logging" 2.1 auf 2.2 oder 3.0 verwendet.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 2519ddc02cee5978483bcaef4341a52aad3ba2a6
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892457"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="cc05f-103">Migrieren von "Microsoft.Extensions.Logging" 2.1 auf 2.2 oder 3.0</span><span class="sxs-lookup"><span data-stu-id="cc05f-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="cc05f-104">Dieser Artikel beschreibt die allgemeinen Schritte zum Migrieren einer ohne ASP.NET Core-Anwendung, verwendet `Microsoft.Extensions.Logging` 2.1 auf 2.2 oder 3.0.</span><span class="sxs-lookup"><span data-stu-id="cc05f-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="cc05f-105">2.1 zu 2.2</span><span class="sxs-lookup"><span data-stu-id="cc05f-105">2.1 to 2.2</span></span>

<span data-ttu-id="cc05f-106">Erstellen Sie manuell `ServiceCollection` , und rufen Sie `AddLogging`.</span><span class="sxs-lookup"><span data-stu-id="cc05f-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="cc05f-107">2.1-Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cc05f-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="cc05f-108">2.2-Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cc05f-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="cc05f-109">2.1, 3.0</span><span class="sxs-lookup"><span data-stu-id="cc05f-109">2.1 to 3.0</span></span>

<span data-ttu-id="cc05f-110">In 3.0 verwenden `LoggingFactory.Create`.</span><span class="sxs-lookup"><span data-stu-id="cc05f-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="cc05f-111">2.1-Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cc05f-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="cc05f-112">3.0-Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cc05f-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="cc05f-113">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="cc05f-113">Additional resources</span></span>

<xref:fundamentals/logging/index>