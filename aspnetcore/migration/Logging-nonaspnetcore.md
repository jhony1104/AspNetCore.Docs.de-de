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
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a>Migrieren von "Microsoft.Extensions.Logging" 2.1 auf 2.2 oder 3.0

Dieser Artikel beschreibt die allgemeinen Schritte zum Migrieren einer ohne ASP.NET Core-Anwendung, verwendet `Microsoft.Extensions.Logging` 2.1 auf 2.2 oder 3.0.

## <a name="21-to-22"></a>2.1 zu 2.2

Erstellen Sie manuell `ServiceCollection` , und rufen Sie `AddLogging`.

2.1-Beispiel:

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

2.2-Beispiel:

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a>2.1, 3.0

In 3.0 verwenden `LoggingFactory.Create`.

2.1-Beispiel:

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

3.0-Beispiel:

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

<xref:fundamentals/logging/index>