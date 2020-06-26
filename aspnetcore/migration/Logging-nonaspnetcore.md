---
title: Migrieren von Microsoft. Extensions. Logging 2,1 zu 2,2 oder 3,0
author: pakrym
description: Erfahren Sie, wie Sie eine Non-ASP.net Core-Anwendung, die Microsoft. Extensions. Logging verwendet, von 2,1 zu 2,2 oder 3,0 migrieren.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 0c85ca637c1e93bbde93c7d5d12408637476558e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399789"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a>Migrieren von Microsoft. Extensions. Logging 2,1 zu 2,2 oder 3,0

In diesem Artikel werden die allgemeinen Schritte zum Migrieren einer Non-ASP.net Core-Anwendung beschrieben, die `Microsoft.Extensions.Logging` zwischen 2,1 und 2,2 oder 3,0 verwendet.

## <a name="21-to-22"></a>2.1 zu 2.2

Erstellen Sie manuell `ServiceCollection` und rufen Sie auf `AddLogging` .

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

Verwenden Sie in 3,0 `LoggingFactory.Create` .

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

* Das [nuget-Paket Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).
* <xref:fundamentals/logging/index>
