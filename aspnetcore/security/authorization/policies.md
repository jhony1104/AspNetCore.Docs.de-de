---
title: Richtlinienbasierte Autorisierung in ASP.NET Core
author: rick-anderson
description: Informationen Sie zum Erstellen und Verwenden von Authorization Policy-Handler für das Erzwingen von autorisierungsanforderungen in einer ASP.NET Core-app.
ms.author: riande
ms.custom: mvc
ms.date: 04/05/2019
uid: security/authorization/policies
ms.openlocfilehash: ea9d687d3810c104d5b3fa39033849c21569709b
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59068169"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="cc4cf-103">Richtlinienbasierte Autorisierung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cc4cf-103">Policy-based authorization in ASP.NET Core</span></span>

<span data-ttu-id="cc4cf-104">Im Hintergrund [rollenbasierte Autorisierung](xref:security/authorization/roles) und [anspruchsbasierte Autorisierung](xref:security/authorization/claims) verwenden Sie eine Anforderung, einen Handler für die Anforderung und einer vorkonfigurierten Richtlinie.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="cc4cf-105">Diese Bausteine unterstützen den Ausdruck der auswertungen der Autorisierung im Code.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="cc4cf-106">Das Ergebnis ist eine umfangreichere, wieder verwendbare, testfähigen Autorisierung-Struktur.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="cc4cf-107">Eine Autorisierungsrichtlinie besteht eine oder mehrere Anforderungen aus.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="cc4cf-108">Er ist als Teil der autorisierungsdienstkonfiguration registriert, der `Startup.ConfigureServices` Methode:</span><span class="sxs-lookup"><span data-stu-id="cc4cf-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="cc4cf-109">Im vorherigen Beispiel wird eine Richtlinie für "AtLeast21" erstellt.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="cc4cf-110">Es wurde eine einzelne Anforderung&mdash;mit einem Mindestalter, die als Parameter für die Anforderung bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="cc4cf-111">Anwenden von Richtlinien auf MVC-Controller</span><span class="sxs-lookup"><span data-stu-id="cc4cf-111">Applying policies to MVC controllers</span></span>

