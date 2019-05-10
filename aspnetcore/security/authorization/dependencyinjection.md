---
title: Abhängigkeitsinjektion in anforderungshandlern in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie anforderungshandlern Autorisierung in einer ASP.NET Core-app mithilfe der Abhängigkeitsinjektion einfügen.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 71d563e11d308a95c08e6d012d3a071f4697d2de
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896367"
---
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a>Abhängigkeitsinjektion in anforderungshandlern in ASP.NET Core

<a name="security-authorization-di"></a>

[Müssen Autorisierung Handler registriert werden](xref:security/authorization/policies#handler-registration) in der Sammlung von Diensten während der Konfiguration (mit [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)).

Nehmen wir an, dass Sie ein Repository mit Regeln konnten, die Sie in einem autorisierungshandler auswerten möchten, und dieses Repository in der Sammlung von Diensten registriert wurde. Autorisierung wird aufgelöst, und fügen, die in dem Konstruktor.

Beispielsweise, wenn Sie ASP verwenden möchten. NET der Infrastruktur, die Sie einfügen möchten Protokollierung `ILoggerFactory` in Ihren Handler. Solcher Handler sieht etwa wie auf:

```csharp
public class LoggingAuthorizationHandler : AuthorizationHandler<MyRequirement>
   {
       ILogger _logger;

       public LoggingAuthorizationHandler(ILoggerFactory loggerFactory)
       {
           _logger = loggerFactory.CreateLogger(this.GetType().FullName);
       }

       protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, MyRequirement requirement)
       {
           _logger.LogInformation("Inside my handler");
           // Check if the requirement is fulfilled.
           return Task.CompletedTask;
       }
   }
   ```

Registrieren Sie würde den Handler mit `services.AddSingleton()`:

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

Eine Instanz des Handlers wird erstellt, wenn die Anwendung gestartet wird, und DI-wird, die den registrierten einfügen `ILoggerFactory` in Ihrem Konstruktor.

> [!NOTE]
> Handler, die Entity Framework verwenden, sollten nicht als Singletons registriert werden.
