---
title: Richtlinien basierte Autorisierung in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie Autorisierungs Richtlinien Handler zum Erzwingen von Autorisierungs Anforderungen in einer ASP.net Core-app erstellen und verwenden.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/policies
ms.openlocfilehash: 533bddc9c4499dad99cfdb3089045ea10aed4548
ms.sourcegitcommit: 4437f4c149f1ef6c28796dcfaa2863b4c088169c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/19/2020
ms.locfileid: "85074152"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="c756d-103">Richtlinien basierte Autorisierung in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="c756d-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c756d-104">Im Hintergrund werden bei der [rollenbasierten Autorisierung](xref:security/authorization/roles) und der [Anspruchs basierten Autorisierung](xref:security/authorization/claims) eine Anforderung, ein Anforderungs Handler und eine vorkonfigurierte Richtlinie verwendet.</span><span class="sxs-lookup"><span data-stu-id="c756d-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="c756d-105">Diese Bausteine unterstützen den Ausdruck der Autorisierungs Auswertungen im Code.</span><span class="sxs-lookup"><span data-stu-id="c756d-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="c756d-106">Das Ergebnis ist eine umfangreichere, wiederverwendbare, testbare Autorisierungs Struktur.</span><span class="sxs-lookup"><span data-stu-id="c756d-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="c756d-107">Eine Autorisierungs Richtlinie besteht aus einer oder mehreren Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="c756d-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="c756d-108">Sie wird als Teil der Autorisierungs Dienst Konfiguration in der- `Startup.ConfigureServices` Methode registriert:</span><span class="sxs-lookup"><span data-stu-id="c756d-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="c756d-109">Im vorherigen Beispiel wird eine Richtlinie "AtLeast21" erstellt.</span><span class="sxs-lookup"><span data-stu-id="c756d-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="c756d-110">Sie verfügt über eine einzige Anforderung, die ein &mdash; minimal Alter ist, das als Parameter für die Anforderung angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="c756d-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="c756d-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="c756d-111">IAuthorizationService</span></span> 

<span data-ttu-id="c756d-112">Der primäre Dienst, der bestimmt, ob die Autorisierung erfolgreich war, ist <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :</span><span class="sxs-lookup"><span data-stu-id="c756d-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="c756d-113">Im vorangehenden Code werden die beiden Methoden des [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="c756d-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="c756d-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>ist ein markerdienst ohne Methoden und der Mechanismus für die Überwachung, ob die Autorisierung erfolgreich war.</span><span class="sxs-lookup"><span data-stu-id="c756d-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="c756d-115">Jeder <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> ist für die Überprüfung zuständig, ob die Anforderungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="c756d-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="c756d-116">Mit der- <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> Klasse wird der Handler verwendet, um zu kennzeichnen, ob Anforderungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="c756d-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="c756d-117">Der folgende Code zeigt die vereinfachte (und mit Kommentaren versehene) Standard Implementierung des Autorisierungs Dienstanbieter:</span><span class="sxs-lookup"><span data-stu-id="c756d-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="c756d-118">Der folgende Code zeigt ein typisches `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c756d-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="c756d-119">Verwenden Sie <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> oder `[Authorize(Policy = "Something")]` für die Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="c756d-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="c756d-120">Anwenden von Richtlinien auf MVC-Controller</span><span class="sxs-lookup"><span data-stu-id="c756d-120">Apply policies to MVC controllers</span></span>

