---
title: Migrieren von ClaimsPrincipal.Current
author: mjrousos
description: Informationen Sie zum Migrieren von ClaimsPrincipal.Current zum Abrufen des aktuellen authentifizierten Benutzers Identität und Ansprüche in ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 03/26/2019
uid: migration/claimsprincipal-current
ms.openlocfilehash: 526cc3cf3a58a656e2a1b162cfaccacc7694dc51
ms.sourcegitcommit: 687ffb15ebe65379f75c84739ea851d5a0d788b7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488641"
---
# <a name="migrate-from-claimsprincipalcurrent"></a>Migrieren von ClaimsPrincipal.Current

In ASP.NET 4.x-Projekten, war es üblich, dass verwendet [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) zum Abrufen der aktuellen authentifizierten Identität und Ansprüche des Benutzers. In ASP.NET Core ist diese Eigenschaft nicht mehr festgelegt. Code, der davon abhängig war, muss aktualisiert werden, um die aktuellen authentifizierten Identität des Benutzers über eine unterschiedliche zu erhalten.

## <a name="context-specific-data-instead-of-static-data"></a>Kontextspezifische Daten anstelle von statischen Daten

Bei Verwendung von ASP.NET Core, die Werte von `ClaimsPrincipal.Current` und `Thread.CurrentPrincipal` werden nicht festgelegt. Diese Eigenschaften, die sowohl statischen Zustand darstellen, die ASP.NET Core wird in der Regel vermieden. Stattdessen wird die ASP.NET Core-Architektur zum Abrufen von Abhängigkeiten (z. B. die aktuelle Identität des Benutzers) aus der Dienstkontext-spezifischen Dienst Sammlungen (mit der [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (DI)-Modell). Mehr Neuigkeiten `Thread.CurrentPrincipal` ist statisch, damit die Änderungen in einigen asynchronen Szenarien nicht beibehalten werden können (und `ClaimsPrincipal.Current` ruft nur `Thread.CurrentPrincipal` standardmäßig).

Um zu verstehen, die Arten von Problemen Thread können statische Member führen, den folgende Codeausschnitt in asynchronen Szenarien zu berücksichtigen:

```csharp
// Create a ClaimsPrincipal and set Thread.CurrentPrincipal
var identity = new ClaimsIdentity();
identity.AddClaim(new Claim(ClaimTypes.Name, "User1"));
Thread.CurrentPrincipal = new ClaimsPrincipal(identity);

// Check the current user
Console.WriteLine($"Current user: {Thread.CurrentPrincipal?.Identity.Name}");

// For the method to complete asynchronously
await Task.Yield();

// Check the current user after
Console.WriteLine($"Current user: {Thread.CurrentPrincipal?.Identity.Name}");
```

Im vorherigen Beispiel Code wird `Thread.CurrentPrincipal` und dessen Wert überprüft wird, vor und nach dem Warten eines asynchronen Aufrufs. `Thread.CurrentPrincipal` bezieht sich auf die *Thread* auf dem sie festgelegt ist, und die Methode ist wahrscheinlich die Ausführung in einem anderen Thread nach dem await-Ausdruck fortgesetzt werden. Folglich `Thread.CurrentPrincipal` ist vorhanden, wenn er zuerst daraufhin überprüft, aber nach dem Aufruf von null ist, `await Task.Yield()`.

Abrufen der Identität des aktuellen Benutzers aus der app DI-Dienst-Auflistung ist besser getestet werden, zu, da der Test-Identitäten problemlos eingefügt werden können.

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a>Rufen Sie die aktuelle Benutzer in einer ASP.NET Core-app

Es gibt mehrere Optionen zum Abrufen des aktuellen authentifizierten Benutzers `ClaimsPrincipal` in ASP.NET Core anstelle von `ClaimsPrincipal.Current`:

* **ControllerBase.User**. MVC-Controller stehen derzeit authentifizierten Benutzer mit ihren [Benutzer](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user) Eigenschaft.
* **HttpContext.User**. Komponenten mit Zugriff auf die aktuelle `HttpContext` (z. B. Middleware) erhalten des aktuellen Benutzers `ClaimsPrincipal` aus [HttpContext.User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user).
* **Vom Aufrufer übergebenen**. Bibliotheken ohne Zugriff auf die aktuelle `HttpContext` werden häufig von Controllern oder Middleware-Komponenten bezeichnet und kann die aktuelle Identität des Benutzers als Argument übergeben.
* **IHttpContextAccessor**. Das Projekt, die Migration zu ASP.NET Core möglicherweise zu groß, um die Identität des aktuellen Benutzers problemlos an alle erforderlichen Stellen zu übergeben. In solchen Fällen [IHttpContextAccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) kann verwendet werden, dieses Problem zu umgehen. `IHttpContextAccessor` ist auf dem aktuellen `HttpContext` (falls vorhanden). Es wäre eine kurzfristige Lösung, die beim Abrufen der Identität des aktuellen Benutzers in Code, der noch nicht zum Arbeiten mit ASP.NET Core DI-driven Architecture aktualisiert wurden:

  * Stellen `IHttpContextAccessor` zur Verfügung, in der DI-Container durch Aufrufen von [AddHttpContextAccessor](https://github.com/aspnet/Hosting/issues/793) in `Startup.ConfigureServices`.
  * Rufen Sie eine Instanz des `IHttpContextAccessor` während des Starts und in eine statische Variable zu speichern. Die Instanz wird für Code verfügbar gemacht, die zuvor den aktuellen Benutzer über eine statische Eigenschaft abgerufen wurde.
  * Abrufen des aktuellen Benutzers `ClaimsPrincipal` mit `HttpContextAccessor.HttpContext?.User`. Wenn dieser Code, außerhalb des Kontexts einer HTTP-Anforderung verwendet wird die `HttpContext` ist null.

Der letzte option, mit einem `IHttpContextAccessor` Instanz in einer statischen Variablen gespeichert ist, wird die ASP.NET Core-Prinzipien (vorzugsweise eingefügte Abhängigkeiten statische Abhängigkeiten), umgekehrte. Schließlich abrufen möchten, um `IHttpContextAccessor` stattdessen über Dependency Injection-Instanzen. Kann eine statische Hilfsmethode nützlich Brücke, die jedoch sein, wenn große vorhandene ASP.NET-Anwendungen zu migrieren, die zuvor verwendet haben `ClaimsPrincipal.Current`.
