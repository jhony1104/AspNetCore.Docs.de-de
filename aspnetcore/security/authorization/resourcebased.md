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
# <a name="resource-based-authorization-in-aspnet-core"></a><span data-ttu-id="4950a-103">Ressourcenbasierte Autorisierung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4950a-103">Resource-based authorization in ASP.NET Core</span></span>

<span data-ttu-id="4950a-104">Autorisierungsstrategie für die hängt von der Ressource, auf die zugegriffen wird.</span><span class="sxs-lookup"><span data-stu-id="4950a-104">Authorization strategy depends upon the resource being accessed.</span></span> <span data-ttu-id="4950a-105">Erwägen Sie ein Dokument, das eine Author-Eigenschaft hat.</span><span class="sxs-lookup"><span data-stu-id="4950a-105">Consider a document that has an author property.</span></span> <span data-ttu-id="4950a-106">Nur der Autor kann das Dokument zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="4950a-106">Only the author is allowed to update the document.</span></span> <span data-ttu-id="4950a-107">Daher muss das Dokument aus dem Datenspeicher abgerufen werden, vor der Auswertung der Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="4950a-107">Consequently, the document must be retrieved from the data store before authorization evaluation can occur.</span></span>

<span data-ttu-id="4950a-108">Attribut-Auswertung erfolgt, bevor Sie die Datenbindung und Ausführung der Seitenhandler für die oder Aktion, die das Dokument geladen wird.</span><span class="sxs-lookup"><span data-stu-id="4950a-108">Attribute evaluation occurs before data binding and before execution of the page handler or action that loads the document.</span></span> <span data-ttu-id="4950a-109">Aus diesen Gründen deklarative Autorisierung mit einem `[Authorize]` Attribut nicht ausreichen.</span><span class="sxs-lookup"><span data-stu-id="4950a-109">For these reasons, declarative authorization with an `[Authorize]` attribute doesn't suffice.</span></span> <span data-ttu-id="4950a-110">Stattdessen können Sie eine benutzerdefinierte Autorisierung-Methode aufrufen&mdash;einen Stil, bekannt als *imperative Autorisierung*.</span><span class="sxs-lookup"><span data-stu-id="4950a-110">Instead, you can invoke a custom authorization method&mdash;a style known as *imperative authorization*.</span></span>

<span data-ttu-id="4950a-111">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="4950a-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="4950a-112">[Erstellen einer ASP.NET Core-app mit Benutzerdaten, die durch Autorisierung geschützt](xref:security/authorization/secure-data) enthält eine Beispiel-app, die ressourcenbasierte Autorisierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="4950a-112">[Create an ASP.NET Core app with user data protected by authorization](xref:security/authorization/secure-data) contains a sample app that uses resource-based authorization.</span></span>

## <a name="use-imperative-authorization"></a><span data-ttu-id="4950a-113">Verwenden Sie imperative Autorisierung</span><span class="sxs-lookup"><span data-stu-id="4950a-113">Use imperative authorization</span></span>

<span data-ttu-id="4950a-114">Autorisierung wird als implementiert eine [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) service und registriert ist, in der Sammlung von Diensten in der `Startup` Klasse.</span><span class="sxs-lookup"><span data-stu-id="4950a-114">Authorization is implemented as an [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) service and is registered in the service collection within the `Startup` class.</span></span> <span data-ttu-id="4950a-115">Der Dienst über zur Verfügung gestellt wird [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) Seitenhandler oder Aktionen.</span><span class="sxs-lookup"><span data-stu-id="4950a-115">The service is made available via [dependency injection](xref:fundamentals/dependency-injection) to page handlers or actions.</span></span>

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Controllers/DocumentController.cs?name=snippet_IAuthServiceDI&highlight=6)]

<span data-ttu-id="4950a-116">`IAuthorizationService` verfügt über zwei `AuthorizeAsync` Überladungen der Methode: ein akzeptieren der Ressource und den Richtliniennamen und die andere akzeptiert die Ressource und eine Liste der Anforderungen zum Auswerten.</span><span class="sxs-lookup"><span data-stu-id="4950a-116">`IAuthorizationService` has two `AuthorizeAsync` method overloads: one accepting the resource and the policy name and the other accepting the resource and a list of requirements to evaluate.</span></span>

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

