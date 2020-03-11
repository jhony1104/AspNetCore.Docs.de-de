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
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a>Migrieren von Microsoft. Extensions. Logging 2,1 zu 2,2 oder 3,0

In diesem Artikel werden die allgemeinen Schritte zum Migrieren einer Non-ASP.net Core-Anwendung beschrieben, die `Microsoft.Extensions.Logging` von 2,1 zu 2,2 oder 3,0 verwendet.

## <a name="21-to-22"></a>2.1 zu 2.2

Erstellen Sie `ServiceCollection` manuell, und rufen Sie `AddLogging`auf.

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

Verwenden Sie in 3,0 `LoggingFactory.Create`.

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