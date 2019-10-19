---
title: Ressourcenbasierte Autorisierung in ASP.net Core
author: scottaddie
description: Erfahren Sie, wie Sie die ressourcenbasierte Autorisierung in einer ASP.net Core-App implementieren, wenn ein Autorisierungs Attribut nicht ausreicht.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/15/2018
uid: security/authorization/resourcebased
ms.openlocfilehash: 835592521c714e270595e1448ae6e0aed1707b77
ms.sourcegitcommit: a166291c6708f5949c417874108332856b53b6a9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2019
ms.locfileid: "72590002"
---
# <a name="resource-based-authorization-in-aspnet-core"></a><span data-ttu-id="65fe2-103">Ressourcenbasierte Autorisierung in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="65fe2-103">Resource-based authorization in ASP.NET Core</span></span>

<span data-ttu-id="65fe2-104">Autorisierungs Strategie hängt von der Ressource ab, auf die zugegriffen wird.</span><span class="sxs-lookup"><span data-stu-id="65fe2-104">Authorization strategy depends upon the resource being accessed.</span></span> <span data-ttu-id="65fe2-105">Stellen Sie sich ein Dokument mit der Eigenschaft Autor vor.</span><span class="sxs-lookup"><span data-stu-id="65fe2-105">Consider a document that has an author property.</span></span> <span data-ttu-id="65fe2-106">Nur der Autor darf das Dokument aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="65fe2-106">Only the author is allowed to update the document.</span></span> <span data-ttu-id="65fe2-107">Folglich muss das Dokument aus dem Datenspeicher abgerufen werden, bevor die Autorisierungs Auswertung erfolgen kann.</span><span class="sxs-lookup"><span data-stu-id="65fe2-107">Consequently, the document must be retrieved from the data store before authorization evaluation can occur.</span></span>

<span data-ttu-id="65fe2-108">Die Attribut Auswertung erfolgt vor der Datenbindung und vor der Ausführung des Seiten Handlers oder der Aktion, die das Dokument lädt.</span><span class="sxs-lookup"><span data-stu-id="65fe2-108">Attribute evaluation occurs before data binding and before execution of the page handler or action that loads the document.</span></span> <span data-ttu-id="65fe2-109">Aus diesen Gründen genügt die deklarative Autorisierung mit einem `[Authorize]`-Attribut nicht.</span><span class="sxs-lookup"><span data-stu-id="65fe2-109">For these reasons, declarative authorization with an `[Authorize]` attribute doesn't suffice.</span></span> <span data-ttu-id="65fe2-110">Stattdessen können Sie eine benutzerdefinierte Autorisierungs Methode &mdash;a Stil aufrufen, der als *imperative Autorisierung*bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="65fe2-110">Instead, you can invoke a custom authorization method&mdash;a style known as *imperative authorization*.</span></span>

<span data-ttu-id="65fe2-111">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="65fe2-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="65fe2-112">[Erstellen einer ASP.net Core-App mit von der Autorisierung geschützten Benutzerdaten](xref:security/authorization/secure-data) enthält eine Beispiel-APP, die die ressourcenbasierte Autorisierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="65fe2-112">[Create an ASP.NET Core app with user data protected by authorization](xref:security/authorization/secure-data) contains a sample app that uses resource-based authorization.</span></span>

## <a name="use-imperative-authorization"></a><span data-ttu-id="65fe2-113">Imperative Autorisierung verwenden</span><span class="sxs-lookup"><span data-stu-id="65fe2-113">Use imperative authorization</span></span>

<span data-ttu-id="65fe2-114">Die Autorisierung wird als [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) -Dienst implementiert und in der Service Collection innerhalb der `Startup`-Klasse registriert.</span><span class="sxs-lookup"><span data-stu-id="65fe2-114">Authorization is implemented as an [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) service and is registered in the service collection within the `Startup` class.</span></span> <span data-ttu-id="65fe2-115">Der Dienst wird über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) in Seiten Handler oder Aktionen bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="65fe2-115">The service is made available via [dependency injection](xref:fundamentals/dependency-injection) to page handlers or actions.</span></span>

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Controllers/DocumentController.cs?name=snippet_IAuthServiceDI&highlight=6)]

