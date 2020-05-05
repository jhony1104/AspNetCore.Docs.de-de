---
title: Migrieren von "ClaimsPrincipal. Current"
author: mjrousos
description: Erfahren Sie, wie Sie von ClaimsPrincipal. Current migrieren, um die Identität und Ansprüche des aktuellen authentifizierten Benutzers in ASP.net Core abzurufen.
ms.author: scaddie
ms.custom: mvc
ms.date: 03/26/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/claimsprincipal-current
ms.openlocfilehash: 5f6b5b960eacf176088d9fc60f9ba16014e613fc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776089"
---
# <a name="migrate-from-claimsprincipalcurrent"></a>Migrieren von "ClaimsPrincipal. Current"

In ASP.NET 4. x-Projekten war es üblich, " [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) " zum Abrufen der Identität und Ansprüche des aktuellen authentifizierten Benutzers zu verwenden. In ASP.net Core ist diese Eigenschaft nicht mehr festgelegt. Code, der von ihm abhängig war, muss aktualisiert werden, um die Identität des aktuellen authentifizierten Benutzers auf andere Weise zu erhalten.

## <a name="context-specific-data-instead-of-static-data"></a>Kontextspezifische Daten anstelle von statischen Daten

Wenn Sie ASP.net Core verwenden, werden die Werte `ClaimsPrincipal.Current` von `Thread.CurrentPrincipal` und nicht festgelegt. Diese Eigenschaften stellen beide den statischen Zustand dar, der ASP.net Core im allgemeinen vermeidet. Stattdessen ist die Architektur von ASP.net Core das Abrufen von Abhängigkeiten (z. b. der Identität des aktuellen Benutzers) von kontextspezifischen Dienst Auflistungen (unter Verwendung des Modells für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (di)). Darüber hinaus `Thread.CurrentPrincipal` ist Thread statisch, sodass Änderungen in einigen asynchronen Szenarien möglicherweise nicht persistent gespeichert werden (und `ClaimsPrincipal.Current` standardmäßig `Thread.CurrentPrincipal` nur aufgerufen werden).

Um die möglichen Probleme zu verstehen, die Thread statische Member in asynchronen Szenarien verursachen können, sollten Sie den folgenden Code Ausschnitt in Erwägung ziehen:

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

Im vorangehenden Beispielcode `Thread.CurrentPrincipal` wird der Wert vor und nach dem warten auf einen asynchronen-Befehl festgelegt und überprüft. `Thread.CurrentPrincipal`ist spezifisch für den *Thread* , in dem er festgelegt ist, und die-Methode wird die Ausführung wahrscheinlich in einem anderen Thread nach der Warte Zeit fortsetzen. Folglich ist `Thread.CurrentPrincipal` vorhanden, wenn Sie zum ersten Mal aktiviert ist, aber nach dem- `await Task.Yield()`Aufrufvorgang NULL ist.

Das Überprüfen der Identität des aktuellen Benutzers aus der di-Dienst Sammlung der APP ist auch besser zu testen, da Test Identitäten problemlos eingefügt werden können.

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a>Abrufen des aktuellen Benutzers in einer ASP.net Core-App

Es gibt mehrere Optionen zum Abrufen der aktuellen authentifizierten Benutzer `ClaimsPrincipal` in ASP.net Core anstelle von: `ClaimsPrincipal.Current`

* **Controllerbase. User**. MVC-Controller können mit Ihrer [User](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user) -Eigenschaft auf den aktuellen authentifizierten Benutzer zugreifen.
* **HttpContext. User**. Komponenten mit Zugriff auf die aktuelle `HttpContext` (z. b. Middleware) können die aktuellen Benutzer `ClaimsPrincipal` von " [HttpContext. User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user)" abrufen.
* **Übergeben vom**Aufrufer. Bibliotheken ohne Zugriff auf die aktuelle `HttpContext` werden häufig von Controllern oder middlewarekomponenten aufgerufen, und die Identität des aktuellen Benutzers kann als Argument übermittelt werden.
* **Ihttpcontextaccessor**. Das Projekt, das zu ASP.net Core migriert wird, ist möglicherweise zu groß, um die Identität des aktuellen Benutzers an alle erforderlichen Orte zu übergeben. In solchen Fällen kann [ihttpcontextaccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) als Problem Umgehung verwendet werden. `IHttpContextAccessor`kann auf die aktuelle `HttpContext` zugreifen (sofern vorhanden). Wenn di verwendet wird, finden Sie <xref:fundamentals/httpcontext>Weitere Informationen unter. Eine kurzfristige Lösung, um die Identität des aktuellen Benutzers im Code zu erhalten, der noch nicht aktualisiert wurde, um mit der ASP.net Core di-gesteuerten Architektur zu arbeiten, wäre Folgendes:

  * Stellen `IHttpContextAccessor` Sie im di-Container durch Aufrufen von [addhttpcontextaccessor](https://github.com/aspnet/Hosting/issues/793) in `Startup.ConfigureServices`verfügbar.
  * Beim Start eine Instanz `IHttpContextAccessor` von erhalten und in einer statischen Variablen speichern. Die-Instanz wird für Code verfügbar gemacht, der zuvor den aktuellen Benutzer aus einer statischen Eigenschaft abgerufen hat.
  * Rufen `ClaimsPrincipal` Sie die mit `HttpContextAccessor.HttpContext?.User`dem des aktuellen Benutzers ab. Wenn dieser Code außerhalb des Kontexts einer HTTP-Anforderung verwendet wird, ist `HttpContext` der NULL.

Die letzte Option, die eine `IHttpContextAccessor` Instanz verwendet, die in einer statischen Variablen gespeichert ist, entspricht ASP.net Core Prinzipien (bevorzugt eingefügte Abhängigkeiten von statischen Abhängigkeiten). Planen Sie das Abrufen `IHttpContextAccessor` von Instanzen aus der Abhängigkeitsinjektion. Ein statisches Hilfsprogramm kann jedoch nützlich sein, wenn Sie große vorhandene ASP.net-apps migrieren, die `ClaimsPrincipal.Current`zuvor verwendet wurden.
