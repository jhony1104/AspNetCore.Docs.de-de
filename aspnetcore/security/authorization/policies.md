---
title: Policy-basierte Autorisierung in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie Autorisierungsrichtlinienhandler zum Erzwingen von Autorisierungsanforderungen in einer ASP.NET Core-App erstellen und verwenden.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
uid: security/authorization/policies
ms.openlocfilehash: 66412a6020ea8f12427c8c5b466e1b2eedccf0f9
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488760"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="cba26-103">Policy-basierte Autorisierung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cba26-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cba26-104">Unter den Abdeckungen verwenden [rollenbasierte Autorisierung](xref:security/authorization/roles) und [anspruchsbasierte Autorisierung](xref:security/authorization/claims) eine Anforderung, einen Anforderungshandler und eine vorkonfigurierte Richtlinie.</span><span class="sxs-lookup"><span data-stu-id="cba26-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="cba26-105">Diese Bausteine unterstützen die Ausprägung von Autorisierungsauswertungen im Code.</span><span class="sxs-lookup"><span data-stu-id="cba26-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="cba26-106">Das Ergebnis ist eine umfangreichere, wiederverwendbare, testbare Autorisierungsstruktur.</span><span class="sxs-lookup"><span data-stu-id="cba26-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="cba26-107">Eine Autorisierungsrichtlinie besteht aus einer oder mehreren Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="cba26-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="cba26-108">Es ist als Teil der Autorisierungsdienstkonfiguration `Startup.ConfigureServices` in der Methode registriert:</span><span class="sxs-lookup"><span data-stu-id="cba26-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="cba26-109">Im vorherigen Beispiel wird eine "AtLeast21"-Richtlinie erstellt.</span><span class="sxs-lookup"><span data-stu-id="cba26-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="cba26-110">Es hat eine&mdash;einzige Anforderung, dass ein Mindestalter, das als Parameter für die Anforderung angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="cba26-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="cba26-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="cba26-111">IAuthorizationService</span></span> 

<span data-ttu-id="cba26-112">Der primäre Dienst, der bestimmt, ob die Autorisierung erfolgreich ist, ist: <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService></span><span class="sxs-lookup"><span data-stu-id="cba26-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="cba26-113">Der vorherige Code hebt die beiden Methoden des [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)hervor.</span><span class="sxs-lookup"><span data-stu-id="cba26-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="cba26-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>ist ein Markerdienst ohne Methoden und der Mechanismus zum Nachverfolgen, ob die Autorisierung erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="cba26-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="cba26-115">Jeder <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> ist für die Überprüfung verantwortlich, ob die Anforderungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="cba26-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

<span data-ttu-id="cba26-116">Die <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> Klasse verwendet der Handler, um zu markieren, ob anforderungenerfüllt wurden:</span><span class="sxs-lookup"><span data-stu-id="cba26-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="cba26-117">Der folgende Code zeigt die vereinfachte (und mit Kommentaren kommentierte) Standardimplementierung des Autorisierungsdienstes:</span><span class="sxs-lookup"><span data-stu-id="cba26-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

<span data-ttu-id="cba26-118">Der folgende Code `ConfigureServices`zeigt eine typische:</span><span class="sxs-lookup"><span data-stu-id="cba26-118">The following code shows a typical `ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddControllersWithViews();
    services.AddRazorPages();
}
```

