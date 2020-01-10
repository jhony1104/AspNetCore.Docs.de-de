---
title: Richtlinien basierte Autorisierung in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie Autorisierungs Richtlinien Handler zum Erzwingen von Autorisierungs Anforderungen in einer ASP.net Core-app erstellen und verwenden.
ms.author: riande
ms.custom: mvc
ms.date: 10/05/2019
uid: security/authorization/policies
ms.openlocfilehash: eeb5ddd63ef8177325b35e5a666aa5e9ab047057
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828957"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="b423d-103">Richtlinien basierte Autorisierung in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="b423d-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b423d-104">Im Hintergrund werden bei der [rollenbasierten Autorisierung](xref:security/authorization/roles) und der [Anspruchs basierten Autorisierung](xref:security/authorization/claims) eine Anforderung, ein Anforderungs Handler und eine vorkonfigurierte Richtlinie verwendet.</span><span class="sxs-lookup"><span data-stu-id="b423d-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="b423d-105">Diese Bausteine unterstützen den Ausdruck der Autorisierungs Auswertungen im Code.</span><span class="sxs-lookup"><span data-stu-id="b423d-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="b423d-106">Das Ergebnis ist eine umfangreichere, wiederverwendbare, testbare Autorisierungs Struktur.</span><span class="sxs-lookup"><span data-stu-id="b423d-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="b423d-107">Eine Autorisierungs Richtlinie besteht aus einer oder mehreren Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="b423d-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="b423d-108">Sie wird als Teil der Autorisierungs Dienst Konfiguration in der `Startup.ConfigureServices`-Methode registriert:</span><span class="sxs-lookup"><span data-stu-id="b423d-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="b423d-109">Im vorherigen Beispiel wird eine Richtlinie "AtLeast21" erstellt.</span><span class="sxs-lookup"><span data-stu-id="b423d-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="b423d-110">Sie verfügt über eine einzige Anforderung&mdash;die eines minimalen Alters, das als Parameter für die Anforderung angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="b423d-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="b423d-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="b423d-111">IAuthorizationService</span></span> 

<span data-ttu-id="b423d-112">Der primäre Dienst, der bestimmt, ob die Autorisierung erfolgreich ist, <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span><span class="sxs-lookup"><span data-stu-id="b423d-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="b423d-113">Im vorangehenden Code werden die beiden Methoden des [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="b423d-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="b423d-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> ist ein markerdienst ohne Methoden und der Mechanismus für die Nachverfolgung, ob die Autorisierung erfolgreich war.</span><span class="sxs-lookup"><span data-stu-id="b423d-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="b423d-115">Jede <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> ist für die Überprüfung zuständig, ob die Anforderungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="b423d-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="b423d-116">Die <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext>-Klasse verwendet den Handler, um zu kennzeichnen, ob Anforderungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="b423d-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="b423d-117">Der folgende Code zeigt die vereinfachte (und mit Kommentaren versehene) Standard Implementierung des Autorisierungs Dienstanbieter:</span><span class="sxs-lookup"><span data-stu-id="b423d-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="b423d-118">Der folgende Code zeigt eine typische `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b423d-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="b423d-119">Verwenden Sie für die Autorisierung <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> oder `[Authorize(Policy = "Something")]`.</span><span class="sxs-lookup"><span data-stu-id="b423d-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="b423d-120">Anwenden von Richtlinien auf MVC-Controller</span><span class="sxs-lookup"><span data-stu-id="b423d-120">Applying policies to MVC controllers</span></span>