<span data-ttu-id="65fe2-116">`IAuthorizationService` hat zwei `AuthorizeAsync` Methoden Überladungen: einen, der die Ressource annimmt, und den Richtlinien Namen und den anderen, der die Ressource annimmt, und eine Liste der auszuwertenden Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="65fe2-116">`IAuthorizationService` has two `AuthorizeAsync` method overloads: one accepting the resource and the policy name and the other accepting the resource and a list of requirements to evaluate.</span></span>

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

<span data-ttu-id="65fe2-117">Im folgenden Beispiel wird die zu sichernden Ressource in ein benutzerdefiniertes `Document` Objekt geladen.</span><span class="sxs-lookup"><span data-stu-id="65fe2-117">In the following example, the resource to be secured is loaded into a custom `Document` object.</span></span> <span data-ttu-id="65fe2-118">Eine `AuthorizeAsync` Überladung wird aufgerufen, um zu bestimmen, ob der aktuelle Benutzer das angegebene Dokument bearbeiten darf.</span><span class="sxs-lookup"><span data-stu-id="65fe2-118">An `AuthorizeAsync` overload is invoked to determine whether the current user is allowed to edit the provided document.</span></span> <span data-ttu-id="65fe2-119">Eine benutzerdefinierte editpolicy-Autorisierungs Richtlinie wird in die Entscheidung einbezogen.</span><span class="sxs-lookup"><span data-stu-id="65fe2-119">A custom "EditPolicy" authorization policy is factored into the decision.</span></span> <span data-ttu-id="65fe2-120">Weitere Informationen zum Erstellen von Autorisierungs Richtlinien finden Sie unter [benutzerdefinierte Richtlinien basierte Autorisierung](xref:security/authorization/policies) .</span><span class="sxs-lookup"><span data-stu-id="65fe2-120">See [Custom policy-based authorization](xref:security/authorization/policies) for more on creating authorization policies.</span></span>

> [!NOTE]
> <span data-ttu-id="65fe2-121">In den folgenden Codebeispielen wird vorausgesetzt, dass die-Authentifizierung ausgeführt wurde und die `User`-Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="65fe2-121">The following code samples assume authentication has run and set the `User` property.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Pages/Document/Edit.cshtml.cs?name=snippet_DocumentEditHandler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentEditAction)]

::: moniker-end

## <a name="write-a-resource-based-handler"></a><span data-ttu-id="65fe2-122">Schreiben eines ressourcenbasierten Handlers</span><span class="sxs-lookup"><span data-stu-id="65fe2-122">Write a resource-based handler</span></span>

<span data-ttu-id="65fe2-123">Das Schreiben eines Handlers für die ressourcenbasierte Autorisierung unterscheidet sich nicht wesentlich vom [Schreiben eines einfachen Anforderungs Handlers](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler).</span><span class="sxs-lookup"><span data-stu-id="65fe2-123">Writing a handler for resource-based authorization isn't much different than [writing a plain requirements handler](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler).</span></span> <span data-ttu-id="65fe2-124">Erstellen Sie eine benutzerdefinierte Anforderungs Klasse, und implementieren Sie eine Anforderungs Handler-Klasse.</span><span class="sxs-lookup"><span data-stu-id="65fe2-124">Create a custom requirement class, and implement a requirement handler class.</span></span> <span data-ttu-id="65fe2-125">Weitere Informationen zum Erstellen einer Anforderungs Klasse finden Sie unter [Anforderungen](xref:security/authorization/policies#requirements).</span><span class="sxs-lookup"><span data-stu-id="65fe2-125">For more information on creating a requirement class, see [Requirements](xref:security/authorization/policies#requirements).</span></span>