<span data-ttu-id="4950a-117">Im folgenden Beispiel wird die Ressource, die gesichert werden geladen, in ein benutzerdefiniertes `Document` Objekt.</span><span class="sxs-lookup"><span data-stu-id="4950a-117">In the following example, the resource to be secured is loaded into a custom `Document` object.</span></span> <span data-ttu-id="4950a-118">Ein `AuthorizeAsync` Überladung wird aufgerufen, um zu bestimmen, ob der aktuelle Benutzer zulässig ist, um das angegebene Dokument zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="4950a-118">An `AuthorizeAsync` overload is invoked to determine whether the current user is allowed to edit the provided document.</span></span> <span data-ttu-id="4950a-119">Die Entscheidung wird eine benutzerdefinierte Autorisierungsrichtlinie von "EditPolicy" umgerechnet.</span><span class="sxs-lookup"><span data-stu-id="4950a-119">A custom "EditPolicy" authorization policy is factored into the decision.</span></span> <span data-ttu-id="4950a-120">Finden Sie unter [benutzerdefinierte richtlinienbasierte Autorisierung](xref:security/authorization/policies) Weitere Informationen zum Erstellen von Autorisierungsrichtlinien.</span><span class="sxs-lookup"><span data-stu-id="4950a-120">See [Custom policy-based authorization](xref:security/authorization/policies) for more on creating authorization policies.</span></span>

> [!NOTE]
> <span data-ttu-id="4950a-121">Der folgende Code-Beispielen wird davon ausgegangen, Authentifizierung ausgeführt wurde und legen die `User` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="4950a-121">The following code samples assume authentication has run and set the `User` property.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Pages/Document/Edit.cshtml.cs?name=snippet_DocumentEditHandler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentEditAction)]

::: moniker-end

## <a name="write-a-resource-based-handler"></a><span data-ttu-id="4950a-122">Schreiben Sie einen Handler ressourcenbasierte</span><span class="sxs-lookup"><span data-stu-id="4950a-122">Write a resource-based handler</span></span>

<span data-ttu-id="4950a-123">Schreiben einen Handler für die ressourcenbasierte Autorisierung nicht viel anders als ist [einen Handler für die einfache Anforderungen schreiben](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler).</span><span class="sxs-lookup"><span data-stu-id="4950a-123">Writing a handler for resource-based authorization isn't much different than [writing a plain requirements handler](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler).</span></span> <span data-ttu-id="4950a-124">Erstellen Sie eine benutzerdefinierte Anforderung-Klasse, und implementieren Sie eine Anforderung Handler-Klasse.</span><span class="sxs-lookup"><span data-stu-id="4950a-124">Create a custom requirement class, and implement a requirement handler class.</span></span> <span data-ttu-id="4950a-125">Weitere Informationen zum Erstellen einer Anforderungsklasse finden Sie unter [Anforderungen](xref:security/authorization/policies#requirements).</span><span class="sxs-lookup"><span data-stu-id="4950a-125">For more information on creating a requirement class, see [Requirements](xref:security/authorization/policies#requirements).</span></span>

<span data-ttu-id="4950a-126">Die Handlerklasse gibt die Anforderung und den Ressourcentyp an.</span><span class="sxs-lookup"><span data-stu-id="4950a-126">The handler class specifies both the requirement and resource type.</span></span> <span data-ttu-id="4950a-127">Z. B. einen Ereignishandler mit einem `SameAuthorRequirement` und `Document` Ressource folgt:</span><span class="sxs-lookup"><span data-stu-id="4950a-127">For example, a handler utilizing a `SameAuthorRequirement` and a `Document` resource follows:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

<span data-ttu-id="4950a-128">Im obigen Beispiel stellen Sie sich vor, die `SameAuthorRequirement` ist ein Sonderfall, der eine weitere generische `SpecificAuthorRequirement` Klasse.</span><span class="sxs-lookup"><span data-stu-id="4950a-128">In the preceding example, imagine that `SameAuthorRequirement` is a special case of a more generic `SpecificAuthorRequirement` class.</span></span> <span data-ttu-id="4950a-129">Die `SpecificAuthorRequirement` -Klasse (nicht gezeigt) enthält eine `Name` Eigenschaft, die den Namen des Autors.</span><span class="sxs-lookup"><span data-stu-id="4950a-129">The `SpecificAuthorRequirement` class (not shown) contains a `Name` property representing the name of the author.</span></span> <span data-ttu-id="4950a-130">Die `Name` Eigenschaft konnte für den aktuellen Benutzer festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="4950a-130">The `Name` property could be set to the current user.</span></span>

<span data-ttu-id="4950a-131">Registrieren Sie die Anforderungen und die Ereignishandler in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="4950a-131">Register the requirement and handler in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]

### <a name="operational-requirements"></a><span data-ttu-id="4950a-132">Betriebsanforderungen</span><span class="sxs-lookup"><span data-stu-id="4950a-132">Operational requirements</span></span>

