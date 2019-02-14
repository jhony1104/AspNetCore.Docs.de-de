---
title: Richtlinienbasierte Autorisierung in ASP.NET Core
author: rick-anderson
description: Informationen Sie zum Erstellen und Verwenden von Authorization Policy-Handler für das Erzwingen von autorisierungsanforderungen in einer ASP.NET Core-app.
ms.author: riande
ms.custom: mvc
ms.date: 11/21/2017
uid: security/authorization/policies
ms.openlocfilehash: 937c73c26cd3935c5069d4735e754d1a567f41f4
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248107"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="f8831-103">Richtlinienbasierte Autorisierung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f8831-103">Policy-based authorization in ASP.NET Core</span></span>

<span data-ttu-id="f8831-104">Im Hintergrund [rollenbasierte Autorisierung](xref:security/authorization/roles) und [anspruchsbasierte Autorisierung](xref:security/authorization/claims) verwenden Sie eine Anforderung, einen Handler für die Anforderung und einer vorkonfigurierten Richtlinie.</span><span class="sxs-lookup"><span data-stu-id="f8831-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="f8831-105">Diese Bausteine unterstützen den Ausdruck der auswertungen der Autorisierung im Code.</span><span class="sxs-lookup"><span data-stu-id="f8831-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="f8831-106">Das Ergebnis ist eine umfangreichere, wieder verwendbare, testfähigen Autorisierung-Struktur.</span><span class="sxs-lookup"><span data-stu-id="f8831-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="f8831-107">Eine Autorisierungsrichtlinie besteht eine oder mehrere Anforderungen aus.</span><span class="sxs-lookup"><span data-stu-id="f8831-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="f8831-108">Er ist als Teil der autorisierungsdienstkonfiguration registriert, der `Startup.ConfigureServices` Methode:</span><span class="sxs-lookup"><span data-stu-id="f8831-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=40-41,50-55,63,72)]

<span data-ttu-id="f8831-109">Im vorherigen Beispiel wird eine Richtlinie für "AtLeast21" erstellt.</span><span class="sxs-lookup"><span data-stu-id="f8831-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="f8831-110">Es wurde eine einzelne Anforderung&mdash;mit einem Mindestalter, die als Parameter für die Anforderung bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="f8831-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

<span data-ttu-id="f8831-111">Richtlinien werden angewendet, mit der `[Authorize]` Attribut mit dem Richtliniennamen.</span><span class="sxs-lookup"><span data-stu-id="f8831-111">Policies are applied by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="f8831-112">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f8831-112">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="requirements"></a><span data-ttu-id="f8831-113">Anforderungen</span><span class="sxs-lookup"><span data-stu-id="f8831-113">Requirements</span></span>

<span data-ttu-id="f8831-114">Eine autorisierungsanforderung ist eine Auflistung von Datenparametern, die eine Richtlinie zum Auswerten des aktuelle Benutzerprinzipals verwenden können.</span><span class="sxs-lookup"><span data-stu-id="f8831-114">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="f8831-115">In unserer "AtLeast21"-Richtlinie die Anforderung ist ein einzelner Parameter&mdash;das Mindestalter.</span><span class="sxs-lookup"><span data-stu-id="f8831-115">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="f8831-116">Eine Anforderung implementiert [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), dies ist eine leere Markierungsschnittstelle.</span><span class="sxs-lookup"><span data-stu-id="f8831-116">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="f8831-117">Eine parametrisierte Mindestalter-Anforderung kann wie folgt implementiert werden:</span><span class="sxs-lookup"><span data-stu-id="f8831-117">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="f8831-118">Eine Autorisierungsrichtlinie mehrere autorisierungsanforderungen enthält, müssen alle Anforderungen in der Reihenfolge für die richtlinienauswertung erfolgreich übergeben.</span><span class="sxs-lookup"><span data-stu-id="f8831-118">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="f8831-119">Anders ausgedrückt, sind mehrere autorisierungsanforderungen, die eine einzelne Autorisierungsrichtlinie hinzugefügt auf behandelt eine **und** Basis.</span><span class="sxs-lookup"><span data-stu-id="f8831-119">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="f8831-120">Eine Anforderung benötigen nicht Daten oder Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="f8831-120">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="f8831-121">Authorization-Handler</span><span class="sxs-lookup"><span data-stu-id="f8831-121">Authorization handlers</span></span>

