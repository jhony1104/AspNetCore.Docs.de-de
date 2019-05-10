---
title: Ressourcenbasierte Autorisierung in ASP.NET Core
author: scottaddie
description: Erfahren Sie, wie Sie die ressourcenbasierte Autorisierung in einer ASP.NET Core-app zu implementieren, wenn ein Authorize-Attributs ausreichen wird nicht.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/15/2018
uid: security/authorization/resourcebased
ms.openlocfilehash: afc152ea677cab42d57bd642b4821159f125117e
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64891737"
---
# <a name="resource-based-authorization-in-aspnet-core"></a>Ressourcenbasierte Autorisierung in ASP.NET Core

Autorisierungsstrategie für die hängt von der Ressource, auf die zugegriffen wird. Erwägen Sie ein Dokument, das eine Author-Eigenschaft hat. Nur der Autor kann das Dokument zu aktualisieren. Daher muss das Dokument aus dem Datenspeicher abgerufen werden, vor der Auswertung der Autorisierung.

Attribut-Auswertung erfolgt, bevor Sie die Datenbindung und Ausführung der Seitenhandler für die oder Aktion, die das Dokument geladen wird. Aus diesen Gründen deklarative Autorisierung mit einem `[Authorize]` Attribut nicht ausreichen. Stattdessen können Sie eine benutzerdefinierte Autorisierung-Methode aufrufen&mdash;einen Stil, bekannt als *imperative Autorisierung*.

[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).

[Erstellen einer ASP.NET Core-app mit Benutzerdaten, die durch Autorisierung geschützt](xref:security/authorization/secure-data) enthält eine Beispiel-app, die ressourcenbasierte Autorisierung verwendet.

## <a name="use-imperative-authorization"></a>Verwenden Sie imperative Autorisierung

Autorisierung wird als implementiert eine [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) service und registriert ist, in der Sammlung von Diensten in der `Startup` Klasse. Der Dienst über zur Verfügung gestellt wird [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) Seitenhandler oder Aktionen.

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Controllers/DocumentController.cs?name=snippet_IAuthServiceDI&highlight=6)]

`IAuthorizationService` verfügt über zwei `AuthorizeAsync` Überladungen der Methode: ein akzeptieren der Ressource und den Richtliniennamen und die andere akzeptiert die Ressource und eine Liste der Anforderungen zum Auswerten.

::: moniker range=">= aspnetcore-2.0"

```csharp
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

```csharp
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

<a name="security-authorization-resource-based-imperative"></a>

Im folgenden Beispiel wird die Ressource, die gesichert werden geladen, in ein benutzerdefiniertes `Document` Objekt. Ein `AuthorizeAsync` Überladung wird aufgerufen, um zu bestimmen, ob der aktuelle Benutzer zulässig ist, um das angegebene Dokument zu bearbeiten. Die Entscheidung wird eine benutzerdefinierte Autorisierungsrichtlinie von "EditPolicy" umgerechnet. Finden Sie unter [benutzerdefinierte richtlinienbasierte Autorisierung](xref:security/authorization/policies) Weitere Informationen zum Erstellen von Autorisierungsrichtlinien.

> [!NOTE]
> Der folgende Code-Beispielen wird davon ausgegangen, Authentifizierung ausgeführt wurde und legen die `User` Eigenschaft.

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Pages/Document/Edit.cshtml.cs?name=snippet_DocumentEditHandler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentEditAction)]

::: moniker-end

## <a name="write-a-resource-based-handler"></a>Schreiben Sie einen Handler ressourcenbasierte

Schreiben einen Handler für die ressourcenbasierte Autorisierung nicht viel anders als ist [einen Handler für die einfache Anforderungen schreiben](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler). Erstellen Sie eine benutzerdefinierte Anforderung-Klasse, und implementieren Sie eine Anforderung Handler-Klasse. Weitere Informationen zum Erstellen einer Anforderungsklasse finden Sie unter [Anforderungen](xref:security/authorization/policies#requirements).

Die Handlerklasse gibt die Anforderung und den Ressourcentyp an. Z. B. einen Ereignishandler mit einem `SameAuthorRequirement` und `Document` Ressource folgt:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

Im obigen Beispiel stellen Sie sich vor, die `SameAuthorRequirement` ist ein Sonderfall, der eine weitere generische `SpecificAuthorRequirement` Klasse. Die `SpecificAuthorRequirement` -Klasse (nicht gezeigt) enthält eine `Name` Eigenschaft, die den Namen des Autors. Die `Name` Eigenschaft konnte für den aktuellen Benutzer festgelegt werden.

Registrieren Sie die Anforderungen und die Ereignishandler in `Startup.ConfigureServices`:

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]

### <a name="operational-requirements"></a>Betriebsanforderungen

Wenn Sie Entscheidungen basierend auf den Ergebnissen des CRUD (Create, Read, Update, Delete)-Operationen vornehmen, verwenden Sie die [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) Helper-Klasse. Diese Klasse ermöglicht Ihnen, einen einzelnen Handler, anstatt eine einzelne Klasse für jeden Vorgangstyp zu schreiben. Geben Sie einige Vorgangsnamen, um es zu verwenden:

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_OperationsClass)]

Der Handler wird wie folgt implementiert, mit einem `OperationAuthorizationRequirement` Anforderung und eine `Document` Ressource:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

Der vorherige Handler überprüft den Vorgang mit der Ressource, die Identität des Benutzers und der Anforderung des `Name` Eigenschaft.

Um einen Handler für die operative Ressource aufzurufen, geben Sie den Vorgang aufrufen `AuthorizeAsync` im Seitenhandler für die oder Aktion. Im folgenden Beispiel wird bestimmt, ob der authentifizierte Benutzer zum Anzeigen des angegebenen Dokuments zulässig ist.

> [!NOTE]
> Der folgende Code-Beispielen wird davon ausgegangen, Authentifizierung ausgeführt wurde und legen die `User` Eigenschaft.

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Pages/Document/View.cshtml.cs?name=snippet_DocumentViewHandler&highlight=10-11)]

Bei einer erfolgreichen Autorisierung wird die Seite zum Anzeigen des Dokuments zurückgegeben. Wenn die Autorisierung fehlschlägt, aber der Benutzer wird authentifiziert, Zurückgeben von `ForbidResult` informiert alle authentifizierungsmiddleware, die Fehler bei der Autorisierung. Ein `ChallengeResult` wird zurückgegeben, wenn die Authentifizierung ausgeführt werden muss. Für die interaktive Browser-Clients kann es für den Benutzer zu einer Anmeldeseite umleiten geeignet sein.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentViewAction&highlight=11-12)]

Die Ansicht für das Dokument wird zurückgegeben, bei einer erfolgreichen Autorisierung. Wenn die Autorisierung fehlschlägt, Rückgabe `ChallengeResult` informiert Sie Middleware für die Authentifizierung, dass Fehler bei der Autorisierung, und die Middleware kann die entsprechende Antwort. Eine entsprechende Antwort konnte einen Statuscode 401 oder 403 zurück. Für die interaktive Browser-Clients kann dies bedeuten, Weiterleiten des Benutzers zu einer Anmeldeseite um.

::: moniker-end
