---
title: Migrieren von Microsoft. Extensions. Logging 2,1 zu 2,2 oder 3,0
author: pakrym
description: Erfahren Sie, wie Sie eine Non-ASP.net Core-Anwendung, die Microsoft. Extensions. Logging verwendet, von 2,1 zu 2,2 oder 3,0 migrieren.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 2519ddc02cee5978483bcaef4341a52aad3ba2a6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651877"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="abe13-103">Migrieren von Microsoft. Extensions. Logging 2,1 zu 2,2 oder 3,0</span><span class="sxs-lookup"><span data-stu-id="abe13-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="abe13-104">In diesem Artikel werden die allgemeinen Schritte zum Migrieren einer Non-ASP.net Core-Anwendung beschrieben, die `Microsoft.Extensions.Logging` von 2,1 zu 2,2 oder 3,0 verwendet.</span><span class="sxs-lookup"><span data-stu-id="abe13-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="abe13-105">2.1 zu 2.2</span><span class="sxs-lookup"><span data-stu-id="abe13-105">2.1 to 2.2</span></span>

<span data-ttu-id="abe13-106">Erstellen Sie `ServiceCollection` manuell, und rufen Sie `AddLogging`auf.</span><span class="sxs-lookup"><span data-stu-id="abe13-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="abe13-107">2,1 Beispiel:</span><span class="sxs-lookup"><span data-stu-id="abe13-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="abe13-108">2,2 Beispiel:</span><span class="sxs-lookup"><span data-stu-id="abe13-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="abe13-109">2,1 bis 3,0</span><span class="sxs-lookup"><span data-stu-id="abe13-109">2.1 to 3.0</span></span>

<span data-ttu-id="abe13-110">Verwenden Sie in 3,0 `LoggingFactory.Create`.</span><span class="sxs-lookup"><span data-stu-id="abe13-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="abe13-111">2,1 Beispiel:</span><span class="sxs-lookup"><span data-stu-id="abe13-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="abe13-112">3,0 Beispiel:</span><span class="sxs-lookup"><span data-stu-id="abe13-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="abe13-113">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="abe13-113">Additional resources</span></span>

<xref:fundamentals/logging/index>