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
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a>Migrieren von Microsoft. Extensions. Logging 2,1 zu 2,2 oder 3,0

In diesem Artikel werden die allgemeinen Schritte zum Migrieren einer Non-ASP.net Core- `Microsoft.Extensions.Logging` Anwendung beschrieben, die zwischen 2,1 und 2,2 oder 3,0 verwendet.

## <a name="21-to-22"></a>2.1 zu 2.2

Erstellen `ServiceCollection` Sie manuell und `AddLogging`rufen Sie auf.

2,1 Beispiel:

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

2,2 Beispiel:

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a>2,1 bis 3,0

Verwenden `LoggingFactory.Create`Sie in 3,0.

2,1 Beispiel:

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

3,0 Beispiel:

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

<xref:fundamentals/logging/index>