<span data-ttu-id="cc4cf-112">Wenn Sie Razor-Seiten verwenden, finden Sie unter [Anwenden von Richtlinien auf Razor Pages](#applying-policies-to-razor-pages) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-112">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="cc4cf-113">Richtlinien auf Controller angewendet werden, mithilfe der `[Authorize]` Attribut mit dem Richtliniennamen.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-113">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="cc4cf-114">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cc4cf-114">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="cc4cf-115">Anwenden von Richtlinien auf Razor-Seiten</span><span class="sxs-lookup"><span data-stu-id="cc4cf-115">Applying policies to Razor Pages</span></span>

<span data-ttu-id="cc4cf-116">Richtlinien werden angewendet, Razor Pages unter Verwendung der `[Authorize]` Attribut mit dem Richtliniennamen.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-116">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="cc4cf-117">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cc4cf-117">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="cc4cf-118">Richtlinien können auch auf Razor Pages angewendet werden, mithilfe einer [Autorisierung Konvention](xref:security/authorization/razor-pages-authorization).</span><span class="sxs-lookup"><span data-stu-id="cc4cf-118">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="cc4cf-119">Anforderungen</span><span class="sxs-lookup"><span data-stu-id="cc4cf-119">Requirements</span></span>

<span data-ttu-id="cc4cf-120">Eine autorisierungsanforderung ist eine Auflistung von Datenparametern, die eine Richtlinie zum Auswerten des aktuelle Benutzerprinzipals verwenden können.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-120">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="cc4cf-121">In unserer "AtLeast21"-Richtlinie die Anforderung ist ein einzelner Parameter&mdash;das Mindestalter.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-121">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="cc4cf-122">Eine Anforderung implementiert [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), dies ist eine leere Markierungsschnittstelle.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-122">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="cc4cf-123">Eine parametrisierte Mindestalter-Anforderung kann wie folgt implementiert werden:</span><span class="sxs-lookup"><span data-stu-id="cc4cf-123">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="cc4cf-124">Eine Autorisierungsrichtlinie mehrere autorisierungsanforderungen enthält, müssen alle Anforderungen in der Reihenfolge für die richtlinienauswertung erfolgreich übergeben.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-124">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="cc4cf-125">Anders ausgedrückt, sind mehrere autorisierungsanforderungen, die eine einzelne Autorisierungsrichtlinie hinzugefügt auf behandelt eine **und** Basis.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-125">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="cc4cf-126">Eine Anforderung benötigen nicht Daten oder Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-126">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="cc4cf-127">Authorization-Handler</span><span class="sxs-lookup"><span data-stu-id="cc4cf-127">Authorization handlers</span></span>

<span data-ttu-id="cc4cf-128">Ein autorisierungshandler ist verantwortlich für die Auswertung von Eigenschaften für die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-128">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="cc4cf-129">Der autorisierungshandler für die wertet die Anforderungen für ein bereitgestelltes [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) zu bestimmen, ob der Zugriff zulässig ist.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-129">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="cc4cf-130">Eine Anforderung haben [mehrere Handler](#security-authorization-policies-based-multiple-handlers).</span><span class="sxs-lookup"><span data-stu-id="cc4cf-130">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="cc4cf-131">Ein Handler kann erben [AuthorizationHandler\<TRequirement >](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), wobei `TRequirement` ist die Anforderung behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-131">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="cc4cf-132">Alternativ kann ein Handler implementieren [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) um mehr als eine Art von Anforderung zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-132">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="cc4cf-133">Verwenden Sie einen Handler für eine Anforderung</span><span class="sxs-lookup"><span data-stu-id="cc4cf-133">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="cc4cf-134">Im folgenden finden ein Beispiel für eine direkte Beziehung, in der ein Mindestalter-Handler eine einzelne Anforderung verwendet:</span><span class="sxs-lookup"><span data-stu-id="cc4cf-134">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="cc4cf-135">Der vorangehende Code wird bestimmt, ob der aktuelle Benutzer, der Dienstprinzipal verfügt ein Geburtsdatum Anspruch, der von einem bekannten und vertrauenswürdigen Aussteller ausgegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-135">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="cc4cf-136">Autorisierung kann nicht auftreten, wenn der Anspruch nicht vorhanden ist, wird in diesem Fall eine abgeschlossene Aufgabe zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-136">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="cc4cf-137">Wenn ein Anspruch vorhanden ist, wird das Alter des Benutzers berechnet.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-137">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="cc4cf-138">Wenn der Benutzer das Mindestalter, definiert durch die Anforderung erfüllt, wird die Autorisierung erfolgreich angesehen.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-138">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="cc4cf-139">Wenn eine Autorisierung erfolgreich ist, wird `context.Succeed` wird aufgerufen, mit der Anforderung erfüllt, als einzigen Parameter.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-139">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="cc4cf-140">Verwenden Sie einen Handler für mehrere Anforderungen</span><span class="sxs-lookup"><span data-stu-id="cc4cf-140">Use a handler for multiple requirements</span></span>

<span data-ttu-id="cc4cf-141">Im folgenden finden ein Beispiel für eine 1: n Beziehung, in der ein Berechtigung-Handler drei verschiedene Typen von Anforderungen verarbeiten kann:</span><span class="sxs-lookup"><span data-stu-id="cc4cf-141">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="cc4cf-142">Der vorangehende Code durchläuft [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;eine Eigenschaft, die sowohl die Anforderungen nicht als erfolgreich gekennzeichnet.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-142">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="cc4cf-143">Für eine `ReadPermission` Anforderung entweder "Besitzer" oder "Sponsor" aus, um die angeforderte Ressource zuzugreifen, muss der Benutzer sein.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-143">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="cc4cf-144">Im Fall von einem `EditPermission` oder `DeletePermission` erforderlich, er oder sie muss einen Besitzer aus, um die angeforderte Ressource zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-144">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="cc4cf-145">Registrierung</span><span class="sxs-lookup"><span data-stu-id="cc4cf-145">Handler registration</span></span>

<span data-ttu-id="cc4cf-146">Handler werden in der dienstauflistung während der Konfiguration registriert.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-146">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="cc4cf-147">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cc4cf-147">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="cc4cf-148">Der vorangehende Code registriert `MinimumAgeHandler` als Singleton durch den Aufruf `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-148">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="cc4cf-149">Handler können registriert werden, mithilfe der integrierten [service Lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="cc4cf-149">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="cc4cf-150">Was sollte es sich um ein Handler zurückgeben?</span><span class="sxs-lookup"><span data-stu-id="cc4cf-150">What should a handler return?</span></span>

<span data-ttu-id="cc4cf-151">Beachten Sie, dass die `Handle` -Methode in der die [Handler Beispiel](#security-authorization-handler-example) keinen Wert zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-151">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="cc4cf-152">Wie wird ein Status, der Erfolg oder Fehler angegeben?</span><span class="sxs-lookup"><span data-stu-id="cc4cf-152">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="cc4cf-153">Ein Handler für die erfolgreiche Ausführung durch den Aufruf `context.Succeed(IAuthorizationRequirement requirement)`, übergeben der Anforderung, die erfolgreich überprüft wurde.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-153">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="cc4cf-154">Ein Ereignishandler muss nicht in der Regel Fehler zu behandeln, wie andere Handler für die gleiche Anforderung erfolgreich ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-154">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="cc4cf-155">Um Fehler zu gewährleisten, selbst wenn andere Handler für die Anforderung erfolgreich ist, rufen Sie `context.Fail`.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-155">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="cc4cf-156">Wenn Sie einen Handler aufruft `context.Succeed` oder `context.Fail`, alle anderen Ereignishandler werden immer noch aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-156">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="cc4cf-157">Dadurch können Anforderungen zum Erzeugen von Nebeneffekten, z. B. die Protokollierung, die stattfindet, wenn auch einen anderen Handler wurde erfolgreich überprüft oder Fehler bei Anforderung.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-157">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="cc4cf-158">Bei Festlegung auf `false`, [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) Eigenschaft (verfügbar in ASP.NET Core 1.1 und höher) wird verkürzt, die Ausführung der Handler bei `context.Fail` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-158">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="cc4cf-159">`InvokeHandlersAfterFailure` Standardmäßig `true`, in diesem Fall alle Ereignishandler aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-159">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="cc4cf-160">Authorization-Ereignishandler werden aufgerufen, selbst wenn die Authentifizierung fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-160">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="cc4cf-161">Warum sollte ich mehrere Handler für eine Anforderung die?</span><span class="sxs-lookup"><span data-stu-id="cc4cf-161">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="cc4cf-162">In Fällen, in dem Sie Auswertung auf eine **oder** Basis, mehrere Handler für eine einzelne Anforderung zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-162">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="cc4cf-163">Microsoft hat z. B. Türen, die nur mit Schlüssel Karten geöffnet werden.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-163">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="cc4cf-164">Wenn Sie Ihre Schlüsselkarte zu Hause lassen, wird die Empfangsdame druckt einen temporären Aufkleber und öffnet die Tür für Sie.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-164">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="cc4cf-165">In diesem Fall müssten Sie eine einzelne Anforderung *BuildingEntry*, aber mehrere Handler, die jeweils eine einzelne Anforderung zu untersuchen.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-165">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="cc4cf-166">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="cc4cf-166">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="cc4cf-167">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="cc4cf-167">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="cc4cf-168">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="cc4cf-168">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="cc4cf-169">Stellen Sie sicher, dass beide Handler sind [registriert](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span><span class="sxs-lookup"><span data-stu-id="cc4cf-169">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="cc4cf-170">Wenn beide Handler erfolgreich ist, wenn eine Richtlinie ausgewertet wird die `BuildingEntryRequirement`, die richtlinienauswertung erfolgreich ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-170">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="cc4cf-171">Eine Funktion verwenden, um eine Richtlinie zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-171">Using a func to fulfill a policy</span></span>

<span data-ttu-id="cc4cf-172">Es gibt möglicherweise Situationen, in die Ausführen eine Richtlinie einfach in Code Ausdrücken ist.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-172">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="cc4cf-173">Es ist möglich, geben Sie einen `Func<AuthorizationHandlerContext, bool>` bei der Konfiguration Ihrer Richtlinie mit den `RequireAssertion` Richtlinie-Generator.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-173">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="cc4cf-174">Z. B. den vorherigen `BadgeEntryHandler` könnte wie folgt umgeschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="cc4cf-174">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="cc4cf-175">Zugriff auf MVC-Anforderungskontext in Ereignishandlern</span><span class="sxs-lookup"><span data-stu-id="cc4cf-175">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="cc4cf-176">Die `HandleRequirementAsync` Methode, die Sie in einem autorisierungshandler implementieren, hat zwei Parameter: eine `AuthorizationHandlerContext` und `TRequirement` behandelnden.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-176">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="cc4cf-177">Frameworks wie MVC oder Jabbr sind kostenlos, fügen jedes beliebige Objekt an die `Resource` Eigenschaft für die `AuthorizationHandlerContext` um zusätzliche Informationen zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-177">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="cc4cf-178">Z. B. MVC übergibt eine Instanz der [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in die `Resource` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-178">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="cc4cf-179">Diese Eigenschaft bietet Zugriff auf `HttpContext`, `RouteData`, und alles, was andernfalls von MVC und Razor-Seiten bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-179">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="cc4cf-180">Die Verwendung der `Resource` -Eigenschaft ist die spezifische Framework.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-180">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="cc4cf-181">Mithilfe der Informationen in den `Resource` Eigenschaft schränkt Ihre Autorisierungsrichtlinien auf bestimmten Frameworks.</span><span class="sxs-lookup"><span data-stu-id="cc4cf-181">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="cc4cf-182">Sollten Sie eine Umwandlung der `Resource` Eigenschaft mithilfe der `is` -Schlüsselwort, und bestätigen Sie dann die Umwandlung erfolgreich war, um sicherzustellen, dass Ihr Code nicht abstürzt. mit einer `InvalidCastException` bei Ausführung auf anderen Frameworks:</span><span class="sxs-lookup"><span data-stu-id="cc4cf-182">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```
