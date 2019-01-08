---
title: Migrieren von "Microsoft.Extensions.Logging" 2.1 auf 2.2 oder 3.0
author: pakrym
description: Informationen Sie zum Migrieren einer ohne ASP.NET Core-Anwendung, die "Microsoft.Extensions.Logging" 2.1 auf 2.2 oder 3.0 verwendet.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 2519ddc02cee5978483bcaef4341a52aad3ba2a6
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54099470"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="c20ad-103">Migrieren von "Microsoft.Extensions.Logging" 2.1 auf 2.2 oder 3.0</span><span class="sxs-lookup"><span data-stu-id="c20ad-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="c20ad-104">Dieser Artikel beschreibt die allgemeinen Schritte zum Migrieren einer ohne ASP.NET Core-Anwendung, verwendet `Microsoft.Extensions.Logging` 2.1 auf 2.2 oder 3.0.</span><span class="sxs-lookup"><span data-stu-id="c20ad-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="c20ad-105">2.1, 2.2</span><span class="sxs-lookup"><span data-stu-id="c20ad-105">2.1 to 2.2</span></span>

<span data-ttu-id="c20ad-106">Erstellen Sie manuell `ServiceCollection` , und rufen Sie `AddLogging`.</span><span class="sxs-lookup"><span data-stu-id="c20ad-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="c20ad-107">2.1-Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c20ad-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="c20ad-108">2.2-Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c20ad-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="c20ad-109">2.1, 3.0</span><span class="sxs-lookup"><span data-stu-id="c20ad-109">2.1 to 3.0</span></span>

<span data-ttu-id="c20ad-110">In 3.0 verwenden `LoggingFactory.Create`.</span><span class="sxs-lookup"><span data-stu-id="c20ad-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="c20ad-111">2.1-Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c20ad-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="c20ad-112">3.0-Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c20ad-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="c20ad-113">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c20ad-113">Additional resources</span></span>

<xref:fundamentals/logging/index>