<span data-ttu-id="cba26-119">Verwendung <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` oder Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="cba26-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="cba26-120">Anwenden von Richtlinien auf MVC-Controller</span><span class="sxs-lookup"><span data-stu-id="cba26-120">Applying policies to MVC controllers</span></span>

<span data-ttu-id="cba26-121">Wenn Sie Razor-Seiten verwenden, finden Sie weitere Informationen unter [Anwenden von Richtlinien auf Razor-Seiten](#applying-policies-to-razor-pages) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="cba26-121">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="cba26-122">Richtlinien werden auf Controller `[Authorize]` angewendet, indem das Attribut mit dem Richtliniennamen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="cba26-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="cba26-123">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cba26-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="cba26-124">Anwenden von Richtlinien auf Razor-Seiten</span><span class="sxs-lookup"><span data-stu-id="cba26-124">Applying policies to Razor Pages</span></span>

<span data-ttu-id="cba26-125">Richtlinien werden auf Razor Pages `[Authorize]` angewendet, indem das Attribut mit dem Richtliniennamen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="cba26-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="cba26-126">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cba26-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="cba26-127">Richtlinien können auch mithilfe einer [Autorisierungskonvention](xref:security/authorization/razor-pages-authorization)auf Razor-Seiten angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="cba26-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="cba26-128">Requirements (Anforderungen)</span><span class="sxs-lookup"><span data-stu-id="cba26-128">Requirements</span></span>

<span data-ttu-id="cba26-129">Eine Autorisierungsanforderung ist eine Sammlung von Datenparametern, die eine Richtlinie zum Auswerten des aktuellen Benutzerprinzipals verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="cba26-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="cba26-130">In unserer "AtLeast21"-Richtlinie ist die&mdash;Anforderung ein einzelner Parameter das Mindestalter.</span><span class="sxs-lookup"><span data-stu-id="cba26-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="cba26-131">Eine Anforderung implementiert [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), die eine leere Markerschnittstelle ist.</span><span class="sxs-lookup"><span data-stu-id="cba26-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="cba26-132">Eine parametrisierte Mindestaltersanforderung könnte wie folgt implementiert werden:</span><span class="sxs-lookup"><span data-stu-id="cba26-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="cba26-133">Wenn eine Autorisierungsrichtlinie mehrere Autorisierungsanforderungen enthält, müssen alle Anforderungen erfüllt sein, damit die Richtlinienbewertung erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="cba26-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="cba26-134">Mit anderen Worten, mehrere Autorisierungsanforderungen, die einer einzelnen Autorisierungsrichtlinie hinzugefügt werden, werden auf **UND-Basis** behandelt.</span><span class="sxs-lookup"><span data-stu-id="cba26-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="cba26-135">Eine Anforderung muss keine Daten oder Eigenschaften enthalten.</span><span class="sxs-lookup"><span data-stu-id="cba26-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="cba26-136">Autorisierungshandler</span><span class="sxs-lookup"><span data-stu-id="cba26-136">Authorization handlers</span></span>

<span data-ttu-id="cba26-137">Ein Autorisierungshandler ist für die Bewertung der Eigenschaften einer Anforderung verantwortlich.</span><span class="sxs-lookup"><span data-stu-id="cba26-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="cba26-138">Der Autorisierungshandler wertet die Anforderungen anhand eines bereitgestellten [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) aus, um zu ermitteln, ob der Zugriff zulässig ist.</span><span class="sxs-lookup"><span data-stu-id="cba26-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="cba26-139">Eine Anforderung kann [über mehrere Handler](#security-authorization-policies-based-multiple-handlers)verfügen.</span><span class="sxs-lookup"><span data-stu-id="cba26-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="cba26-140">Ein Handler kann [AuthorizationHandler\<TRequirement ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)>`TRequirement` erben, wobei die serkende Anforderung ist.</span><span class="sxs-lookup"><span data-stu-id="cba26-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="cba26-141">Alternativ kann ein Handler [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) implementieren, um mehr als einen Anforderungstyp zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="cba26-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="cba26-142">Verwenden eines Handlers für eine Anforderung</span><span class="sxs-lookup"><span data-stu-id="cba26-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="cba26-143">Im Folgenden finden Sie ein Beispiel für eine 1:1-Beziehung, in der ein Mindestaltershandler eine einzelne Anforderung verwendet:</span><span class="sxs-lookup"><span data-stu-id="cba26-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="cba26-144">Der vorstehende Code bestimmt, ob der aktuelle Benutzerprinzipal über einen Geburtsdatumsanspruch verfügt, der von einem bekannten und vertrauenswürdigen Aussteller ausgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="cba26-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="cba26-145">Die Autorisierung kann nicht erfolgen, wenn der Anspruch fehlt, in diesem Fall wird eine abgeschlossene Aufgabe zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="cba26-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="cba26-146">Wenn ein Anspruch vorhanden ist, wird das Alter des Benutzers berechnet.</span><span class="sxs-lookup"><span data-stu-id="cba26-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="cba26-147">Erfüllt der Benutzer das in der Anforderung festgelegte Mindestalter, gilt die Autorisierung als erfolgreich.</span><span class="sxs-lookup"><span data-stu-id="cba26-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="cba26-148">Wenn die Autorisierung `context.Succeed` erfolgreich ist, wird mit der erfüllten Anforderung als einziger Parameter aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="cba26-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="cba26-149">Verwenden eines Handlers für mehrere Anforderungen</span><span class="sxs-lookup"><span data-stu-id="cba26-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="cba26-150">Im Folgenden finden Sie ein Beispiel für eine 1:n-Beziehung, in der ein Berechtigungshandler drei verschiedene Arten von Anforderungen verarbeiten kann:</span><span class="sxs-lookup"><span data-stu-id="cba26-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="cba26-151">Der vorherige Code durchläuft [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;eine Eigenschaft, die Anforderungen enthält, die nicht als erfolgreich markiert sind.</span><span class="sxs-lookup"><span data-stu-id="cba26-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="cba26-152">Für `ReadPermission` eine Anforderung muss der Benutzer entweder ein Besitzer oder ein Sponsor sein, um auf die angeforderte Ressource zugreifen zu können.</span><span class="sxs-lookup"><span data-stu-id="cba26-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="cba26-153">Im Falle einer `EditPermission` `DeletePermission` Anforderung oder Anforderung muss er ein Besitzer sein, um auf die angeforderte Ressource zugreifen zu können.</span><span class="sxs-lookup"><span data-stu-id="cba26-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="cba26-154">Handlerregistrierung</span><span class="sxs-lookup"><span data-stu-id="cba26-154">Handler registration</span></span>

<span data-ttu-id="cba26-155">Handler werden während der Konfiguration in der Dienstauflistung registriert.</span><span class="sxs-lookup"><span data-stu-id="cba26-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="cba26-156">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cba26-156">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="cba26-157">Der vorhergehende `MinimumAgeHandler` Code registriert sich als `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`Singleton, indem er auf .</span><span class="sxs-lookup"><span data-stu-id="cba26-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="cba26-158">Handler können mit einer der integrierten [Dienstlebensdauern](xref:fundamentals/dependency-injection#service-lifetimes)registriert werden.</span><span class="sxs-lookup"><span data-stu-id="cba26-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="cba26-159">Was sollte ein Handler zurückgeben?</span><span class="sxs-lookup"><span data-stu-id="cba26-159">What should a handler return?</span></span>

<span data-ttu-id="cba26-160">Beachten Sie, dass die `Handle` Methode im [Handlerbeispiel](#security-authorization-handler-example) keinen Wert zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="cba26-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="cba26-161">Wie wird der Status von Erfolg oder Misserfolg angezeigt?</span><span class="sxs-lookup"><span data-stu-id="cba26-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="cba26-162">Ein Handler gibt `context.Succeed(IAuthorizationRequirement requirement)`den Erfolg an, indem er die Anforderung, die erfolgreich überprüft wurde, übergibt.</span><span class="sxs-lookup"><span data-stu-id="cba26-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="cba26-163">Ein Handler muss Fehler nicht im Allgemeinen behandeln, da andere Handler für dieselbe Anforderung erfolgreich sein können.</span><span class="sxs-lookup"><span data-stu-id="cba26-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="cba26-164">Um einen Fehler zu gewährleisten, rufen `context.Fail`Sie auch dann auf, wenn andere Anforderungshandler erfolgreich sind.</span><span class="sxs-lookup"><span data-stu-id="cba26-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="cba26-165">Wenn ein `context.Succeed` Handler `context.Fail`aufruft oder , werden alle anderen Handler weiterhin aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="cba26-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="cba26-166">Dadurch können Anforderungen Nebenwirkungen wie Protokollierung erzeugen, die auch dann stattfindet, wenn ein anderer Handler eine Anforderung erfolgreich validiert oder nicht bestanden hat.</span><span class="sxs-lookup"><span data-stu-id="cba26-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="cba26-167">Wenn auf `false`festgelegt, verkürzt die [InvokeHandlersAfterFailure-Eigenschaft](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (verfügbar in ASP.NET Core 1.1 `context.Fail` und höher) die Ausführung von Handlern, wenn sie aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="cba26-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="cba26-168">`InvokeHandlersAfterFailure`Standardwerte `true`auf , in diesem Fall werden alle Handler aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="cba26-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="cba26-169">Autorisierungshandler werden auch dann aufgerufen, wenn die Authentifizierung fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="cba26-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="cba26-170">Warum sollte ich mehrere Handler für eine Anforderung verwenden?</span><span class="sxs-lookup"><span data-stu-id="cba26-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="cba26-171">In Fällen, in denen die Evaluierung auf **ODER-Basis** erfolgen soll, implementieren Sie mehrere Handler für eine einzelne Anforderung.</span><span class="sxs-lookup"><span data-stu-id="cba26-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="cba26-172">Microsoft verfügt beispielsweise über Türen, die sich nur mit Schlüsselkarten öffnen.</span><span class="sxs-lookup"><span data-stu-id="cba26-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="cba26-173">Wenn Sie Ihre Schlüsselkarte zu Hause lassen, druckt die Rezeptionistin einen temporären Aufkleber und öffnet Ihnen die Tür.</span><span class="sxs-lookup"><span data-stu-id="cba26-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="cba26-174">In diesem Szenario haben Sie eine einzelne Anforderung, *BuildingEntry*, aber mehrere Handler, wobei jeder eine einzelne Anforderung untersucht.</span><span class="sxs-lookup"><span data-stu-id="cba26-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="cba26-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="cba26-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="cba26-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="cba26-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="cba26-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="cba26-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="cba26-178">Stellen Sie sicher, dass beide Handler [registriert](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)sind.</span><span class="sxs-lookup"><span data-stu-id="cba26-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="cba26-179">Wenn einer der Handler erfolgreich ist, wenn eine Richtlinie die `BuildingEntryRequirement`auswertet, ist die Richtlinienauswertung erfolgreich.</span><span class="sxs-lookup"><span data-stu-id="cba26-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="cba26-180">Verwenden eines Func zur Erfüllung einer Richtlinie</span><span class="sxs-lookup"><span data-stu-id="cba26-180">Using a func to fulfill a policy</span></span>

<span data-ttu-id="cba26-181">Es kann Situationen geben, in denen die Erfüllung einer Richtlinie einfach im Code auszudrücken ist.</span><span class="sxs-lookup"><span data-stu-id="cba26-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="cba26-182">Es ist möglich, `Func<AuthorizationHandlerContext, bool>` eine beim Konfigurieren `RequireAssertion` Ihrer Richtlinie mit dem Richtlinien-Generator zu liefern.</span><span class="sxs-lookup"><span data-stu-id="cba26-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="cba26-183">Die vorherige `BadgeEntryHandler` könnte z. B. wie folgt umgeschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="cba26-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="cba26-184">Zugriff auf MVC-Anforderungskontext in Handlern</span><span class="sxs-lookup"><span data-stu-id="cba26-184">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="cba26-185">Die `HandleRequirementAsync` Methode, die Sie in einem `AuthorizationHandlerContext` Autorisierungshandler implementieren, hat zwei Parameter: eine und die, die `TRequirement` Sie behandeln.</span><span class="sxs-lookup"><span data-stu-id="cba26-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="cba26-186">Frameworks wie MVC oder Jabbr können der `Resource` Eigenschaft auf `AuthorizationHandlerContext` der beliebige Objektdatei hinzufügen, um zusätzliche Informationen zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="cba26-186">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="cba26-187">MVC übergibt z. B. eine `Resource` Instanz von [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in der Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="cba26-187">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="cba26-188">Diese Eigenschaft bietet `HttpContext` `RouteData`Zugriff auf , und alles andere, was von MVC und Razor Pages bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="cba26-188">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="cba26-189">Die Verwendung `Resource` der Eigenschaft ist frameworkspezifisch.</span><span class="sxs-lookup"><span data-stu-id="cba26-189">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="cba26-190">Die Verwendung `Resource` von Informationen in der Eigenschaft beschränkt Ihre Autorisierungsrichtlinien auf bestimmte Frameworks.</span><span class="sxs-lookup"><span data-stu-id="cba26-190">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="cba26-191">Sie sollten `Resource` die Eigenschaft `is` mit dem Schlüsselwort umwerfen und dann bestätigen, dass `InvalidCastException` es der Umwandlung gelungen ist, sicherzustellen, dass Ihr Code nicht mit einem abstürzt, wenn er auf anderen Frameworks ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="cba26-191">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cba26-192">Unter den Abdeckungen verwenden [rollenbasierte Autorisierung](xref:security/authorization/roles) und [anspruchsbasierte Autorisierung](xref:security/authorization/claims) eine Anforderung, einen Anforderungshandler und eine vorkonfigurierte Richtlinie.</span><span class="sxs-lookup"><span data-stu-id="cba26-192">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="cba26-193">Diese Bausteine unterstützen die Ausprägung von Autorisierungsauswertungen im Code.</span><span class="sxs-lookup"><span data-stu-id="cba26-193">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="cba26-194">Das Ergebnis ist eine umfangreichere, wiederverwendbare, testbare Autorisierungsstruktur.</span><span class="sxs-lookup"><span data-stu-id="cba26-194">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="cba26-195">Eine Autorisierungsrichtlinie besteht aus einer oder mehreren Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="cba26-195">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="cba26-196">Es ist als Teil der Autorisierungsdienstkonfiguration `Startup.ConfigureServices` in der Methode registriert:</span><span class="sxs-lookup"><span data-stu-id="cba26-196">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="cba26-197">Im vorherigen Beispiel wird eine "AtLeast21"-Richtlinie erstellt.</span><span class="sxs-lookup"><span data-stu-id="cba26-197">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="cba26-198">Es hat eine&mdash;einzige Anforderung, dass ein Mindestalter, das als Parameter für die Anforderung angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="cba26-198">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="cba26-199">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="cba26-199">IAuthorizationService</span></span> 

<span data-ttu-id="cba26-200">Der primäre Dienst, der bestimmt, ob die Autorisierung erfolgreich ist, ist: <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService></span><span class="sxs-lookup"><span data-stu-id="cba26-200">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="cba26-201">Der vorherige Code hebt die beiden Methoden des [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)hervor.</span><span class="sxs-lookup"><span data-stu-id="cba26-201">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="cba26-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>ist ein Markerdienst ohne Methoden und der Mechanismus zum Nachverfolgen, ob die Autorisierung erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="cba26-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="cba26-203">Jeder <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> ist für die Überprüfung verantwortlich, ob die Anforderungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="cba26-203">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

<span data-ttu-id="cba26-204">Die <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> Klasse verwendet der Handler, um zu markieren, ob anforderungenerfüllt wurden:</span><span class="sxs-lookup"><span data-stu-id="cba26-204">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="cba26-205">Der folgende Code zeigt die vereinfachte (und mit Kommentaren kommentierte) Standardimplementierung des Autorisierungsdienstes:</span><span class="sxs-lookup"><span data-stu-id="cba26-205">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

<span data-ttu-id="cba26-206">Der folgende Code `ConfigureServices`zeigt eine typische:</span><span class="sxs-lookup"><span data-stu-id="cba26-206">The following code shows a typical `ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
}
```

<span data-ttu-id="cba26-207">Verwendung <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` oder Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="cba26-207">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="cba26-208">Anwenden von Richtlinien auf MVC-Controller</span><span class="sxs-lookup"><span data-stu-id="cba26-208">Applying policies to MVC controllers</span></span>