<span data-ttu-id="b423d-121">Wenn Sie Razor Pages verwenden, finden Sie weitere Informationen unter [Anwenden von Richtlinien auf Razor Pages](#applying-policies-to-razor-pages) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="b423d-121">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="b423d-122">Richtlinien werden mithilfe des `[Authorize]`-Attributs mit dem Richtlinien Namen auf Controller angewendet.</span><span class="sxs-lookup"><span data-stu-id="b423d-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="b423d-123">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="b423d-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="b423d-124">Anwenden von Richtlinien auf Razor Pages</span><span class="sxs-lookup"><span data-stu-id="b423d-124">Applying policies to Razor Pages</span></span>

<span data-ttu-id="b423d-125">Richtlinien werden auf Razor Pages mithilfe des `[Authorize]`-Attributs mit dem Richtlinien Namen angewendet.</span><span class="sxs-lookup"><span data-stu-id="b423d-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="b423d-126">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="b423d-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="b423d-127">Richtlinien können auch auf Razor Pages mithilfe einer [Autorisierungs Konvention](xref:security/authorization/razor-pages-authorization)angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="b423d-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="b423d-128">-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="b423d-128">Requirements</span></span>

<span data-ttu-id="b423d-129">Eine Autorisierungs Anforderung ist eine Auflistung von Daten Parametern, die eine Richtlinie verwenden kann, um den aktuellen Benutzer Prinzipal auszuwerten.</span><span class="sxs-lookup"><span data-stu-id="b423d-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="b423d-130">In unserer Richtlinie "AtLeast21" ist die Anforderung ein einzelner Parameter&mdash;dem minimal Alter.</span><span class="sxs-lookup"><span data-stu-id="b423d-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="b423d-131">Eine Anforderung implementiert [iauthorizationrequirements](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), bei dem es sich um eine leere markerschnittstelle handelt.</span><span class="sxs-lookup"><span data-stu-id="b423d-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="b423d-132">Eine parametrisierte minimale Alters Anforderung kann wie folgt implementiert werden:</span><span class="sxs-lookup"><span data-stu-id="b423d-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="b423d-133">Wenn eine Autorisierungs Richtlinie mehrere Autorisierungs Anforderungen enthält, müssen alle Anforderungen bestanden werden, damit die Richtlinien Auswertung erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="b423d-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="b423d-134">Anders ausgedrückt: mehrere Autorisierungs Anforderungen, die einer einzelnen Autorisierungs Richtlinie hinzugefügt werden, werden auf der Grundlage von **und** behandelt.</span><span class="sxs-lookup"><span data-stu-id="b423d-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="b423d-135">Eine Anforderung muss keine Daten oder Eigenschaften haben.</span><span class="sxs-lookup"><span data-stu-id="b423d-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="b423d-136">Autorisierungs Handler</span><span class="sxs-lookup"><span data-stu-id="b423d-136">Authorization handlers</span></span>

<span data-ttu-id="b423d-137">Ein Autorisierungs Handler ist dafür verantwortlich, die Eigenschaften einer Anforderung zu evaluieren.</span><span class="sxs-lookup"><span data-stu-id="b423d-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="b423d-138">Der Autorisierungs Handler wertet die Anforderungen anhand eines angegebenen [authorizationhandlercontext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) aus, um zu bestimmen, ob der Zugriff zulässig ist.</span><span class="sxs-lookup"><span data-stu-id="b423d-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="b423d-139">Eine Anforderung kann [mehrere Handler](#security-authorization-policies-based-multiple-handlers)aufweisen.</span><span class="sxs-lookup"><span data-stu-id="b423d-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="b423d-140">Ein Handler kann [authorizationhandler\<trequirement >](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)erben, wobei `TRequirement` die erforderliche Anforderung ist.</span><span class="sxs-lookup"><span data-stu-id="b423d-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="b423d-141">Alternativ kann ein Handler [iauthorizationhandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) implementieren, um mehr als eine Art von Anforderung zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="b423d-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="b423d-142">Verwenden eines Handlers für eine Anforderung</span><span class="sxs-lookup"><span data-stu-id="b423d-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="b423d-143">Im folgenden finden Sie ein Beispiel für eine 1:1-Beziehung, bei der ein minimaler Alters Handler eine einzige Anforderung verwendet:</span><span class="sxs-lookup"><span data-stu-id="b423d-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="b423d-144">Der vorangehende Code bestimmt, ob der aktuelle Benutzer Prinzipal über ein Geburtsdatum des Geburts Anspruchs verfügt, das von einem bekannten und vertrauenswürdigen Aussteller ausgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="b423d-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="b423d-145">Die Autorisierung kann nicht ausgeführt werden, wenn der Anspruch fehlt. in diesem Fall wird eine abgeschlossene Aufgabe zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="b423d-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="b423d-146">Wenn ein Anspruch vorhanden ist, wird das Alter des Benutzers berechnet.</span><span class="sxs-lookup"><span data-stu-id="b423d-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="b423d-147">Wenn der Benutzer das durch die Anforderung definierte mindestage erfüllt, wird die Autorisierung als erfolgreich eingestuft.</span><span class="sxs-lookup"><span data-stu-id="b423d-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="b423d-148">Wenn die Autorisierung erfolgreich ist, wird `context.Succeed` mit der Erfüllung der Anforderungen als einziger Parameter aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="b423d-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="b423d-149">Verwenden eines Handlers für mehrere Anforderungen</span><span class="sxs-lookup"><span data-stu-id="b423d-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="b423d-150">Im folgenden finden Sie ein Beispiel für eine 1: n-Beziehung, in der ein Berechtigungs Handler drei verschiedene Arten von Anforderungen verarbeiten kann:</span><span class="sxs-lookup"><span data-stu-id="b423d-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="b423d-151">Der vorangehende Code durchsucht [die&mdash;eine](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) Eigenschaft, die die Anforderungen enthält, die nicht als erfolgreich gekennzeichnet wurden.</span><span class="sxs-lookup"><span data-stu-id="b423d-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="b423d-152">Für eine `ReadPermission` Anforderung muss der Benutzer entweder ein Besitzer oder ein Sponsor sein, um auf die angeforderte Ressource zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="b423d-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="b423d-153">Im Fall einer `EditPermission` oder `DeletePermission` Anforderung muss er ein Besitzer sein, um auf die angeforderte Ressource zugreifen zu können.</span><span class="sxs-lookup"><span data-stu-id="b423d-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="b423d-154">Handlerregistrierung</span><span class="sxs-lookup"><span data-stu-id="b423d-154">Handler registration</span></span>

<span data-ttu-id="b423d-155">Handler werden während der Konfiguration in der Dienste-Sammlung registriert.</span><span class="sxs-lookup"><span data-stu-id="b423d-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="b423d-156">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="b423d-156">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="b423d-157">Im vorangehenden Code wird `MinimumAgeHandler` als Singleton registriert, indem `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="b423d-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="b423d-158">Handler können mithilfe einer beliebigen integrierten [Dienst Lebensdauer](xref:fundamentals/dependency-injection#service-lifetimes)registriert werden.</span><span class="sxs-lookup"><span data-stu-id="b423d-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="b423d-159">Was sollte ein Handler zurückgeben?</span><span class="sxs-lookup"><span data-stu-id="b423d-159">What should a handler return?</span></span>

<span data-ttu-id="b423d-160">Beachten Sie, dass die `Handle`-Methode im [handlerbeispiel](#security-authorization-handler-example) keinen Wert zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="b423d-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="b423d-161">Wie wird der Status "erfolgreich" oder "Fehler" angegeben?</span><span class="sxs-lookup"><span data-stu-id="b423d-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="b423d-162">Ein Handler gibt den Erfolg durch Aufrufen von `context.Succeed(IAuthorizationRequirement requirement)`an und übergibt die Anforderung, die erfolgreich überprüft wurde.</span><span class="sxs-lookup"><span data-stu-id="b423d-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="b423d-163">Ein Handler muss im Allgemeinen keine Fehler behandeln, da andere Handler für dieselbe Anforderung erfolgreich ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="b423d-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="b423d-164">Um einen Fehler zu gewährleisten, wenden Sie `context.Fail`an, selbst wenn andere Anforderungs Handler erfolgreich sind.</span><span class="sxs-lookup"><span data-stu-id="b423d-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="b423d-165">Wenn ein Handler `context.Succeed` oder `context.Fail`aufruft, werden alle anderen Handler weiterhin aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="b423d-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="b423d-166">Dies ermöglicht die Generierung von Nebeneffekten, wie z. b. die Protokollierung, die auch dann stattfindet, wenn ein anderer Handler eine Anforderung erfolgreich überprüft oder nicht erfüllt hat.</span><span class="sxs-lookup"><span data-stu-id="b423d-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="b423d-167">Wenn diese Eigenschaft auf `false`festgelegt ist, wird die Ausführung von Handlern durch die [invokehandlersafterfailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) -Eigenschaft (verfügbar in ASP.net Core 1,1 und höher) kurz beendet, wenn `context.Fail` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="b423d-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="b423d-168">`InvokeHandlersAfterFailure` standardmäßig `true`. in diesem Fall werden alle Handler aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="b423d-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="b423d-169">Autorisierungs Handler werden auch dann aufgerufen, wenn die Authentifizierung fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="b423d-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="b423d-170">Warum sollte ich mehrere Handler für eine Anforderung wünschen?</span><span class="sxs-lookup"><span data-stu-id="b423d-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="b423d-171">Wenn Sie evaluieren möchten, können Sie mehrere Handler **für eine einzelne** Anforderung implementieren.</span><span class="sxs-lookup"><span data-stu-id="b423d-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="b423d-172">Beispielsweise verfügt Microsoft über Türen, die nur mit Schlüsselkarten geöffnet werden.</span><span class="sxs-lookup"><span data-stu-id="b423d-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="b423d-173">Wenn Sie Ihre Schlüsselkarte zuhause lassen, druckt die Rezeption einen temporären Aufkleber und öffnet die Tür für Sie.</span><span class="sxs-lookup"><span data-stu-id="b423d-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="b423d-174">In diesem Szenario verfügen Sie über eine einzige Anforderung, *buildingentry*, aber mehrere Handler, die jeweils eine einzelne Anforderung untersuchen.</span><span class="sxs-lookup"><span data-stu-id="b423d-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="b423d-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="b423d-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="b423d-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="b423d-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="b423d-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="b423d-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="b423d-178">Stellen Sie sicher, dass beide Handler [registriert](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)sind.</span><span class="sxs-lookup"><span data-stu-id="b423d-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="b423d-179">Wenn einer der beiden Handler erfolgreich ist, wenn eine Richtlinie die `BuildingEntryRequirement`auswertet, ist die Richtlinien Auswertung erfolgreich.</span><span class="sxs-lookup"><span data-stu-id="b423d-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="b423d-180">Verwenden eines Func zum erfüllen einer Richtlinie</span><span class="sxs-lookup"><span data-stu-id="b423d-180">Using a func to fulfill a policy</span></span>

<span data-ttu-id="b423d-181">Es kann Situationen geben, in denen das erfüllen einer Richtlinie einfach im Code ausgedrückt werden kann.</span><span class="sxs-lookup"><span data-stu-id="b423d-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="b423d-182">Beim Konfigurieren Ihrer Richtlinie mit dem `RequireAssertion` Policy Builder ist es möglich, eine `Func<AuthorizationHandlerContext, bool>` bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="b423d-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="b423d-183">Beispielsweise könnte der vorherige `BadgeEntryHandler` wie folgt umgeschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="b423d-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="b423d-184">Zugreifen auf den MVC-Anforderungs Kontext in Handlern</span><span class="sxs-lookup"><span data-stu-id="b423d-184">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="b423d-185">Die `HandleRequirementAsync` Methode, die Sie in einem Autorisierungs Handler implementieren, verfügt über zwei Parameter: eine `AuthorizationHandlerContext` und die `TRequirement`, die Sie behandeln.</span><span class="sxs-lookup"><span data-stu-id="b423d-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="b423d-186">Frameworks wie MVC oder jabbr können ein beliebiges Objekt zur `Resource`-Eigenschaft des `AuthorizationHandlerContext` hinzufügen, um zusätzliche Informationen zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="b423d-186">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="b423d-187">MVC übergibt z. b. eine Instanz von [authorizationfiltercontext](/dotnet/api/?term=AuthorizationFilterContext) in der `Resource`-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="b423d-187">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="b423d-188">Diese Eigenschaft ermöglicht den Zugriff auf `HttpContext`, `RouteData`und alles andere, was von MVC und Razor Pages bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="b423d-188">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="b423d-189">Die Verwendung der `Resource`-Eigenschaft ist Framework-spezifisch.</span><span class="sxs-lookup"><span data-stu-id="b423d-189">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="b423d-190">Die Verwendung von Informationen in der `Resource`-Eigenschaft schränkt Ihre Autorisierungs Richtlinien auf bestimmte Frameworks ein.</span><span class="sxs-lookup"><span data-stu-id="b423d-190">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="b423d-191">Sie sollten die `Resource`-Eigenschaft mit dem Schlüsselwort `is` umwandeln und dann bestätigen, dass die Umwandlung erfolgreich war, um sicherzustellen, dass Ihr Code bei der Ausführung in anderen Frameworks nicht mit einem `InvalidCastException` stürzt:</span><span class="sxs-lookup"><span data-stu-id="b423d-191">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

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

<span data-ttu-id="b423d-192">Im Hintergrund werden bei der [rollenbasierten Autorisierung](xref:security/authorization/roles) und der [Anspruchs basierten Autorisierung](xref:security/authorization/claims) eine Anforderung, ein Anforderungs Handler und eine vorkonfigurierte Richtlinie verwendet.</span><span class="sxs-lookup"><span data-stu-id="b423d-192">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="b423d-193">Diese Bausteine unterstützen den Ausdruck der Autorisierungs Auswertungen im Code.</span><span class="sxs-lookup"><span data-stu-id="b423d-193">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="b423d-194">Das Ergebnis ist eine umfangreichere, wiederverwendbare, testbare Autorisierungs Struktur.</span><span class="sxs-lookup"><span data-stu-id="b423d-194">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="b423d-195">Eine Autorisierungs Richtlinie besteht aus einer oder mehreren Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="b423d-195">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="b423d-196">Sie wird als Teil der Autorisierungs Dienst Konfiguration in der `Startup.ConfigureServices`-Methode registriert:</span><span class="sxs-lookup"><span data-stu-id="b423d-196">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="b423d-197">Im vorherigen Beispiel wird eine Richtlinie "AtLeast21" erstellt.</span><span class="sxs-lookup"><span data-stu-id="b423d-197">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="b423d-198">Sie verfügt über eine einzige Anforderung&mdash;die eines minimalen Alters, das als Parameter für die Anforderung angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="b423d-198">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="b423d-199">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="b423d-199">IAuthorizationService</span></span> 

<span data-ttu-id="b423d-200">Der primäre Dienst, der bestimmt, ob die Autorisierung erfolgreich ist, <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span><span class="sxs-lookup"><span data-stu-id="b423d-200">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="b423d-201">Im vorangehenden Code werden die beiden Methoden des [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="b423d-201">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="b423d-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> ist ein markerdienst ohne Methoden und der Mechanismus für die Nachverfolgung, ob die Autorisierung erfolgreich war.</span><span class="sxs-lookup"><span data-stu-id="b423d-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="b423d-203">Jede <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> ist für die Überprüfung zuständig, ob die Anforderungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="b423d-203">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="b423d-204">Die <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext>-Klasse verwendet den Handler, um zu kennzeichnen, ob Anforderungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="b423d-204">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="b423d-205">Der folgende Code zeigt die vereinfachte (und mit Kommentaren versehene) Standard Implementierung des Autorisierungs Dienstanbieter:</span><span class="sxs-lookup"><span data-stu-id="b423d-205">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="b423d-206">Der folgende Code zeigt eine typische `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b423d-206">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="b423d-207">Verwenden Sie für die Autorisierung <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> oder `[Authorize(Policy = "Something")]`.</span><span class="sxs-lookup"><span data-stu-id="b423d-207">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="b423d-208">Anwenden von Richtlinien auf MVC-Controller</span><span class="sxs-lookup"><span data-stu-id="b423d-208">Applying policies to MVC controllers</span></span>

<span data-ttu-id="b423d-209">Wenn Sie Razor Pages verwenden, finden Sie weitere Informationen unter [Anwenden von Richtlinien auf Razor Pages](#applying-policies-to-razor-pages) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="b423d-209">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="b423d-210">Richtlinien werden mithilfe des `[Authorize]`-Attributs mit dem Richtlinien Namen auf Controller angewendet.</span><span class="sxs-lookup"><span data-stu-id="b423d-210">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="b423d-211">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="b423d-211">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="b423d-212">Anwenden von Richtlinien auf Razor Pages</span><span class="sxs-lookup"><span data-stu-id="b423d-212">Applying policies to Razor Pages</span></span>

<span data-ttu-id="b423d-213">Richtlinien werden auf Razor Pages mithilfe des `[Authorize]`-Attributs mit dem Richtlinien Namen angewendet.</span><span class="sxs-lookup"><span data-stu-id="b423d-213">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="b423d-214">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="b423d-214">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="b423d-215">Richtlinien können auch auf Razor Pages mithilfe einer [Autorisierungs Konvention](xref:security/authorization/razor-pages-authorization)angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="b423d-215">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="b423d-216">-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="b423d-216">Requirements</span></span>

<span data-ttu-id="b423d-217">Eine Autorisierungs Anforderung ist eine Auflistung von Daten Parametern, die eine Richtlinie verwenden kann, um den aktuellen Benutzer Prinzipal auszuwerten.</span><span class="sxs-lookup"><span data-stu-id="b423d-217">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="b423d-218">In unserer Richtlinie "AtLeast21" ist die Anforderung ein einzelner Parameter&mdash;dem minimal Alter.</span><span class="sxs-lookup"><span data-stu-id="b423d-218">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="b423d-219">Eine Anforderung implementiert [iauthorizationrequirements](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), bei dem es sich um eine leere markerschnittstelle handelt.</span><span class="sxs-lookup"><span data-stu-id="b423d-219">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="b423d-220">Eine parametrisierte minimale Alters Anforderung kann wie folgt implementiert werden:</span><span class="sxs-lookup"><span data-stu-id="b423d-220">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="b423d-221">Wenn eine Autorisierungs Richtlinie mehrere Autorisierungs Anforderungen enthält, müssen alle Anforderungen bestanden werden, damit die Richtlinien Auswertung erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="b423d-221">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="b423d-222">Anders ausgedrückt: mehrere Autorisierungs Anforderungen, die einer einzelnen Autorisierungs Richtlinie hinzugefügt werden, werden auf der Grundlage von **und** behandelt.</span><span class="sxs-lookup"><span data-stu-id="b423d-222">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="b423d-223">Eine Anforderung muss keine Daten oder Eigenschaften haben.</span><span class="sxs-lookup"><span data-stu-id="b423d-223">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="b423d-224">Autorisierungs Handler</span><span class="sxs-lookup"><span data-stu-id="b423d-224">Authorization handlers</span></span>

<span data-ttu-id="b423d-225">Ein Autorisierungs Handler ist dafür verantwortlich, die Eigenschaften einer Anforderung zu evaluieren.</span><span class="sxs-lookup"><span data-stu-id="b423d-225">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="b423d-226">Der Autorisierungs Handler wertet die Anforderungen anhand eines angegebenen [authorizationhandlercontext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) aus, um zu bestimmen, ob der Zugriff zulässig ist.</span><span class="sxs-lookup"><span data-stu-id="b423d-226">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="b423d-227">Eine Anforderung kann [mehrere Handler](#security-authorization-policies-based-multiple-handlers)aufweisen.</span><span class="sxs-lookup"><span data-stu-id="b423d-227">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="b423d-228">Ein Handler kann [authorizationhandler\<trequirement >](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)erben, wobei `TRequirement` die erforderliche Anforderung ist.</span><span class="sxs-lookup"><span data-stu-id="b423d-228">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="b423d-229">Alternativ kann ein Handler [iauthorizationhandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) implementieren, um mehr als eine Art von Anforderung zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="b423d-229">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="b423d-230">Verwenden eines Handlers für eine Anforderung</span><span class="sxs-lookup"><span data-stu-id="b423d-230">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="b423d-231">Im folgenden finden Sie ein Beispiel für eine 1:1-Beziehung, bei der ein minimaler Alters Handler eine einzige Anforderung verwendet:</span><span class="sxs-lookup"><span data-stu-id="b423d-231">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="b423d-232">Der vorangehende Code bestimmt, ob der aktuelle Benutzer Prinzipal über ein Geburtsdatum des Geburts Anspruchs verfügt, das von einem bekannten und vertrauenswürdigen Aussteller ausgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="b423d-232">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="b423d-233">Die Autorisierung kann nicht ausgeführt werden, wenn der Anspruch fehlt. in diesem Fall wird eine abgeschlossene Aufgabe zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="b423d-233">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="b423d-234">Wenn ein Anspruch vorhanden ist, wird das Alter des Benutzers berechnet.</span><span class="sxs-lookup"><span data-stu-id="b423d-234">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="b423d-235">Wenn der Benutzer das durch die Anforderung definierte mindestage erfüllt, wird die Autorisierung als erfolgreich eingestuft.</span><span class="sxs-lookup"><span data-stu-id="b423d-235">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="b423d-236">Wenn die Autorisierung erfolgreich ist, wird `context.Succeed` mit der Erfüllung der Anforderungen als einziger Parameter aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="b423d-236">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="b423d-237">Verwenden eines Handlers für mehrere Anforderungen</span><span class="sxs-lookup"><span data-stu-id="b423d-237">Use a handler for multiple requirements</span></span>

<span data-ttu-id="b423d-238">Im folgenden finden Sie ein Beispiel für eine 1: n-Beziehung, in der ein Berechtigungs Handler drei verschiedene Arten von Anforderungen verarbeiten kann:</span><span class="sxs-lookup"><span data-stu-id="b423d-238">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="b423d-239">Der vorangehende Code durchsucht [die&mdash;eine](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements) Eigenschaft, die die Anforderungen enthält, die nicht als erfolgreich gekennzeichnet wurden.</span><span class="sxs-lookup"><span data-stu-id="b423d-239">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="b423d-240">Für eine `ReadPermission` Anforderung muss der Benutzer entweder ein Besitzer oder ein Sponsor sein, um auf die angeforderte Ressource zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="b423d-240">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="b423d-241">Im Fall einer `EditPermission` oder `DeletePermission` Anforderung muss er ein Besitzer sein, um auf die angeforderte Ressource zugreifen zu können.</span><span class="sxs-lookup"><span data-stu-id="b423d-241">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="b423d-242">Handlerregistrierung</span><span class="sxs-lookup"><span data-stu-id="b423d-242">Handler registration</span></span>

<span data-ttu-id="b423d-243">Handler werden während der Konfiguration in der Dienste-Sammlung registriert.</span><span class="sxs-lookup"><span data-stu-id="b423d-243">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="b423d-244">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="b423d-244">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="b423d-245">Im vorangehenden Code wird `MinimumAgeHandler` als Singleton registriert, indem `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="b423d-245">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="b423d-246">Handler können mithilfe einer beliebigen integrierten [Dienst Lebensdauer](xref:fundamentals/dependency-injection#service-lifetimes)registriert werden.</span><span class="sxs-lookup"><span data-stu-id="b423d-246">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="b423d-247">Was sollte ein Handler zurückgeben?</span><span class="sxs-lookup"><span data-stu-id="b423d-247">What should a handler return?</span></span>

<span data-ttu-id="b423d-248">Beachten Sie, dass die `Handle`-Methode im [handlerbeispiel](#security-authorization-handler-example) keinen Wert zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="b423d-248">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="b423d-249">Wie wird der Status "erfolgreich" oder "Fehler" angegeben?</span><span class="sxs-lookup"><span data-stu-id="b423d-249">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="b423d-250">Ein Handler gibt den Erfolg durch Aufrufen von `context.Succeed(IAuthorizationRequirement requirement)`an und übergibt die Anforderung, die erfolgreich überprüft wurde.</span><span class="sxs-lookup"><span data-stu-id="b423d-250">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="b423d-251">Ein Handler muss im Allgemeinen keine Fehler behandeln, da andere Handler für dieselbe Anforderung erfolgreich ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="b423d-251">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="b423d-252">Um einen Fehler zu gewährleisten, wenden Sie `context.Fail`an, selbst wenn andere Anforderungs Handler erfolgreich sind.</span><span class="sxs-lookup"><span data-stu-id="b423d-252">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="b423d-253">Wenn ein Handler `context.Succeed` oder `context.Fail`aufruft, werden alle anderen Handler weiterhin aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="b423d-253">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="b423d-254">Dies ermöglicht die Generierung von Nebeneffekten, wie z. b. die Protokollierung, die auch dann stattfindet, wenn ein anderer Handler eine Anforderung erfolgreich überprüft oder nicht erfüllt hat.</span><span class="sxs-lookup"><span data-stu-id="b423d-254">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="b423d-255">Wenn diese Eigenschaft auf `false`festgelegt ist, wird die Ausführung von Handlern durch die [invokehandlersafterfailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) -Eigenschaft (verfügbar in ASP.net Core 1,1 und höher) kurz beendet, wenn `context.Fail` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="b423d-255">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="b423d-256">`InvokeHandlersAfterFailure` standardmäßig `true`. in diesem Fall werden alle Handler aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="b423d-256">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="b423d-257">Autorisierungs Handler werden auch dann aufgerufen, wenn die Authentifizierung fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="b423d-257">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="b423d-258">Warum sollte ich mehrere Handler für eine Anforderung wünschen?</span><span class="sxs-lookup"><span data-stu-id="b423d-258">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="b423d-259">Wenn Sie evaluieren möchten, können Sie mehrere Handler **für eine einzelne** Anforderung implementieren.</span><span class="sxs-lookup"><span data-stu-id="b423d-259">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="b423d-260">Beispielsweise verfügt Microsoft über Türen, die nur mit Schlüsselkarten geöffnet werden.</span><span class="sxs-lookup"><span data-stu-id="b423d-260">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="b423d-261">Wenn Sie Ihre Schlüsselkarte zuhause lassen, druckt die Rezeption einen temporären Aufkleber und öffnet die Tür für Sie.</span><span class="sxs-lookup"><span data-stu-id="b423d-261">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="b423d-262">In diesem Szenario verfügen Sie über eine einzige Anforderung, *buildingentry*, aber mehrere Handler, die jeweils eine einzelne Anforderung untersuchen.</span><span class="sxs-lookup"><span data-stu-id="b423d-262">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="b423d-263">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="b423d-263">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="b423d-264">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="b423d-264">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="b423d-265">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="b423d-265">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="b423d-266">Stellen Sie sicher, dass beide Handler [registriert](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)sind.</span><span class="sxs-lookup"><span data-stu-id="b423d-266">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="b423d-267">Wenn einer der beiden Handler erfolgreich ist, wenn eine Richtlinie die `BuildingEntryRequirement`auswertet, ist die Richtlinien Auswertung erfolgreich.</span><span class="sxs-lookup"><span data-stu-id="b423d-267">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="b423d-268">Verwenden eines Func zum erfüllen einer Richtlinie</span><span class="sxs-lookup"><span data-stu-id="b423d-268">Using a func to fulfill a policy</span></span>

<span data-ttu-id="b423d-269">Es kann Situationen geben, in denen das erfüllen einer Richtlinie einfach im Code ausgedrückt werden kann.</span><span class="sxs-lookup"><span data-stu-id="b423d-269">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="b423d-270">Beim Konfigurieren Ihrer Richtlinie mit dem `RequireAssertion` Policy Builder ist es möglich, eine `Func<AuthorizationHandlerContext, bool>` bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="b423d-270">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="b423d-271">Beispielsweise könnte der vorherige `BadgeEntryHandler` wie folgt umgeschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="b423d-271">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="b423d-272">Zugreifen auf den MVC-Anforderungs Kontext in Handlern</span><span class="sxs-lookup"><span data-stu-id="b423d-272">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="b423d-273">Die `HandleRequirementAsync` Methode, die Sie in einem Autorisierungs Handler implementieren, verfügt über zwei Parameter: eine `AuthorizationHandlerContext` und die `TRequirement`, die Sie behandeln.</span><span class="sxs-lookup"><span data-stu-id="b423d-273">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="b423d-274">Frameworks wie MVC oder jabbr können ein beliebiges Objekt zur `Resource`-Eigenschaft des `AuthorizationHandlerContext` hinzufügen, um zusätzliche Informationen zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="b423d-274">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="b423d-275">MVC übergibt z. b. eine Instanz von [authorizationfiltercontext](/dotnet/api/?term=AuthorizationFilterContext) in der `Resource`-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="b423d-275">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="b423d-276">Diese Eigenschaft ermöglicht den Zugriff auf `HttpContext`, `RouteData`und alles andere, was von MVC und Razor Pages bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="b423d-276">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="b423d-277">Die Verwendung der `Resource`-Eigenschaft ist Framework-spezifisch.</span><span class="sxs-lookup"><span data-stu-id="b423d-277">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="b423d-278">Die Verwendung von Informationen in der `Resource`-Eigenschaft schränkt Ihre Autorisierungs Richtlinien auf bestimmte Frameworks ein.</span><span class="sxs-lookup"><span data-stu-id="b423d-278">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="b423d-279">Sie sollten die `Resource`-Eigenschaft mit dem Schlüsselwort `is` umwandeln und dann bestätigen, dass die Umwandlung erfolgreich war, um sicherzustellen, dass Ihr Code bei der Ausführung in anderen Frameworks nicht mit einem `InvalidCastException` stürzt:</span><span class="sxs-lookup"><span data-stu-id="b423d-279">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end