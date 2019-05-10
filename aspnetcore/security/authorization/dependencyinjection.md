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
# <a name="dependency-injection-in-requirement-handlers-in-aspnet-core"></a><span data-ttu-id="982b2-103">Abhängigkeitsinjektion in anforderungshandlern in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="982b2-103">Dependency injection in requirement handlers in ASP.NET Core</span></span>

<a name="security-authorization-di"></a>

<span data-ttu-id="982b2-104">[Müssen Autorisierung Handler registriert werden](xref:security/authorization/policies#handler-registration) in der Sammlung von Diensten während der Konfiguration (mit [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)).</span><span class="sxs-lookup"><span data-stu-id="982b2-104">[Authorization handlers must be registered](xref:security/authorization/policies#handler-registration) in the service collection during configuration (using [dependency injection](xref:fundamentals/dependency-injection)).</span></span>

<span data-ttu-id="982b2-105">Nehmen wir an, dass Sie ein Repository mit Regeln konnten, die Sie in einem autorisierungshandler auswerten möchten, und dieses Repository in der Sammlung von Diensten registriert wurde.</span><span class="sxs-lookup"><span data-stu-id="982b2-105">Suppose you had a repository of rules you wanted to evaluate inside an authorization handler and that repository was registered in the service collection.</span></span> <span data-ttu-id="982b2-106">Autorisierung wird aufgelöst, und fügen, die in dem Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="982b2-106">Authorization will resolve and inject that into your constructor.</span></span>

<span data-ttu-id="982b2-107">Beispielsweise, wenn Sie ASP verwenden möchten. NET der Infrastruktur, die Sie einfügen möchten Protokollierung `ILoggerFactory` in Ihren Handler.</span><span class="sxs-lookup"><span data-stu-id="982b2-107">For example, if you wanted to use ASP.NET's logging infrastructure you would want to inject `ILoggerFactory` into your handler.</span></span> <span data-ttu-id="982b2-108">Solcher Handler sieht etwa wie auf:</span><span class="sxs-lookup"><span data-stu-id="982b2-108">Such a handler might look like:</span></span>

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

<span data-ttu-id="982b2-109">Registrieren Sie würde den Handler mit `services.AddSingleton()`:</span><span class="sxs-lookup"><span data-stu-id="982b2-109">You would register the handler with `services.AddSingleton()`:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
```

<span data-ttu-id="982b2-110">Eine Instanz des Handlers wird erstellt, wenn die Anwendung gestartet wird, und DI-wird, die den registrierten einfügen `ILoggerFactory` in Ihrem Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="982b2-110">An instance of the handler will be created when your application starts, and DI will inject the registered `ILoggerFactory` into your constructor.</span></span>

> [!NOTE]
> <span data-ttu-id="982b2-111">Handler, die Entity Framework verwenden, sollten nicht als Singletons registriert werden.</span><span class="sxs-lookup"><span data-stu-id="982b2-111">Handlers that use Entity Framework shouldn't be registered as singletons.</span></span>
