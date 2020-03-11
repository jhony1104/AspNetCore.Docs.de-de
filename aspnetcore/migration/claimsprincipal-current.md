---
title: Migrieren von "ClaimsPrincipal. Current"
author: mjrousos
description: Erfahren Sie, wie Sie von ClaimsPrincipal. Current migrieren, um die Identität und Ansprüche des aktuellen authentifizierten Benutzers in ASP.net Core abzurufen.
ms.author: scaddie
ms.custom: mvc
ms.date: 03/26/2019
uid: migration/claimsprincipal-current
ms.openlocfilehash: f7472f5b851d3869da3d26b881e276ce4ca004fb
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651871"
---
# <a name="migrate-from-claimsprincipalcurrent"></a>Migrieren von "ClaimsPrincipal. Current"

In ASP.NET 4. x-Projekten war es üblich, " [ClaimsPrincipal. Current](/dotnet/api/system.security.claims.claimsprincipal.current) " zum Abrufen der Identität und Ansprüche des aktuellen authentifizierten Benutzers zu verwenden. In ASP.net Core ist diese Eigenschaft nicht mehr festgelegt. Code, der von ihm abhängig war, muss aktualisiert werden, um die Identität des aktuellen authentifizierten Benutzers auf andere Weise zu erhalten.

## <a name="context-specific-data-instead-of-static-data"></a>Kontextspezifische Daten anstelle von statischen Daten

Wenn Sie ASP.net Core verwenden, werden die Werte von `ClaimsPrincipal.Current` und `Thread.CurrentPrincipal` nicht festgelegt. Diese Eigenschaften stellen beide den statischen Zustand dar, der ASP.net Core im allgemeinen vermeidet. Stattdessen ist die Architektur von ASP.net Core das Abrufen von Abhängigkeiten (z. b. der Identität des aktuellen Benutzers) von kontextspezifischen Dienst Auflistungen (unter Verwendung des Modells für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (di)). Weitere `Thread.CurrentPrincipal` ist Thread statisch, sodass Änderungen in einigen asynchronen Szenarios möglicherweise nicht persistent gespeichert werden (und `ClaimsPrincipal.Current` lediglich standardmäßig `Thread.CurrentPrincipal` aufruft).

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

Der vorangehende Beispielcode legt `Thread.CurrentPrincipal` fest und überprüft seinen Wert vor und nach dem warten auf einen asynchronen-Befehl. `Thread.CurrentPrincipal` ist spezifisch für den *Thread* , in dem er festgelegt ist, und die Methode wird die Ausführung wahrscheinlich in einem anderen Thread nach der Warte Zeit fortsetzen. Folglich ist `Thread.CurrentPrincipal` vorhanden, wenn Sie zum ersten Mal aktiviert ist, aber nach dem Aufruf`await Task.Yield()`NULL ist.

Das Überprüfen der Identität des aktuellen Benutzers aus der di-Dienst Sammlung der APP ist auch besser zu testen, da Test Identitäten problemlos eingefügt werden können.

## <a name="retrieve-the-current-user-in-an-aspnet-core-app"></a>Abrufen des aktuellen Benutzers in einer ASP.net Core-App

Es gibt mehrere Optionen zum Abrufen der `ClaimsPrincipal` des aktuellen authentifizierten Benutzers in ASP.net Core anstelle von `ClaimsPrincipal.Current`:

* **Controllerbase. User**. MVC-Controller können mit Ihrer [User](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.user) -Eigenschaft auf den aktuellen authentifizierten Benutzer zugreifen.
* **HttpContext. User**. Komponenten mit Zugriff auf die aktuelle `HttpContext` (z. b. Middleware) können die `ClaimsPrincipal` des aktuellen Benutzers von " [HttpContext. User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user)" abrufen.
* **Übergeben vom**Aufrufer. Bibliotheken, die keinen Zugriff auf die aktuelle `HttpContext` haben, werden häufig von Controllern oder middlewarekomponenten aufgerufen, und die Identität des aktuellen Benutzers kann als Argument übermittelt werden.
* **Ihttpcontextaccessor**. Das Projekt, das zu ASP.net Core migriert wird, ist möglicherweise zu groß, um die Identität des aktuellen Benutzers an alle erforderlichen Orte zu übergeben. In solchen Fällen kann [ihttpcontextaccessor](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor) als Problem Umgehung verwendet werden. `IHttpContextAccessor` kann auf den aktuellen `HttpContext` zugreifen (sofern vorhanden). Wenn di verwendet wird, finden Sie weitere Informationen unter <xref:fundamentals/httpcontext>. Eine kurzfristige Lösung, um die Identität des aktuellen Benutzers im Code zu erhalten, der noch nicht aktualisiert wurde, um mit der ASP.net Core di-gesteuerten Architektur zu arbeiten, wäre Folgendes:

  * Stellen Sie `IHttpContextAccessor` im di-Container durch Aufrufen von [addhttpcontextaccessor](https://github.com/aspnet/Hosting/issues/793) in `Startup.ConfigureServices`bereit.
  * Eine Instanz von `IHttpContextAccessor` während des Starts erhalten und in einer statischen Variablen speichern. Die-Instanz wird für Code verfügbar gemacht, der zuvor den aktuellen Benutzer aus einer statischen Eigenschaft abgerufen hat.
  * Rufen Sie die `ClaimsPrincipal` des aktuellen Benutzers mithilfe `HttpContextAccessor.HttpContext?.User`ab. Wenn dieser Code außerhalb des Kontexts einer HTTP-Anforderung verwendet wird, ist der `HttpContext` NULL.

Die letzte Option, die eine `IHttpContextAccessor` Instanz verwendet, die in einer statischen Variablen gespeichert ist, entspricht ASP.net Core Prinzipien (bevorzugt injizierte Abhängigkeiten von statischen Abhängigkeiten). Planen Sie das Abrufen von `IHttpContextAccessor` Instanzen stattdessen aus der Abhängigkeitsinjektion. Ein statisches Hilfsprogramm kann jedoch nützlich sein, wenn Sie große vorhandene ASP.net-apps migrieren, die zuvor `ClaimsPrincipal.Current`verwendet haben.