<span data-ttu-id="65fe2-126">Die Handlerklasse gibt sowohl die Anforderung als auch den Ressourcentyp an.</span><span class="sxs-lookup"><span data-stu-id="65fe2-126">The handler class specifies both the requirement and resource type.</span></span> <span data-ttu-id="65fe2-127">Ein Handler, der eine `SameAuthorRequirement` und eine `Document` Ressource nutzt, folgt beispielsweise:</span><span class="sxs-lookup"><span data-stu-id="65fe2-127">For example, a handler utilizing a `SameAuthorRequirement` and a `Document` resource follows:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

<span data-ttu-id="65fe2-128">Stellen Sie im vorhergehenden Beispiel fest, dass `SameAuthorRequirement` ein Sonderfall einer allgemeineren `SpecificAuthorRequirement` Klasse ist.</span><span class="sxs-lookup"><span data-stu-id="65fe2-128">In the preceding example, imagine that `SameAuthorRequirement` is a special case of a more generic `SpecificAuthorRequirement` class.</span></span> <span data-ttu-id="65fe2-129">Die `SpecificAuthorRequirement`-Klasse (nicht angezeigt) enthält eine `Name` Eigenschaft, die den Namen des Autors darstellt.</span><span class="sxs-lookup"><span data-stu-id="65fe2-129">The `SpecificAuthorRequirement` class (not shown) contains a `Name` property representing the name of the author.</span></span> <span data-ttu-id="65fe2-130">Die `Name`-Eigenschaft kann auf den aktuellen Benutzer festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="65fe2-130">The `Name` property could be set to the current user.</span></span>

<span data-ttu-id="65fe2-131">Registrieren Sie die Anforderung und den Handler in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="65fe2-131">Register the requirement and handler in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]

### <a name="operational-requirements"></a><span data-ttu-id="65fe2-132">Betriebliche Anforderungen</span><span class="sxs-lookup"><span data-stu-id="65fe2-132">Operational requirements</span></span>

<span data-ttu-id="65fe2-133">Wenn Sie Entscheidungen auf der Grundlage der Ergebnisse von CRUD-Vorgängen (erstellen, lesen, aktualisieren, löschen) treffen, verwenden Sie die Hilfsklasse [operationauthorizationrequirements](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) .</span><span class="sxs-lookup"><span data-stu-id="65fe2-133">If you're making decisions based on the outcomes of CRUD (Create, Read, Update, Delete) operations, use the [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) helper class.</span></span> <span data-ttu-id="65fe2-134">Diese Klasse ermöglicht das Schreiben eines einzelnen Handlers anstelle einer einzelnen Klasse für jeden Vorgangstyp.</span><span class="sxs-lookup"><span data-stu-id="65fe2-134">This class enables you to write a single handler instead of an individual class for each operation type.</span></span> <span data-ttu-id="65fe2-135">Um es zu verwenden, geben Sie einige Vorgangs Namen an:</span><span class="sxs-lookup"><span data-stu-id="65fe2-135">To use it, provide some operation names:</span></span>

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_OperationsClass)]

<span data-ttu-id="65fe2-136">Der Handler wird wie folgt implementiert, wobei eine `OperationAuthorizationRequirement` Anforderung und eine `Document` Ressource verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="65fe2-136">The handler is implemented as follows, using an `OperationAuthorizationRequirement` requirement and a `Document` resource:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

<span data-ttu-id="65fe2-137">Der vorherige Handler überprüft den Vorgang mithilfe der Ressource, der Identität des Benutzers und der `Name`-Eigenschaft der Anforderung.</span><span class="sxs-lookup"><span data-stu-id="65fe2-137">The preceding handler validates the operation using the resource, the user's identity, and the requirement's `Name` property.</span></span>

## <a name="challenge-and-forbid-with-an-operational-resource-handler"></a><span data-ttu-id="65fe2-138">Herausforderungen und verbieten mit einem funktionstüchtigen Ressourcen Handler</span><span class="sxs-lookup"><span data-stu-id="65fe2-138">Challenge and forbid with an operational resource handler</span></span>