<span data-ttu-id="f8831-122">Ein autorisierungshandler ist verantwortlich für die Auswertung von Eigenschaften für die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="f8831-122">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="f8831-123">Der autorisierungshandler für die wertet die Anforderungen für ein bereitgestelltes [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) zu bestimmen, ob der Zugriff zulässig ist.</span><span class="sxs-lookup"><span data-stu-id="f8831-123">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="f8831-124">Eine Anforderung haben [mehrere Handler](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="f8831-124">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="f8831-125">Ein Handler kann erben [AuthorizationHandler\<TRequirement >](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), wobei `TRequirement` ist die Anforderung behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="f8831-125">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="f8831-126">Alternativ kann ein Handler implementieren [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) um mehr als eine Art von Anforderung zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="f8831-126">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="f8831-127">Verwenden Sie einen Handler für eine Anforderung</span><span class="sxs-lookup"><span data-stu-id="f8831-127">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="f8831-128">Im folgenden finden ein Beispiel für eine direkte Beziehung, in der ein Mindestalter-Handler eine einzelne Anforderung verwendet:</span><span class="sxs-lookup"><span data-stu-id="f8831-128">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="f8831-129">Der vorangehende Code wird bestimmt, ob der aktuelle Benutzer, der Dienstprinzipal verfügt ein Geburtsdatum Anspruch, der von einem bekannten und vertrauenswürdigen Aussteller ausgegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="f8831-129">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="f8831-130">Autorisierung kann nicht auftreten, wenn der Anspruch nicht vorhanden ist, wird in diesem Fall eine abgeschlossene Aufgabe zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="f8831-130">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="f8831-131">Wenn ein Anspruch vorhanden ist, wird das Alter des Benutzers berechnet.</span><span class="sxs-lookup"><span data-stu-id="f8831-131">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="f8831-132">Wenn der Benutzer das Mindestalter, definiert durch die Anforderung erfüllt, wird die Autorisierung erfolgreich angesehen.</span><span class="sxs-lookup"><span data-stu-id="f8831-132">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="f8831-133">Wenn eine Autorisierung erfolgreich ist, wird `context.Succeed` wird aufgerufen, mit der Anforderung erfüllt, als einzigen Parameter.</span><span class="sxs-lookup"><span data-stu-id="f8831-133">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="f8831-134">Verwenden Sie einen Handler für mehrere Anforderungen</span><span class="sxs-lookup"><span data-stu-id="f8831-134">Use a handler for multiple requirements</span></span>

<span data-ttu-id="f8831-135">Im folgenden finden ein Beispiel für eine 1: n Beziehung, in der ein Berechtigung-Handler drei verschiedene Typen von Anforderungen verarbeiten kann:</span><span class="sxs-lookup"><span data-stu-id="f8831-135">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="f8831-136">Der vorangehende Code durchläuft [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;eine Eigenschaft, die sowohl die Anforderungen nicht als erfolgreich gekennzeichnet.</span><span class="sxs-lookup"><span data-stu-id="f8831-136">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="f8831-137">Für eine `ReadPermission` Anforderung entweder "Besitzer" oder "Sponsor" aus, um die angeforderte Ressource zuzugreifen, muss der Benutzer sein.</span><span class="sxs-lookup"><span data-stu-id="f8831-137">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="f8831-138">Im Fall von einem `EditPermission` oder `DeletePermission` erforderlich, er oder sie muss einen Besitzer aus, um die angeforderte Ressource zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="f8831-138">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="f8831-139">Registrierung</span><span class="sxs-lookup"><span data-stu-id="f8831-139">Handler registration</span></span>

<span data-ttu-id="f8831-140">Handler werden in der dienstauflistung während der Konfiguration registriert.</span><span class="sxs-lookup"><span data-stu-id="f8831-140">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="f8831-141">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f8831-141">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=40-41,50-55,63-65,72)]

<span data-ttu-id="f8831-142">Jeder Handler wird der dienstauflistung hinzugefügt, durch den Aufruf `services.AddSingleton<IAuthorizationHandler, YourHandlerClass>();`.</span><span class="sxs-lookup"><span data-stu-id="f8831-142">Each handler is added to the services collection by invoking `services.AddSingleton<IAuthorizationHandler, YourHandlerClass>();`.</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="f8831-143">Was sollte es sich um ein Handler zurückgeben?</span><span class="sxs-lookup"><span data-stu-id="f8831-143">What should a handler return?</span></span>

<span data-ttu-id="f8831-144">Beachten Sie, dass die `Handle` -Methode in der die [Handler Beispiel](#security-authorization-handler-example) keinen Wert zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="f8831-144">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="f8831-145">Wie wird ein Status, der Erfolg oder Fehler angegeben?</span><span class="sxs-lookup"><span data-stu-id="f8831-145">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="f8831-146">Ein Handler für die erfolgreiche Ausführung durch den Aufruf `context.Succeed(IAuthorizationRequirement requirement)`, übergeben der Anforderung, die erfolgreich überprüft wurde.</span><span class="sxs-lookup"><span data-stu-id="f8831-146">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="f8831-147">Ein Ereignishandler muss nicht in der Regel Fehler zu behandeln, wie andere Handler für die gleiche Anforderung erfolgreich ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="f8831-147">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="f8831-148">Um Fehler zu gewährleisten, selbst wenn andere Handler für die Anforderung erfolgreich ist, rufen Sie `context.Fail`.</span><span class="sxs-lookup"><span data-stu-id="f8831-148">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="f8831-149">Bei Festlegung auf `false`, [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) Eigenschaft (verfügbar in ASP.NET Core 1.1 und höher) wird verkürzt, die Ausführung der Handler bei `context.Fail` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="f8831-149">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="f8831-150">`InvokeHandlersAfterFailure` Standardmäßig `true`, in diesem Fall alle Ereignishandler aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="f8831-150">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span> <span data-ttu-id="f8831-151">Dadurch können Anforderungen zum Erzeugen von Nebeneffekten, z. B. die Protokollierung, die immer erfolgen, wenn `context.Fail` in einen anderen Handler aufgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="f8831-151">This allows requirements to produce side effects, such as logging, which always take place even if `context.Fail` has been called in another handler.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="f8831-152">Warum sollte ich mehrere Handler für eine Anforderung die?</span><span class="sxs-lookup"><span data-stu-id="f8831-152">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="f8831-153">In Fällen, in dem Sie Auswertung auf eine **oder** Basis, mehrere Handler für eine einzelne Anforderung zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="f8831-153">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="f8831-154">Microsoft hat z. B. Türen, die nur mit Schlüssel Karten geöffnet werden.</span><span class="sxs-lookup"><span data-stu-id="f8831-154">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="f8831-155">Wenn Sie Ihre Schlüsselkarte zu Hause lassen, wird die Empfangsdame druckt einen temporären Aufkleber und öffnet die Tür für Sie.</span><span class="sxs-lookup"><span data-stu-id="f8831-155">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="f8831-156">In diesem Fall müssten Sie eine einzelne Anforderung *BuildingEntry*, aber mehrere Handler, die jeweils eine einzelne Anforderung zu untersuchen.</span><span class="sxs-lookup"><span data-stu-id="f8831-156">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="f8831-157">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="f8831-157">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="f8831-158">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="f8831-158">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="f8831-159">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="f8831-159">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="f8831-160">Stellen Sie sicher, dass beide Handler sind [registriert](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="f8831-160">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="f8831-161">Wenn beide Handler erfolgreich ist, wenn eine Richtlinie ausgewertet wird die `BuildingEntryRequirement`, die richtlinienauswertung erfolgreich ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="f8831-161">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="f8831-162">Eine Funktion verwenden, um eine Richtlinie zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="f8831-162">Using a func to fulfill a policy</span></span>

<span data-ttu-id="f8831-163">Es gibt möglicherweise Situationen, in die Ausführen eine Richtlinie einfach in Code Ausdrücken ist.</span><span class="sxs-lookup"><span data-stu-id="f8831-163">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="f8831-164">Es ist möglich, geben Sie einen `Func<AuthorizationHandlerContext, bool>` bei der Konfiguration Ihrer Richtlinie mit den `RequireAssertion` Richtlinie-Generator.</span><span class="sxs-lookup"><span data-stu-id="f8831-164">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="f8831-165">Z. B. den vorherigen `BadgeEntryHandler` könnte wie folgt umgeschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="f8831-165">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=52-53,57-63)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="f8831-166">Zugriff auf MVC-Anforderungskontext in Ereignishandlern</span><span class="sxs-lookup"><span data-stu-id="f8831-166">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="f8831-167">Die `HandleRequirementAsync` Methode, die Sie in einem autorisierungshandler implementieren, hat zwei Parameter: eine `AuthorizationHandlerContext` und `TRequirement` behandelnden.</span><span class="sxs-lookup"><span data-stu-id="f8831-167">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="f8831-168">Frameworks wie MVC oder Jabbr sind kostenlos, fügen jedes beliebige Objekt an die `Resource` Eigenschaft für die `AuthorizationHandlerContext` um zusätzliche Informationen zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="f8831-168">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="f8831-169">Z. B. MVC übergibt eine Instanz der [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in die `Resource` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="f8831-169">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="f8831-170">Diese Eigenschaft bietet Zugriff auf `HttpContext`, `RouteData`, und alles, was andernfalls von MVC und Razor-Seiten bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="f8831-170">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="f8831-171">Die Verwendung der `Resource` -Eigenschaft ist die spezifische Framework.</span><span class="sxs-lookup"><span data-stu-id="f8831-171">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="f8831-172">Mithilfe der Informationen in den `Resource` Eigenschaft schränkt Ihre Autorisierungsrichtlinien auf bestimmten Frameworks.</span><span class="sxs-lookup"><span data-stu-id="f8831-172">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="f8831-173">Sollten Sie eine Umwandlung der `Resource` Eigenschaft mithilfe der `as` -Schlüsselwort, und bestätigen Sie dann die Umwandlung wurde erfolgreich ausgeführt werden, um sicherzustellen, dass Ihr Code nicht abstürzt. mit einer `InvalidCastException` bei Ausführung auf anderen Frameworks:</span><span class="sxs-lookup"><span data-stu-id="f8831-173">You should cast the `Resource` property using the `as` keyword, and then confirm the cast has succeed to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```