<span data-ttu-id="cba26-209">Wenn Sie Razor-Seiten verwenden, finden Sie weitere Informationen unter [Anwenden von Richtlinien auf Razor-Seiten](#applying-policies-to-razor-pages) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="cba26-209">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="cba26-210">Richtlinien werden auf Controller `[Authorize]` angewendet, indem das Attribut mit dem Richtliniennamen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="cba26-210">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="cba26-211">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cba26-211">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="cba26-212">Anwenden von Richtlinien auf Razor-Seiten</span><span class="sxs-lookup"><span data-stu-id="cba26-212">Applying policies to Razor Pages</span></span>

<span data-ttu-id="cba26-213">Richtlinien werden auf Razor Pages `[Authorize]` angewendet, indem das Attribut mit dem Richtliniennamen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="cba26-213">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="cba26-214">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cba26-214">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="cba26-215">Richtlinien können auch mithilfe einer [Autorisierungskonvention](xref:security/authorization/razor-pages-authorization)auf Razor-Seiten angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="cba26-215">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="cba26-216">Requirements (Anforderungen)</span><span class="sxs-lookup"><span data-stu-id="cba26-216">Requirements</span></span>

<span data-ttu-id="cba26-217">Eine Autorisierungsanforderung ist eine Sammlung von Datenparametern, die eine Richtlinie zum Auswerten des aktuellen Benutzerprinzipals verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="cba26-217">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="cba26-218">In unserer "AtLeast21"-Richtlinie ist die&mdash;Anforderung ein einzelner Parameter das Mindestalter.</span><span class="sxs-lookup"><span data-stu-id="cba26-218">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="cba26-219">Eine Anforderung implementiert [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), die eine leere Markerschnittstelle ist.</span><span class="sxs-lookup"><span data-stu-id="cba26-219">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="cba26-220">Eine parametrisierte Mindestaltersanforderung könnte wie folgt implementiert werden:</span><span class="sxs-lookup"><span data-stu-id="cba26-220">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="cba26-221">Wenn eine Autorisierungsrichtlinie mehrere Autorisierungsanforderungen enthält, müssen alle Anforderungen erfüllt sein, damit die Richtlinienbewertung erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="cba26-221">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="cba26-222">Mit anderen Worten, mehrere Autorisierungsanforderungen, die einer einzelnen Autorisierungsrichtlinie hinzugefügt werden, werden auf **UND-Basis** behandelt.</span><span class="sxs-lookup"><span data-stu-id="cba26-222">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="cba26-223">Eine Anforderung muss keine Daten oder Eigenschaften enthalten.</span><span class="sxs-lookup"><span data-stu-id="cba26-223">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="cba26-224">Autorisierungshandler</span><span class="sxs-lookup"><span data-stu-id="cba26-224">Authorization handlers</span></span>

<span data-ttu-id="cba26-225">Ein Autorisierungshandler ist für die Bewertung der Eigenschaften einer Anforderung verantwortlich.</span><span class="sxs-lookup"><span data-stu-id="cba26-225">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="cba26-226">Der Autorisierungshandler wertet die Anforderungen anhand eines bereitgestellten [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) aus, um zu ermitteln, ob der Zugriff zulässig ist.</span><span class="sxs-lookup"><span data-stu-id="cba26-226">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="cba26-227">Eine Anforderung kann [über mehrere Handler](#security-authorization-policies-based-multiple-handlers)verfügen.</span><span class="sxs-lookup"><span data-stu-id="cba26-227">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="cba26-228">Ein Handler kann [AuthorizationHandler\<TRequirement ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)>`TRequirement` erben, wobei die serkende Anforderung ist.</span><span class="sxs-lookup"><span data-stu-id="cba26-228">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="cba26-229">Alternativ kann ein Handler [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) implementieren, um mehr als einen Anforderungstyp zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="cba26-229">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="cba26-230">Verwenden eines Handlers für eine Anforderung</span><span class="sxs-lookup"><span data-stu-id="cba26-230">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="cba26-231">Im Folgenden finden Sie ein Beispiel für eine 1:1-Beziehung, in der ein Mindestaltershandler eine einzelne Anforderung verwendet:</span><span class="sxs-lookup"><span data-stu-id="cba26-231">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="cba26-232">Der vorstehende Code bestimmt, ob der aktuelle Benutzerprinzipal über einen Geburtsdatumsanspruch verfügt, der von einem bekannten und vertrauenswürdigen Aussteller ausgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="cba26-232">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="cba26-233">Die Autorisierung kann nicht erfolgen, wenn der Anspruch fehlt, in diesem Fall wird eine abgeschlossene Aufgabe zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="cba26-233">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="cba26-234">Wenn ein Anspruch vorhanden ist, wird das Alter des Benutzers berechnet.</span><span class="sxs-lookup"><span data-stu-id="cba26-234">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="cba26-235">Erfüllt der Benutzer das in der Anforderung festgelegte Mindestalter, gilt die Autorisierung als erfolgreich.</span><span class="sxs-lookup"><span data-stu-id="cba26-235">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="cba26-236">Wenn die Autorisierung `context.Succeed` erfolgreich ist, wird mit der erfüllten Anforderung als einziger Parameter aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="cba26-236">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="cba26-237">Verwenden eines Handlers für mehrere Anforderungen</span><span class="sxs-lookup"><span data-stu-id="cba26-237">Use a handler for multiple requirements</span></span>

<span data-ttu-id="cba26-238">Im Folgenden finden Sie ein Beispiel für eine 1:n-Beziehung, in der ein Berechtigungshandler drei verschiedene Arten von Anforderungen verarbeiten kann:</span><span class="sxs-lookup"><span data-stu-id="cba26-238">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="cba26-239">Der vorherige Code durchläuft [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;eine Eigenschaft, die Anforderungen enthält, die nicht als erfolgreich markiert sind.</span><span class="sxs-lookup"><span data-stu-id="cba26-239">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="cba26-240">Für `ReadPermission` eine Anforderung muss der Benutzer entweder ein Besitzer oder ein Sponsor sein, um auf die angeforderte Ressource zugreifen zu können.</span><span class="sxs-lookup"><span data-stu-id="cba26-240">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="cba26-241">Im Falle einer `EditPermission` `DeletePermission` Anforderung oder Anforderung muss er ein Besitzer sein, um auf die angeforderte Ressource zugreifen zu können.</span><span class="sxs-lookup"><span data-stu-id="cba26-241">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="cba26-242">Handlerregistrierung</span><span class="sxs-lookup"><span data-stu-id="cba26-242">Handler registration</span></span>

<span data-ttu-id="cba26-243">Handler werden während der Konfiguration in der Dienstauflistung registriert.</span><span class="sxs-lookup"><span data-stu-id="cba26-243">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="cba26-244">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cba26-244">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="cba26-245">Der vorhergehende `MinimumAgeHandler` Code registriert sich als `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`Singleton, indem er auf .</span><span class="sxs-lookup"><span data-stu-id="cba26-245">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="cba26-246">Handler können mit einer der integrierten [Dienstlebensdauern](xref:fundamentals/dependency-injection#service-lifetimes)registriert werden.</span><span class="sxs-lookup"><span data-stu-id="cba26-246">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="cba26-247">Was sollte ein Handler zurückgeben?</span><span class="sxs-lookup"><span data-stu-id="cba26-247">What should a handler return?</span></span>

<span data-ttu-id="cba26-248">Beachten Sie, dass die `Handle` Methode im [Handlerbeispiel](#security-authorization-handler-example) keinen Wert zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="cba26-248">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="cba26-249">Wie wird der Status von Erfolg oder Misserfolg angezeigt?</span><span class="sxs-lookup"><span data-stu-id="cba26-249">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="cba26-250">Ein Handler gibt `context.Succeed(IAuthorizationRequirement requirement)`den Erfolg an, indem er die Anforderung, die erfolgreich überprüft wurde, übergibt.</span><span class="sxs-lookup"><span data-stu-id="cba26-250">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="cba26-251">Ein Handler muss Fehler nicht im Allgemeinen behandeln, da andere Handler für dieselbe Anforderung erfolgreich sein können.</span><span class="sxs-lookup"><span data-stu-id="cba26-251">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="cba26-252">Um einen Fehler zu gewährleisten, rufen `context.Fail`Sie auch dann auf, wenn andere Anforderungshandler erfolgreich sind.</span><span class="sxs-lookup"><span data-stu-id="cba26-252">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="cba26-253">Wenn ein `context.Succeed` Handler `context.Fail`aufruft oder , werden alle anderen Handler weiterhin aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="cba26-253">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="cba26-254">Dadurch können Anforderungen Nebenwirkungen wie Protokollierung erzeugen, die auch dann stattfindet, wenn ein anderer Handler eine Anforderung erfolgreich validiert oder nicht bestanden hat.</span><span class="sxs-lookup"><span data-stu-id="cba26-254">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="cba26-255">Wenn auf `false`festgelegt, verkürzt die [InvokeHandlersAfterFailure-Eigenschaft](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (verfügbar in ASP.NET Core 1.1 `context.Fail` und höher) die Ausführung von Handlern, wenn sie aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="cba26-255">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="cba26-256">`InvokeHandlersAfterFailure`Standardwerte `true`auf , in diesem Fall werden alle Handler aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="cba26-256">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="cba26-257">Autorisierungshandler werden auch dann aufgerufen, wenn die Authentifizierung fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="cba26-257">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="cba26-258">Warum sollte ich mehrere Handler für eine Anforderung verwenden?</span><span class="sxs-lookup"><span data-stu-id="cba26-258">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="cba26-259">In Fällen, in denen die Evaluierung auf **ODER-Basis** erfolgen soll, implementieren Sie mehrere Handler für eine einzelne Anforderung.</span><span class="sxs-lookup"><span data-stu-id="cba26-259">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="cba26-260">Microsoft verfügt beispielsweise über Türen, die sich nur mit Schlüsselkarten öffnen.</span><span class="sxs-lookup"><span data-stu-id="cba26-260">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="cba26-261">Wenn Sie Ihre Schlüsselkarte zu Hause lassen, druckt die Rezeptionistin einen temporären Aufkleber und öffnet Ihnen die Tür.</span><span class="sxs-lookup"><span data-stu-id="cba26-261">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="cba26-262">In diesem Szenario haben Sie eine einzelne Anforderung, *BuildingEntry*, aber mehrere Handler, wobei jeder eine einzelne Anforderung untersucht.</span><span class="sxs-lookup"><span data-stu-id="cba26-262">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="cba26-263">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="cba26-263">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="cba26-264">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="cba26-264">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="cba26-265">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="cba26-265">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="cba26-266">Stellen Sie sicher, dass beide Handler [registriert](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)sind.</span><span class="sxs-lookup"><span data-stu-id="cba26-266">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="cba26-267">Wenn einer der Handler erfolgreich ist, wenn eine Richtlinie die `BuildingEntryRequirement`auswertet, ist die Richtlinienauswertung erfolgreich.</span><span class="sxs-lookup"><span data-stu-id="cba26-267">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="cba26-268">Verwenden eines Func zur Erfüllung einer Richtlinie</span><span class="sxs-lookup"><span data-stu-id="cba26-268">Using a func to fulfill a policy</span></span>

<span data-ttu-id="cba26-269">Es kann Situationen geben, in denen die Erfüllung einer Richtlinie einfach im Code auszudrücken ist.</span><span class="sxs-lookup"><span data-stu-id="cba26-269">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="cba26-270">Es ist möglich, `Func<AuthorizationHandlerContext, bool>` eine beim Konfigurieren `RequireAssertion` Ihrer Richtlinie mit dem Richtlinien-Generator zu liefern.</span><span class="sxs-lookup"><span data-stu-id="cba26-270">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="cba26-271">Die vorherige `BadgeEntryHandler` könnte z. B. wie folgt umgeschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="cba26-271">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="cba26-272">Zugriff auf MVC-Anforderungskontext in Handlern</span><span class="sxs-lookup"><span data-stu-id="cba26-272">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="cba26-273">Die `HandleRequirementAsync` Methode, die Sie in einem `AuthorizationHandlerContext` Autorisierungshandler implementieren, hat zwei Parameter: eine und die, die `TRequirement` Sie behandeln.</span><span class="sxs-lookup"><span data-stu-id="cba26-273">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="cba26-274">Frameworks wie MVC oder SignalR können der `Resource` Eigenschaft auf `AuthorizationHandlerContext` der beliebige Objektdatei hinzufügen, um zusätzliche Informationen zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="cba26-274">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="cba26-275">Bei der Verwendung des Endpunktroutings wird die Autorisierung in der Regel von der Authorization Middleware durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="cba26-275">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="cba26-276">In diesem Fall `Resource` ist die <xref:Microsoft.AspNetCore.Http.Endpoint>Eigenschaft eine Instanz von .</span><span class="sxs-lookup"><span data-stu-id="cba26-276">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="cba26-277">Der Endpunkt kann verwendet werden, um den zugrunde liegenden Ressourcen zu untersuchen, an die Sie routingen.</span><span class="sxs-lookup"><span data-stu-id="cba26-277">The endpoint can be used to probe the underlying the resource to which you're routing.</span></span> <span data-ttu-id="cba26-278">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cba26-278">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="cba26-279">Bei herkömmlichem Routing oder wenn die Autorisierung als Teil des `Resource` MVC-Autorisierungsfilters erfolgt, ist der Wert von einer <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> Instanz.</span><span class="sxs-lookup"><span data-stu-id="cba26-279">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="cba26-280">Diese Eigenschaft bietet `HttpContext` `RouteData`Zugriff auf , und alles andere, was von MVC und Razor Pages bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="cba26-280">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="cba26-281">Die Verwendung `Resource` der Eigenschaft ist frameworkspezifisch.</span><span class="sxs-lookup"><span data-stu-id="cba26-281">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="cba26-282">Die Verwendung `Resource` von Informationen in der Eigenschaft beschränkt Ihre Autorisierungsrichtlinien auf bestimmte Frameworks.</span><span class="sxs-lookup"><span data-stu-id="cba26-282">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="cba26-283">Sie sollten `Resource` die Eigenschaft `is` mit dem Schlüsselwort umwerfen und dann bestätigen, dass `InvalidCastException` es der Umwandlung gelungen ist, sicherzustellen, dass Ihr Code nicht mit einem abstürzt, wenn er auf anderen Frameworks ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="cba26-283">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