<span data-ttu-id="4950a-133">Wenn Sie Entscheidungen basierend auf den Ergebnissen des CRUD (Create, Read, Update, Delete)-Operationen vornehmen, verwenden Sie die [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) Helper-Klasse.</span><span class="sxs-lookup"><span data-stu-id="4950a-133">If you're making decisions based on the outcomes of CRUD (Create, Read, Update, Delete) operations, use the [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) helper class.</span></span> <span data-ttu-id="4950a-134">Diese Klasse ermöglicht Ihnen, einen einzelnen Handler, anstatt eine einzelne Klasse für jeden Vorgangstyp zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="4950a-134">This class enables you to write a single handler instead of an individual class for each operation type.</span></span> <span data-ttu-id="4950a-135">Geben Sie einige Vorgangsnamen, um es zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="4950a-135">To use it, provide some operation names:</span></span>

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_OperationsClass)]

<span data-ttu-id="4950a-136">Der Handler wird wie folgt implementiert, mit einem `OperationAuthorizationRequirement` Anforderung und eine `Document` Ressource:</span><span class="sxs-lookup"><span data-stu-id="4950a-136">The handler is implemented as follows, using an `OperationAuthorizationRequirement` requirement and a `Document` resource:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

<span data-ttu-id="4950a-137">Der vorherige Handler überprüft den Vorgang mit der Ressource, die Identität des Benutzers und der Anforderung des `Name` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="4950a-137">The preceding handler validates the operation using the resource, the user's identity, and the requirement's `Name` property.</span></span>

<span data-ttu-id="4950a-138">Um einen Handler für die operative Ressource aufzurufen, geben Sie den Vorgang aufrufen `AuthorizeAsync` im Seitenhandler für die oder Aktion.</span><span class="sxs-lookup"><span data-stu-id="4950a-138">To call an operational resource handler, specify the operation when invoking `AuthorizeAsync` in your page handler or action.</span></span> <span data-ttu-id="4950a-139">Im folgenden Beispiel wird bestimmt, ob der authentifizierte Benutzer zum Anzeigen des angegebenen Dokuments zulässig ist.</span><span class="sxs-lookup"><span data-stu-id="4950a-139">The following example determines whether the authenticated user is permitted to view the provided document.</span></span>

> [!NOTE]
> <span data-ttu-id="4950a-140">Der folgende Code-Beispielen wird davon ausgegangen, Authentifizierung ausgeführt wurde und legen die `User` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="4950a-140">The following code samples assume authentication has run and set the `User` property.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Pages/Document/View.cshtml.cs?name=snippet_DocumentViewHandler&highlight=10-11)]

<span data-ttu-id="4950a-141">Bei einer erfolgreichen Autorisierung wird die Seite zum Anzeigen des Dokuments zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="4950a-141">If authorization succeeds, the page for viewing the document is returned.</span></span> <span data-ttu-id="4950a-142">Wenn die Autorisierung fehlschlägt, aber der Benutzer wird authentifiziert, Zurückgeben von `ForbidResult` informiert alle authentifizierungsmiddleware, die Fehler bei der Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="4950a-142">If authorization fails but the user is authenticated, returning `ForbidResult` informs any authentication middleware that authorization failed.</span></span> <span data-ttu-id="4950a-143">Ein `ChallengeResult` wird zurückgegeben, wenn die Authentifizierung ausgeführt werden muss.</span><span class="sxs-lookup"><span data-stu-id="4950a-143">A `ChallengeResult` is returned when authentication must be performed.</span></span> <span data-ttu-id="4950a-144">Für die interaktive Browser-Clients kann es für den Benutzer zu einer Anmeldeseite umleiten geeignet sein.</span><span class="sxs-lookup"><span data-stu-id="4950a-144">For interactive browser clients, it may be appropriate to redirect the user to a login page.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentViewAction&highlight=11-12)]

<span data-ttu-id="4950a-145">Die Ansicht für das Dokument wird zurückgegeben, bei einer erfolgreichen Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="4950a-145">If authorization succeeds, the view for the document is returned.</span></span> <span data-ttu-id="4950a-146">Wenn die Autorisierung fehlschlägt, Rückgabe `ChallengeResult` informiert Sie Middleware für die Authentifizierung, dass Fehler bei der Autorisierung, und die Middleware kann die entsprechende Antwort.</span><span class="sxs-lookup"><span data-stu-id="4950a-146">If authorization fails, returning `ChallengeResult` informs any authentication middleware that authorization failed, and the middleware can take the appropriate response.</span></span> <span data-ttu-id="4950a-147">Eine entsprechende Antwort konnte einen Statuscode 401 oder 403 zurück.</span><span class="sxs-lookup"><span data-stu-id="4950a-147">An appropriate response could be returning a 401 or 403 status code.</span></span> <span data-ttu-id="4950a-148">Für die interaktive Browser-Clients kann dies bedeuten, Weiterleiten des Benutzers zu einer Anmeldeseite um.</span><span class="sxs-lookup"><span data-stu-id="4950a-148">For interactive browser clients, it could mean redirecting the user to a login page.</span></span>

::: moniker-end