<span data-ttu-id="65fe2-139">In diesem Abschnitt wird gezeigt, wie die Ergebnisse "Challenge" und "verboten" verarbeitet werden und wie sich die Herausforderung und die unter</span><span class="sxs-lookup"><span data-stu-id="65fe2-139">This section shows how the challenge and forbid action results are processed and how challenge and forbid differ.</span></span>

<span data-ttu-id="65fe2-140">Um einen Ressourcen Handler für operative Ressourcen aufzurufen, geben Sie den Vorgang an, wenn Sie `AuthorizeAsync` in Ihrem Seiten Handler oder einer Aktion aufrufen.</span><span class="sxs-lookup"><span data-stu-id="65fe2-140">To call an operational resource handler, specify the operation when invoking `AuthorizeAsync` in your page handler or action.</span></span> <span data-ttu-id="65fe2-141">Im folgenden Beispiel wird ermittelt, ob der authentifizierte Benutzer das angegebene Dokument anzeigen darf.</span><span class="sxs-lookup"><span data-stu-id="65fe2-141">The following example determines whether the authenticated user is permitted to view the provided document.</span></span>

> [!NOTE]
> <span data-ttu-id="65fe2-142">In den folgenden Codebeispielen wird vorausgesetzt, dass die-Authentifizierung ausgeführt wurde und die `User`-Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="65fe2-142">The following code samples assume authentication has run and set the `User` property.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Pages/Document/View.cshtml.cs?name=snippet_DocumentViewHandler&highlight=10-11)]

<span data-ttu-id="65fe2-143">Wenn die Autorisierung erfolgreich ist, wird die Seite zum Anzeigen des Dokuments zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="65fe2-143">If authorization succeeds, the page for viewing the document is returned.</span></span> <span data-ttu-id="65fe2-144">Wenn die Autorisierung fehlschlägt, der Benutzer aber authentifiziert ist, wird bei der Rückgabe `ForbidResult` jede Authentifizierungs Middleware informiert, dass die Autorisierung</span><span class="sxs-lookup"><span data-stu-id="65fe2-144">If authorization fails but the user is authenticated, returning `ForbidResult` informs any authentication middleware that authorization failed.</span></span> <span data-ttu-id="65fe2-145">Eine `ChallengeResult` wird zurückgegeben, wenn die Authentifizierung durchgeführt werden muss.</span><span class="sxs-lookup"><span data-stu-id="65fe2-145">A `ChallengeResult` is returned when authentication must be performed.</span></span> <span data-ttu-id="65fe2-146">Bei interaktiven Browser Clients kann es sinnvoll sein, den Benutzer an eine Anmeldeseite umzuleiten.</span><span class="sxs-lookup"><span data-stu-id="65fe2-146">For interactive browser clients, it may be appropriate to redirect the user to a login page.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentViewAction&highlight=11-12)]

<span data-ttu-id="65fe2-147">Wenn die Autorisierung erfolgreich ist, wird die Sicht für das Dokument zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="65fe2-147">If authorization succeeds, the view for the document is returned.</span></span> <span data-ttu-id="65fe2-148">Wenn die Autorisierung fehlschlägt, werden bei der Rückgabe `ChallengeResult` alle Authentifizierungs Middleware mitgeteilt, dass die Autorisierung fehlgeschlagen ist, und die Middleware kann die entsprechende Antwort</span><span class="sxs-lookup"><span data-stu-id="65fe2-148">If authorization fails, returning `ChallengeResult` informs any authentication middleware that authorization failed, and the middleware can take the appropriate response.</span></span> <span data-ttu-id="65fe2-149">Eine entsprechende Antwort könnte einen 401-oder 403-Statuscode zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="65fe2-149">An appropriate response could be returning a 401 or 403 status code.</span></span> <span data-ttu-id="65fe2-150">Bei interaktiven Browser Clients könnte dies bedeuten, dass der Benutzer an eine Anmeldeseite umgeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="65fe2-150">For interactive browser clients, it could mean redirecting the user to a login page.</span></span>

::: moniker-end