<span data-ttu-id="c756d-121">Wenn Sie Seiten verwenden Razor , finden Sie weitere Informationen unter [Anwenden von Richtlinien auf Razor Seiten](#apply-policies-to-razor-pages) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="c756d-121">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="c756d-122">Richtlinien werden mithilfe des- `[Authorize]` Attributs mit dem Richtlinien Namen auf Controller angewendet.</span><span class="sxs-lookup"><span data-stu-id="c756d-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="c756d-123">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c756d-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-razor-pages"></a><span data-ttu-id="c756d-124">Anwenden von Richtlinien auf Razor Seiten</span><span class="sxs-lookup"><span data-stu-id="c756d-124">Apply policies to Razor Pages</span></span>

<span data-ttu-id="c756d-125">Richtlinien werden Razor mithilfe des- `[Authorize]` Attributs mit dem Richtlinien Namen auf Seiten angewendet.</span><span class="sxs-lookup"><span data-stu-id="c756d-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="c756d-126">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c756d-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="c756d-127">Richtlinien können auch Razor mithilfe einer [Autorisierungs Konvention](xref:security/authorization/razor-pages-authorization)auf Seiten angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="c756d-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="c756d-128">Requirements (Anforderungen)</span><span class="sxs-lookup"><span data-stu-id="c756d-128">Requirements</span></span>

<span data-ttu-id="c756d-129">Eine Autorisierungs Anforderung ist eine Auflistung von Daten Parametern, die eine Richtlinie verwenden kann, um den aktuellen Benutzer Prinzipal auszuwerten.</span><span class="sxs-lookup"><span data-stu-id="c756d-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="c756d-130">In unserer Richtlinie "AtLeast21" ist die Anforderung ein einzelner Parameter &mdash; das minimale Alter.</span><span class="sxs-lookup"><span data-stu-id="c756d-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="c756d-131">Eine Anforderung implementiert [iauthorizationrequirements](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), bei dem es sich um eine leere markerschnittstelle handelt.</span><span class="sxs-lookup"><span data-stu-id="c756d-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="c756d-132">Eine parametrisierte minimale Alters Anforderung kann wie folgt implementiert werden:</span><span class="sxs-lookup"><span data-stu-id="c756d-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="c756d-133">Wenn eine Autorisierungs Richtlinie mehrere Autorisierungs Anforderungen enthält, müssen alle Anforderungen bestanden werden, damit die Richtlinien Auswertung erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="c756d-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="c756d-134">Anders ausgedrückt: mehrere Autorisierungs Anforderungen, die einer einzelnen Autorisierungs Richtlinie hinzugefügt werden, werden auf der Grundlage von **und** behandelt.</span><span class="sxs-lookup"><span data-stu-id="c756d-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="c756d-135">Eine Anforderung muss keine Daten oder Eigenschaften haben.</span><span class="sxs-lookup"><span data-stu-id="c756d-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="c756d-136">Autorisierungs Handler</span><span class="sxs-lookup"><span data-stu-id="c756d-136">Authorization handlers</span></span>

<span data-ttu-id="c756d-137">Ein Autorisierungs Handler ist dafür verantwortlich, die Eigenschaften einer Anforderung zu evaluieren.</span><span class="sxs-lookup"><span data-stu-id="c756d-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="c756d-138">Der Autorisierungs Handler wertet die Anforderungen anhand eines angegebenen [authorizationhandlercontext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) aus, um zu bestimmen, ob der Zugriff zulässig ist.</span><span class="sxs-lookup"><span data-stu-id="c756d-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="c756d-139">Eine Anforderung kann [mehrere Handler](#security-authorization-policies-based-multiple-handlers)aufweisen.</span><span class="sxs-lookup"><span data-stu-id="c756d-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="c756d-140">Ein Handler kann den [authorizationhandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)erben, wobei `TRequirement` die zu behandelnde Anforderung ist.</span><span class="sxs-lookup"><span data-stu-id="c756d-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="c756d-141">Alternativ kann ein Handler [iauthorizationhandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) implementieren, um mehr als eine Art von Anforderung zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="c756d-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="c756d-142">Verwenden eines Handlers für eine Anforderung</span><span class="sxs-lookup"><span data-stu-id="c756d-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="c756d-143">Im folgenden finden Sie ein Beispiel für eine 1:1-Beziehung, bei der ein minimaler Alters Handler eine einzige Anforderung verwendet:</span><span class="sxs-lookup"><span data-stu-id="c756d-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="c756d-144">Der vorangehende Code bestimmt, ob der aktuelle Benutzer Prinzipal über ein Geburtsdatum des Geburts Anspruchs verfügt, das von einem bekannten und vertrauenswürdigen Aussteller ausgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="c756d-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="c756d-145">Die Autorisierung kann nicht ausgeführt werden, wenn der Anspruch fehlt. in diesem Fall wird eine abgeschlossene Aufgabe zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="c756d-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="c756d-146">Wenn ein Anspruch vorhanden ist, wird das Alter des Benutzers berechnet.</span><span class="sxs-lookup"><span data-stu-id="c756d-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="c756d-147">Wenn der Benutzer das durch die Anforderung definierte mindestage erfüllt, wird die Autorisierung als erfolgreich eingestuft.</span><span class="sxs-lookup"><span data-stu-id="c756d-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="c756d-148">Wenn die Autorisierung erfolgreich ist, `context.Succeed` wird mit der Erfüllung der Anforderungen als einziger Parameter aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="c756d-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="c756d-149">Verwenden eines Handlers für mehrere Anforderungen</span><span class="sxs-lookup"><span data-stu-id="c756d-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="c756d-150">Im folgenden finden Sie ein Beispiel für eine 1: n-Beziehung, in der ein Berechtigungs Handler drei verschiedene Arten von Anforderungen verarbeiten kann:</span><span class="sxs-lookup"><span data-stu-id="c756d-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="c756d-151">Der vorangehende Code durchsucht die " [pdingrequirequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)"- &mdash; Eigenschaft mit den Anforderungen, die nicht als erfolgreich gekennzeichnet sind.</span><span class="sxs-lookup"><span data-stu-id="c756d-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="c756d-152">Für eine `ReadPermission` Anforderung muss der Benutzer entweder ein Besitzer oder ein Sponsor sein, um auf die angeforderte Ressource zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="c756d-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="c756d-153">Bei einer-oder- `EditPermission` `DeletePermission` Anforderung muss er ein Besitzer sein, um auf die angeforderte Ressource zugreifen zu können.</span><span class="sxs-lookup"><span data-stu-id="c756d-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="c756d-154">Handlerregistrierung</span><span class="sxs-lookup"><span data-stu-id="c756d-154">Handler registration</span></span>

<span data-ttu-id="c756d-155">Handler werden während der Konfiguration in der Dienste-Sammlung registriert.</span><span class="sxs-lookup"><span data-stu-id="c756d-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="c756d-156">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c756d-156">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="c756d-157">Der vorangehende Code `MinimumAgeHandler` wird als Singleton registriert, indem aufgerufen wird `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="c756d-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="c756d-158">Handler können mithilfe einer beliebigen integrierten [Dienst Lebensdauer](xref:fundamentals/dependency-injection#service-lifetimes)registriert werden.</span><span class="sxs-lookup"><span data-stu-id="c756d-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="c756d-159">Was sollte ein Handler zurückgeben?</span><span class="sxs-lookup"><span data-stu-id="c756d-159">What should a handler return?</span></span>

<span data-ttu-id="c756d-160">Beachten Sie, dass die- `Handle` Methode im [handlerbeispiel](#security-authorization-handler-example) keinen Wert zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="c756d-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="c756d-161">Wie wird der Status "erfolgreich" oder "Fehler" angegeben?</span><span class="sxs-lookup"><span data-stu-id="c756d-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="c756d-162">Ein Handler gibt den Erfolg durch Aufrufen von an `context.Succeed(IAuthorizationRequirement requirement)` und übergibt die Anforderung, die erfolgreich überprüft wurde.</span><span class="sxs-lookup"><span data-stu-id="c756d-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="c756d-163">Ein Handler muss im Allgemeinen keine Fehler behandeln, da andere Handler für dieselbe Anforderung erfolgreich ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="c756d-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="c756d-164">Um einen Fehler zu gewährleisten, müssen Sie, auch wenn andere Anforderungs Handler erfolgreich sind, den Befehl `context.Fail`</span><span class="sxs-lookup"><span data-stu-id="c756d-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="c756d-165">Wenn ein Handler `context.Succeed` oder aufruft `context.Fail` , werden alle anderen Handler immer noch aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="c756d-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="c756d-166">Dies ermöglicht die Generierung von Nebeneffekten, wie z. b. die Protokollierung, die auch dann stattfindet, wenn ein anderer Handler eine Anforderung erfolgreich überprüft oder nicht erfüllt hat.</span><span class="sxs-lookup"><span data-stu-id="c756d-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="c756d-167">Wenn diese Eigenschaft auf festgelegt ist `false` , wird die Ausführung von Handlern durch die [invokehandlersafterfailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) -Eigenschaft (verfügbar in ASP.net Core 1,1 und höher) kurz beendet, wenn `context.Fail` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="c756d-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="c756d-168">`InvokeHandlersAfterFailure`der Standardwert `true` ist. in diesem Fall werden alle Handler aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="c756d-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="c756d-169">Autorisierungs Handler werden auch dann aufgerufen, wenn die Authentifizierung fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="c756d-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="c756d-170">Warum sollte ich mehrere Handler für eine Anforderung wünschen?</span><span class="sxs-lookup"><span data-stu-id="c756d-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="c756d-171">Wenn Sie evaluieren möchten, können Sie mehrere Handler **für eine einzelne** Anforderung implementieren.</span><span class="sxs-lookup"><span data-stu-id="c756d-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="c756d-172">Beispielsweise verfügt Microsoft über Türen, die nur mit Schlüsselkarten geöffnet werden.</span><span class="sxs-lookup"><span data-stu-id="c756d-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="c756d-173">Wenn Sie Ihre Schlüsselkarte zuhause lassen, druckt die Rezeption einen temporären Aufkleber und öffnet die Tür für Sie.</span><span class="sxs-lookup"><span data-stu-id="c756d-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="c756d-174">In diesem Szenario verfügen Sie über eine einzige Anforderung, *buildingentry*, aber mehrere Handler, die jeweils eine einzelne Anforderung untersuchen.</span><span class="sxs-lookup"><span data-stu-id="c756d-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="c756d-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="c756d-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="c756d-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="c756d-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="c756d-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="c756d-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="c756d-178">Stellen Sie sicher, dass beide Handler [registriert](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)sind.</span><span class="sxs-lookup"><span data-stu-id="c756d-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="c756d-179">Wenn einer der beiden Handler erfolgreich ist, wenn eine Richtlinie den `BuildingEntryRequirement` auswertet, ist die Richtlinien Auswertung erfolgreich.</span><span class="sxs-lookup"><span data-stu-id="c756d-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="c756d-180">Verwenden eines Func zum erfüllen einer Richtlinie</span><span class="sxs-lookup"><span data-stu-id="c756d-180">Use a func to fulfill a policy</span></span>

<span data-ttu-id="c756d-181">Es kann Situationen geben, in denen das erfüllen einer Richtlinie einfach im Code ausgedrückt werden kann.</span><span class="sxs-lookup"><span data-stu-id="c756d-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="c756d-182">`Func<AuthorizationHandlerContext, bool>`Beim Konfigurieren Ihrer Richtlinie mit dem Richtlinien Generator ist es möglich, eine bereitzustellen `RequireAssertion` .</span><span class="sxs-lookup"><span data-stu-id="c756d-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="c756d-183">Die vorherige könnte z. b. `BadgeEntryHandler` wie folgt umgeschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="c756d-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="c756d-184">Zugreifen auf den MVC-Anforderungs Kontext in Handlern</span><span class="sxs-lookup"><span data-stu-id="c756d-184">Access MVC request context in handlers</span></span>

<span data-ttu-id="c756d-185">Die `HandleRequirementAsync` Methode, die Sie in einem Autorisierungs Handler implementieren, verfügt über zwei Parameter: einen `AuthorizationHandlerContext` und den, den `TRequirement` Sie behandeln.</span><span class="sxs-lookup"><span data-stu-id="c756d-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="c756d-186">Frameworks wie MVC oder SignalR können jedes beliebige Objekt der- `Resource` Eigenschaft im hinzufügen `AuthorizationHandlerContext` , um zusätzliche Informationen zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="c756d-186">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="c756d-187">Wenn Sie das Endpunkt Routing verwenden, wird die Autorisierung in der Regel von der Autorisierungs Middleware verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="c756d-187">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="c756d-188">In diesem Fall ist die `Resource` Eigenschaft eine Instanz von <xref:Microsoft.AspNetCore.Http.Endpoint> .</span><span class="sxs-lookup"><span data-stu-id="c756d-188">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="c756d-189">Der Endpunkt kann verwendet werden, um die zugrunde liegende Ressource zu überprüfen, an die Sie weiterleiten.</span><span class="sxs-lookup"><span data-stu-id="c756d-189">The endpoint can be used to probe the underlying resource to which you're routing.</span></span> <span data-ttu-id="c756d-190">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c756d-190">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="c756d-191">Der-Endpunkt bietet keinen Zugriff auf die aktuelle `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="c756d-191">The endpoint doesn't provide access to the current `HttpContext`.</span></span> <span data-ttu-id="c756d-192">Wenn Sie das Endpunkt Routing verwenden, verwenden `IHttpContextAcessor` Sie für den Zugriff `HttpContext` innerhalb eines Autorisierungs Handlers.</span><span class="sxs-lookup"><span data-stu-id="c756d-192">When using endpoint routing, use `IHttpContextAcessor` to access `HttpContext` inside of an authorization handler.</span></span> <span data-ttu-id="c756d-193">Weitere Informationen finden Sie unter [Verwenden von HttpContext aus benutzerdefinierten Komponenten](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span><span class="sxs-lookup"><span data-stu-id="c756d-193">For more information, see [Use HttpContext from custom components](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span></span>

<span data-ttu-id="c756d-194">Mit herkömmlichem Routing oder wenn die Autorisierung im Rahmen des Autorisierungs Filters von MVC erfolgt, ist der Wert von `Resource` eine <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> Instanz von.</span><span class="sxs-lookup"><span data-stu-id="c756d-194">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="c756d-195">Diese Eigenschaft ermöglicht den Zugriff auf `HttpContext` , `RouteData` und alles andere, was von MVC und Seiten bereitgestellt wird Razor .</span><span class="sxs-lookup"><span data-stu-id="c756d-195">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="c756d-196">Die Verwendung der- `Resource` Eigenschaft ist Framework-spezifisch.</span><span class="sxs-lookup"><span data-stu-id="c756d-196">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="c756d-197">Die Verwendung von Informationen in der- `Resource` Eigenschaft schränkt Ihre Autorisierungs Richtlinien auf bestimmte Frameworks ein.</span><span class="sxs-lookup"><span data-stu-id="c756d-197">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="c756d-198">Sie sollten die `Resource` -Eigenschaft mit dem `is` -Schlüsselwort umwandeln und dann bestätigen, dass die Umwandlung erfolgreich war, um sicherzustellen, dass der Code nicht mit einem stürzt `InvalidCastException`</span><span class="sxs-lookup"><span data-stu-id="c756d-198">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

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

<span data-ttu-id="c756d-199">Im Hintergrund werden bei der [rollenbasierten Autorisierung](xref:security/authorization/roles) und der [Anspruchs basierten Autorisierung](xref:security/authorization/claims) eine Anforderung, ein Anforderungs Handler und eine vorkonfigurierte Richtlinie verwendet.</span><span class="sxs-lookup"><span data-stu-id="c756d-199">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="c756d-200">Diese Bausteine unterstützen den Ausdruck der Autorisierungs Auswertungen im Code.</span><span class="sxs-lookup"><span data-stu-id="c756d-200">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="c756d-201">Das Ergebnis ist eine umfangreichere, wiederverwendbare, testbare Autorisierungs Struktur.</span><span class="sxs-lookup"><span data-stu-id="c756d-201">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="c756d-202">Eine Autorisierungs Richtlinie besteht aus einer oder mehreren Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="c756d-202">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="c756d-203">Sie wird als Teil der Autorisierungs Dienst Konfiguration in der- `Startup.ConfigureServices` Methode registriert:</span><span class="sxs-lookup"><span data-stu-id="c756d-203">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="c756d-204">Im vorherigen Beispiel wird eine Richtlinie "AtLeast21" erstellt.</span><span class="sxs-lookup"><span data-stu-id="c756d-204">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="c756d-205">Sie verfügt über eine einzige Anforderung, die ein &mdash; minimal Alter ist, das als Parameter für die Anforderung angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="c756d-205">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="c756d-206">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="c756d-206">IAuthorizationService</span></span> 

<span data-ttu-id="c756d-207">Der primäre Dienst, der bestimmt, ob die Autorisierung erfolgreich war, ist <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :</span><span class="sxs-lookup"><span data-stu-id="c756d-207">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="c756d-208">Im vorangehenden Code werden die beiden Methoden des [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="c756d-208">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="c756d-209"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>ist ein markerdienst ohne Methoden und der Mechanismus für die Überwachung, ob die Autorisierung erfolgreich war.</span><span class="sxs-lookup"><span data-stu-id="c756d-209"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="c756d-210">Jeder <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> ist für die Überprüfung zuständig, ob die Anforderungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="c756d-210">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="c756d-211">Mit der- <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> Klasse wird der Handler verwendet, um zu kennzeichnen, ob Anforderungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="c756d-211">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="c756d-212">Der folgende Code zeigt die vereinfachte (und mit Kommentaren versehene) Standard Implementierung des Autorisierungs Dienstanbieter:</span><span class="sxs-lookup"><span data-stu-id="c756d-212">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="c756d-213">Der folgende Code zeigt ein typisches `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="c756d-213">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="c756d-214">Verwenden Sie <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> oder `[Authorize(Policy = "Something")]` für die Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="c756d-214">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="c756d-215">Anwenden von Richtlinien auf MVC-Controller</span><span class="sxs-lookup"><span data-stu-id="c756d-215">Apply policies to MVC controllers</span></span>

<span data-ttu-id="c756d-216">Wenn Sie Seiten verwenden Razor , finden Sie weitere Informationen unter [Anwenden von Richtlinien auf Razor Seiten](#apply-policies-to-razor-pages) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="c756d-216">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="c756d-217">Richtlinien werden mithilfe des- `[Authorize]` Attributs mit dem Richtlinien Namen auf Controller angewendet.</span><span class="sxs-lookup"><span data-stu-id="c756d-217">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="c756d-218">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c756d-218">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-razor-pages"></a><span data-ttu-id="c756d-219">Anwenden von Richtlinien auf Razor Seiten</span><span class="sxs-lookup"><span data-stu-id="c756d-219">Apply policies to Razor Pages</span></span>

<span data-ttu-id="c756d-220">Richtlinien werden Razor mithilfe des- `[Authorize]` Attributs mit dem Richtlinien Namen auf Seiten angewendet.</span><span class="sxs-lookup"><span data-stu-id="c756d-220">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="c756d-221">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c756d-221">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="c756d-222">Richtlinien können auch Razor mithilfe einer [Autorisierungs Konvention](xref:security/authorization/razor-pages-authorization)auf Seiten angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="c756d-222">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="c756d-223">Requirements (Anforderungen)</span><span class="sxs-lookup"><span data-stu-id="c756d-223">Requirements</span></span>

<span data-ttu-id="c756d-224">Eine Autorisierungs Anforderung ist eine Auflistung von Daten Parametern, die eine Richtlinie verwenden kann, um den aktuellen Benutzer Prinzipal auszuwerten.</span><span class="sxs-lookup"><span data-stu-id="c756d-224">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="c756d-225">In unserer Richtlinie "AtLeast21" ist die Anforderung ein einzelner Parameter &mdash; das minimale Alter.</span><span class="sxs-lookup"><span data-stu-id="c756d-225">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="c756d-226">Eine Anforderung implementiert [iauthorizationrequirements](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), bei dem es sich um eine leere markerschnittstelle handelt.</span><span class="sxs-lookup"><span data-stu-id="c756d-226">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="c756d-227">Eine parametrisierte minimale Alters Anforderung kann wie folgt implementiert werden:</span><span class="sxs-lookup"><span data-stu-id="c756d-227">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="c756d-228">Wenn eine Autorisierungs Richtlinie mehrere Autorisierungs Anforderungen enthält, müssen alle Anforderungen bestanden werden, damit die Richtlinien Auswertung erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="c756d-228">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="c756d-229">Anders ausgedrückt: mehrere Autorisierungs Anforderungen, die einer einzelnen Autorisierungs Richtlinie hinzugefügt werden, werden auf der Grundlage von **und** behandelt.</span><span class="sxs-lookup"><span data-stu-id="c756d-229">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="c756d-230">Eine Anforderung muss keine Daten oder Eigenschaften haben.</span><span class="sxs-lookup"><span data-stu-id="c756d-230">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="c756d-231">Autorisierungs Handler</span><span class="sxs-lookup"><span data-stu-id="c756d-231">Authorization handlers</span></span>

<span data-ttu-id="c756d-232">Ein Autorisierungs Handler ist dafür verantwortlich, die Eigenschaften einer Anforderung zu evaluieren.</span><span class="sxs-lookup"><span data-stu-id="c756d-232">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="c756d-233">Der Autorisierungs Handler wertet die Anforderungen anhand eines angegebenen [authorizationhandlercontext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) aus, um zu bestimmen, ob der Zugriff zulässig ist.</span><span class="sxs-lookup"><span data-stu-id="c756d-233">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="c756d-234">Eine Anforderung kann [mehrere Handler](#security-authorization-policies-based-multiple-handlers)aufweisen.</span><span class="sxs-lookup"><span data-stu-id="c756d-234">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="c756d-235">Ein Handler kann den [authorizationhandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)erben, wobei `TRequirement` die zu behandelnde Anforderung ist.</span><span class="sxs-lookup"><span data-stu-id="c756d-235">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="c756d-236">Alternativ kann ein Handler [iauthorizationhandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) implementieren, um mehr als eine Art von Anforderung zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="c756d-236">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="c756d-237">Verwenden eines Handlers für eine Anforderung</span><span class="sxs-lookup"><span data-stu-id="c756d-237">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="c756d-238">Im folgenden finden Sie ein Beispiel für eine 1:1-Beziehung, bei der ein minimaler Alters Handler eine einzige Anforderung verwendet:</span><span class="sxs-lookup"><span data-stu-id="c756d-238">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="c756d-239">Der vorangehende Code bestimmt, ob der aktuelle Benutzer Prinzipal über ein Geburtsdatum des Geburts Anspruchs verfügt, das von einem bekannten und vertrauenswürdigen Aussteller ausgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="c756d-239">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="c756d-240">Die Autorisierung kann nicht ausgeführt werden, wenn der Anspruch fehlt. in diesem Fall wird eine abgeschlossene Aufgabe zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="c756d-240">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="c756d-241">Wenn ein Anspruch vorhanden ist, wird das Alter des Benutzers berechnet.</span><span class="sxs-lookup"><span data-stu-id="c756d-241">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="c756d-242">Wenn der Benutzer das durch die Anforderung definierte mindestage erfüllt, wird die Autorisierung als erfolgreich eingestuft.</span><span class="sxs-lookup"><span data-stu-id="c756d-242">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="c756d-243">Wenn die Autorisierung erfolgreich ist, `context.Succeed` wird mit der Erfüllung der Anforderungen als einziger Parameter aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="c756d-243">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="c756d-244">Verwenden eines Handlers für mehrere Anforderungen</span><span class="sxs-lookup"><span data-stu-id="c756d-244">Use a handler for multiple requirements</span></span>

<span data-ttu-id="c756d-245">Im folgenden finden Sie ein Beispiel für eine 1: n-Beziehung, in der ein Berechtigungs Handler drei verschiedene Arten von Anforderungen verarbeiten kann:</span><span class="sxs-lookup"><span data-stu-id="c756d-245">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="c756d-246">Der vorangehende Code durchsucht die " [pdingrequirequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)"- &mdash; Eigenschaft mit den Anforderungen, die nicht als erfolgreich gekennzeichnet sind.</span><span class="sxs-lookup"><span data-stu-id="c756d-246">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="c756d-247">Für eine `ReadPermission` Anforderung muss der Benutzer entweder ein Besitzer oder ein Sponsor sein, um auf die angeforderte Ressource zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="c756d-247">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="c756d-248">Bei einer-oder- `EditPermission` `DeletePermission` Anforderung muss er ein Besitzer sein, um auf die angeforderte Ressource zugreifen zu können.</span><span class="sxs-lookup"><span data-stu-id="c756d-248">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="c756d-249">Handlerregistrierung</span><span class="sxs-lookup"><span data-stu-id="c756d-249">Handler registration</span></span>

<span data-ttu-id="c756d-250">Handler werden während der Konfiguration in der Dienste-Sammlung registriert.</span><span class="sxs-lookup"><span data-stu-id="c756d-250">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="c756d-251">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c756d-251">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="c756d-252">Der vorangehende Code `MinimumAgeHandler` wird als Singleton registriert, indem aufgerufen wird `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="c756d-252">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="c756d-253">Handler können mithilfe einer beliebigen integrierten [Dienst Lebensdauer](xref:fundamentals/dependency-injection#service-lifetimes)registriert werden.</span><span class="sxs-lookup"><span data-stu-id="c756d-253">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="c756d-254">Was sollte ein Handler zurückgeben?</span><span class="sxs-lookup"><span data-stu-id="c756d-254">What should a handler return?</span></span>

<span data-ttu-id="c756d-255">Beachten Sie, dass die- `Handle` Methode im [handlerbeispiel](#security-authorization-handler-example) keinen Wert zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="c756d-255">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="c756d-256">Wie wird der Status "erfolgreich" oder "Fehler" angegeben?</span><span class="sxs-lookup"><span data-stu-id="c756d-256">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="c756d-257">Ein Handler gibt den Erfolg durch Aufrufen von an `context.Succeed(IAuthorizationRequirement requirement)` und übergibt die Anforderung, die erfolgreich überprüft wurde.</span><span class="sxs-lookup"><span data-stu-id="c756d-257">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="c756d-258">Ein Handler muss im Allgemeinen keine Fehler behandeln, da andere Handler für dieselbe Anforderung erfolgreich ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="c756d-258">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="c756d-259">Um einen Fehler zu gewährleisten, müssen Sie, auch wenn andere Anforderungs Handler erfolgreich sind, den Befehl `context.Fail`</span><span class="sxs-lookup"><span data-stu-id="c756d-259">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="c756d-260">Wenn ein Handler `context.Succeed` oder aufruft `context.Fail` , werden alle anderen Handler immer noch aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="c756d-260">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="c756d-261">Dies ermöglicht die Generierung von Nebeneffekten, wie z. b. die Protokollierung, die auch dann stattfindet, wenn ein anderer Handler eine Anforderung erfolgreich überprüft oder nicht erfüllt hat.</span><span class="sxs-lookup"><span data-stu-id="c756d-261">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="c756d-262">Wenn diese Eigenschaft auf festgelegt ist `false` , wird die Ausführung von Handlern durch die [invokehandlersafterfailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) -Eigenschaft (verfügbar in ASP.net Core 1,1 und höher) kurz beendet, wenn `context.Fail` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="c756d-262">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="c756d-263">`InvokeHandlersAfterFailure`der Standardwert `true` ist. in diesem Fall werden alle Handler aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="c756d-263">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="c756d-264">Autorisierungs Handler werden auch dann aufgerufen, wenn die Authentifizierung fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="c756d-264">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="c756d-265">Warum sollte ich mehrere Handler für eine Anforderung wünschen?</span><span class="sxs-lookup"><span data-stu-id="c756d-265">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="c756d-266">Wenn Sie evaluieren möchten, können Sie mehrere Handler **für eine einzelne** Anforderung implementieren.</span><span class="sxs-lookup"><span data-stu-id="c756d-266">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="c756d-267">Beispielsweise verfügt Microsoft über Türen, die nur mit Schlüsselkarten geöffnet werden.</span><span class="sxs-lookup"><span data-stu-id="c756d-267">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="c756d-268">Wenn Sie Ihre Schlüsselkarte zuhause lassen, druckt die Rezeption einen temporären Aufkleber und öffnet die Tür für Sie.</span><span class="sxs-lookup"><span data-stu-id="c756d-268">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="c756d-269">In diesem Szenario verfügen Sie über eine einzige Anforderung, *buildingentry*, aber mehrere Handler, die jeweils eine einzelne Anforderung untersuchen.</span><span class="sxs-lookup"><span data-stu-id="c756d-269">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="c756d-270">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="c756d-270">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="c756d-271">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="c756d-271">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="c756d-272">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="c756d-272">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="c756d-273">Stellen Sie sicher, dass beide Handler [registriert](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)sind.</span><span class="sxs-lookup"><span data-stu-id="c756d-273">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="c756d-274">Wenn einer der beiden Handler erfolgreich ist, wenn eine Richtlinie den `BuildingEntryRequirement` auswertet, ist die Richtlinien Auswertung erfolgreich.</span><span class="sxs-lookup"><span data-stu-id="c756d-274">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="c756d-275">Verwenden eines Func zum erfüllen einer Richtlinie</span><span class="sxs-lookup"><span data-stu-id="c756d-275">Use a func to fulfill a policy</span></span>

<span data-ttu-id="c756d-276">Es kann Situationen geben, in denen das erfüllen einer Richtlinie einfach im Code ausgedrückt werden kann.</span><span class="sxs-lookup"><span data-stu-id="c756d-276">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="c756d-277">`Func<AuthorizationHandlerContext, bool>`Beim Konfigurieren Ihrer Richtlinie mit dem Richtlinien Generator ist es möglich, eine bereitzustellen `RequireAssertion` .</span><span class="sxs-lookup"><span data-stu-id="c756d-277">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="c756d-278">Die vorherige könnte z. b. `BadgeEntryHandler` wie folgt umgeschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="c756d-278">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="c756d-279">Zugreifen auf den MVC-Anforderungs Kontext in Handlern</span><span class="sxs-lookup"><span data-stu-id="c756d-279">Access MVC request context in handlers</span></span>

<span data-ttu-id="c756d-280">Die `HandleRequirementAsync` Methode, die Sie in einem Autorisierungs Handler implementieren, verfügt über zwei Parameter: einen `AuthorizationHandlerContext` und den, den `TRequirement` Sie behandeln.</span><span class="sxs-lookup"><span data-stu-id="c756d-280">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="c756d-281">Frameworks wie MVC oder SignalR können jedes beliebige Objekt der- `Resource` Eigenschaft im hinzufügen `AuthorizationHandlerContext` , um zusätzliche Informationen zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="c756d-281">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="c756d-282">MVC übergibt z. b. eine Instanz von [authorizationfiltercontext](/dotnet/api/?term=AuthorizationFilterContext) in der- `Resource` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="c756d-282">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="c756d-283">Diese Eigenschaft ermöglicht den Zugriff auf `HttpContext` , `RouteData` und alles andere, was von MVC und Seiten bereitgestellt wird Razor .</span><span class="sxs-lookup"><span data-stu-id="c756d-283">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="c756d-284">Die Verwendung der- `Resource` Eigenschaft ist Framework-spezifisch.</span><span class="sxs-lookup"><span data-stu-id="c756d-284">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="c756d-285">Die Verwendung von Informationen in der- `Resource` Eigenschaft schränkt Ihre Autorisierungs Richtlinien auf bestimmte Frameworks ein.</span><span class="sxs-lookup"><span data-stu-id="c756d-285">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="c756d-286">Sie sollten die `Resource` -Eigenschaft mit dem `is` -Schlüsselwort umwandeln und dann bestätigen, dass die Umwandlung erfolgreich war, um sicherzustellen, dass der Code nicht mit einem stürzt `InvalidCastException`</span><span class="sxs-lookup"><span data-stu-id="c756d-286">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
