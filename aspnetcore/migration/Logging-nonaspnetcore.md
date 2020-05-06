---
title: Migrieren von Microsoft. Extensions. Logging 2,1 zu 2,2 oder 3,0
author: pakrym
description: Erfahren Sie, wie Sie eine Non-ASP.net Core-Anwendung, die Microsoft. Extensions. Logging verwendet, von 2,1 zu 2,2 oder 3,0 migrieren.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 3a84d53cb925a518f6c3e244dd342a3228a1fe17
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777058"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="6621a-103">Migrieren von Microsoft. Extensions. Logging 2,1 zu 2,2 oder 3,0</span><span class="sxs-lookup"><span data-stu-id="6621a-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="6621a-104">In diesem Artikel werden die allgemeinen Schritte zum Migrieren einer Non-ASP.net Core- `Microsoft.Extensions.Logging` Anwendung beschrieben, die zwischen 2,1 und 2,2 oder 3,0 verwendet.</span><span class="sxs-lookup"><span data-stu-id="6621a-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="6621a-105">2.1 zu 2.2</span><span class="sxs-lookup"><span data-stu-id="6621a-105">2.1 to 2.2</span></span>

<span data-ttu-id="6621a-106">Erstellen `ServiceCollection` Sie manuell und `AddLogging`rufen Sie auf.</span><span class="sxs-lookup"><span data-stu-id="6621a-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="6621a-107">2,1 Beispiel:</span><span class="sxs-lookup"><span data-stu-id="6621a-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="6621a-108">2,2 Beispiel:</span><span class="sxs-lookup"><span data-stu-id="6621a-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="6621a-109">2,1 bis 3,0</span><span class="sxs-lookup"><span data-stu-id="6621a-109">2.1 to 3.0</span></span>

<span data-ttu-id="6621a-110">Verwenden `LoggingFactory.Create`Sie in 3,0.</span><span class="sxs-lookup"><span data-stu-id="6621a-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="6621a-111">2,1 Beispiel:</span><span class="sxs-lookup"><span data-stu-id="6621a-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="6621a-112">3,0 Beispiel:</span><span class="sxs-lookup"><span data-stu-id="6621a-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="6621a-113">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="6621a-113">Additional resources</span></span>

<xref:fundamentals/logging/index>