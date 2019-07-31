---
title: Richtlinien basierte Autorisierung in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie Autorisierungs Richtlinien Handler zum Erzwingen von Autorisierungs Anforderungen in einer ASP.net Core-app erstellen und verwenden.
ms.author: riande
ms.custom: mvc
ms.date: 04/05/2019
uid: security/authorization/policies
ms.openlocfilehash: 60625944d4ba31da6b98bdf947991088dc75ed87
ms.sourcegitcommit: 7001657c00358b082734ba4273693b9b3ed35d2a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68669967"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="b5383-103">Richtlinien basierte Autorisierung in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="b5383-103">Policy-based authorization in ASP.NET Core</span></span>

<span data-ttu-id="b5383-104">Im Hintergrund werden bei der [rollenbasierten Autorisierung](xref:security/authorization/roles) und der [Anspruchs basierten Autorisierung](xref:security/authorization/claims) eine Anforderung, ein Anforderungs Handler und eine vorkonfigurierte Richtlinie verwendet.</span><span class="sxs-lookup"><span data-stu-id="b5383-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="b5383-105">Diese Bausteine unterstützen den Ausdruck der Autorisierungs Auswertungen im Code.</span><span class="sxs-lookup"><span data-stu-id="b5383-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="b5383-106">Das Ergebnis ist eine umfangreichere, wiederverwendbare, testbare Autorisierungs Struktur.</span><span class="sxs-lookup"><span data-stu-id="b5383-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="b5383-107">Eine Autorisierungs Richtlinie besteht aus einer oder mehreren Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="b5383-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="b5383-108">Sie wird als Teil der Autorisierungs Dienst Konfiguration in der `Startup.ConfigureServices` -Methode registriert:</span><span class="sxs-lookup"><span data-stu-id="b5383-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="b5383-109">Im vorherigen Beispiel wird eine Richtlinie "AtLeast21" erstellt.</span><span class="sxs-lookup"><span data-stu-id="b5383-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="b5383-110">Sie verfügt über eine einzige&mdash;Anforderung, die ein minimal Alter ist, das als Parameter für die Anforderung angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="b5383-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="b5383-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="b5383-111">IAuthorizationService</span></span> 

<span data-ttu-id="b5383-112">Der primäre Dienst, der bestimmt, ob die Autorisierung erfolgreich war, ist <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span><span class="sxs-lookup"><span data-stu-id="b5383-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="b5383-113">Im vorangehenden Code werden die beiden Methoden des [IAuthorizationService](https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="b5383-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="b5383-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>ist ein markerdienst ohne Methoden und der Mechanismus für die Überwachung, ob die Autorisierung erfolgreich war.</span><span class="sxs-lookup"><span data-stu-id="b5383-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="b5383-115">Jeder <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> ist für die Überprüfung zuständig, ob die Anforderungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="b5383-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/aspnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

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

<span data-ttu-id="b5383-116">Mit <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> der-Klasse wird der Handler verwendet, um zu kennzeichnen, ob Anforderungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="b5383-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="b5383-117">Der folgende Code zeigt die vereinfachte (und mit Kommentaren versehene) Standard Implementierung des Autorisierungs Dienstanbieter:</span><span class="sxs-lookup"><span data-stu-id="b5383-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="b5383-118">Der folgende Code zeigt ein typisches `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b5383-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="b5383-119">Verwenden <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> Sie `[Authorize(Policy = "Something")]` oder für die Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="b5383-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="b5383-120">Anwenden von Richtlinien auf MVC-Controller</span><span class="sxs-lookup"><span data-stu-id="b5383-120">Applying policies to MVC controllers</span></span>

<span data-ttu-id="b5383-121">Wenn Sie Razor Pages verwenden, finden Sie weitere Informationen unter [Anwenden von Richtlinien auf Razor Pages](#applying-policies-to-razor-pages) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="b5383-121">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="b5383-122">Richtlinien werden mithilfe des `[Authorize]` -Attributs mit dem Richtlinien Namen auf Controller angewendet.</span><span class="sxs-lookup"><span data-stu-id="b5383-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="b5383-123">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="b5383-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="b5383-124">Anwenden von Richtlinien auf Razor Pages</span><span class="sxs-lookup"><span data-stu-id="b5383-124">Applying policies to Razor Pages</span></span>

<span data-ttu-id="b5383-125">Richtlinien werden mithilfe des `[Authorize]` -Attributs mit dem Richtlinien Namen auf Razor Pages angewendet.</span><span class="sxs-lookup"><span data-stu-id="b5383-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="b5383-126">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="b5383-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="b5383-127">Richtlinien können auch auf Razor Pages mithilfe einer [Autorisierungs Konvention](xref:security/authorization/razor-pages-authorization)angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="b5383-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="b5383-128">Anforderungen</span><span class="sxs-lookup"><span data-stu-id="b5383-128">Requirements</span></span>

<span data-ttu-id="b5383-129">Eine Autorisierungs Anforderung ist eine Auflistung von Daten Parametern, die eine Richtlinie verwenden kann, um den aktuellen Benutzer Prinzipal auszuwerten.</span><span class="sxs-lookup"><span data-stu-id="b5383-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="b5383-130">In unserer Richtlinie "AtLeast21" ist die Anforderung ein einzelner Parameter&mdash;das minimale Alter.</span><span class="sxs-lookup"><span data-stu-id="b5383-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="b5383-131">Eine Anforderung implementiert [iauthorizationrequirements](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), bei dem es sich um eine leere markerschnittstelle handelt.</span><span class="sxs-lookup"><span data-stu-id="b5383-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="b5383-132">Eine parametrisierte minimale Alters Anforderung kann wie folgt implementiert werden:</span><span class="sxs-lookup"><span data-stu-id="b5383-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="b5383-133">Wenn eine Autorisierungs Richtlinie mehrere Autorisierungs Anforderungen enthält, müssen alle Anforderungen bestanden werden, damit die Richtlinien Auswertung erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="b5383-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="b5383-134">Anders ausgedrückt: mehrere Autorisierungs Anforderungen, die einer einzelnen Autorisierungs Richtlinie hinzugefügt werden, werden auf der Grundlage von **und** behandelt.</span><span class="sxs-lookup"><span data-stu-id="b5383-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="b5383-135">Eine Anforderung muss keine Daten oder Eigenschaften haben.</span><span class="sxs-lookup"><span data-stu-id="b5383-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="b5383-136">Autorisierungs Handler</span><span class="sxs-lookup"><span data-stu-id="b5383-136">Authorization handlers</span></span>

<span data-ttu-id="b5383-137">Ein Autorisierungs Handler ist dafür verantwortlich, die Eigenschaften einer Anforderung zu evaluieren.</span><span class="sxs-lookup"><span data-stu-id="b5383-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="b5383-138">Der Autorisierungs Handler wertet die Anforderungen anhand eines angegebenen [authorizationhandlercontext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) aus, um zu bestimmen, ob der Zugriff zulässig ist.</span><span class="sxs-lookup"><span data-stu-id="b5383-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="b5383-139">Eine Anforderung kann [mehrere Handler](#security-authorization-policies-based-multiple-handlers)aufweisen.</span><span class="sxs-lookup"><span data-stu-id="b5383-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="b5383-140">Ein Handler kann die [authorizationhandler\<-trequirement](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)-> `TRequirement` erben, wobei die zu behandelnde Anforderung ist.</span><span class="sxs-lookup"><span data-stu-id="b5383-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="b5383-141">Alternativ kann ein Handler [iauthorizationhandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) implementieren, um mehr als eine Art von Anforderung zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="b5383-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="b5383-142">Verwenden eines Handlers für eine Anforderung</span><span class="sxs-lookup"><span data-stu-id="b5383-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="b5383-143">Im folgenden finden Sie ein Beispiel für eine 1:1-Beziehung, bei der ein minimaler Alters Handler eine einzige Anforderung verwendet:</span><span class="sxs-lookup"><span data-stu-id="b5383-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="b5383-144">Der vorangehende Code bestimmt, ob der aktuelle Benutzer Prinzipal über ein Geburtsdatum des Geburts Anspruchs verfügt, das von einem bekannten und vertrauenswürdigen Aussteller ausgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="b5383-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="b5383-145">Die Autorisierung kann nicht ausgeführt werden, wenn der Anspruch fehlt. in diesem Fall wird eine abgeschlossene Aufgabe zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="b5383-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="b5383-146">Wenn ein Anspruch vorhanden ist, wird das Alter des Benutzers berechnet.</span><span class="sxs-lookup"><span data-stu-id="b5383-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="b5383-147">Wenn der Benutzer das durch die Anforderung definierte mindestage erfüllt, wird die Autorisierung als erfolgreich eingestuft.</span><span class="sxs-lookup"><span data-stu-id="b5383-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="b5383-148">Wenn die Autorisierung erfolgreich `context.Succeed` ist, wird mit der Erfüllung der Anforderungen als einziger Parameter aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="b5383-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="b5383-149">Verwenden eines Handlers für mehrere Anforderungen</span><span class="sxs-lookup"><span data-stu-id="b5383-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="b5383-150">Im folgenden finden Sie ein Beispiel für eine 1: n-Beziehung, in der ein Berechtigungs Handler drei verschiedene Arten von Anforderungen verarbeiten kann:</span><span class="sxs-lookup"><span data-stu-id="b5383-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="b5383-151">Der vorangehende Code durchsucht die " [pdingrequirequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;"-Eigenschaft mit den Anforderungen, die nicht als erfolgreich gekennzeichnet sind.</span><span class="sxs-lookup"><span data-stu-id="b5383-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="b5383-152">Für eine `ReadPermission` Anforderung muss der Benutzer entweder ein Besitzer oder ein Sponsor sein, um auf die angeforderte Ressource zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="b5383-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="b5383-153">Bei einer `EditPermission` -oder `DeletePermission` -Anforderung muss er ein Besitzer sein, um auf die angeforderte Ressource zugreifen zu können.</span><span class="sxs-lookup"><span data-stu-id="b5383-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="b5383-154">Handlerregistrierung</span><span class="sxs-lookup"><span data-stu-id="b5383-154">Handler registration</span></span>

<span data-ttu-id="b5383-155">Handler werden während der Konfiguration in der Dienste-Sammlung registriert.</span><span class="sxs-lookup"><span data-stu-id="b5383-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="b5383-156">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="b5383-156">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="b5383-157">Der vorangehende Code `MinimumAgeHandler` wird als Singleton registriert, `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`indem aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="b5383-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="b5383-158">Handler können mithilfe einer beliebigen integrierten [Dienst Lebensdauer](xref:fundamentals/dependency-injection#service-lifetimes)registriert werden.</span><span class="sxs-lookup"><span data-stu-id="b5383-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="b5383-159">Was sollte ein Handler zurückgeben?</span><span class="sxs-lookup"><span data-stu-id="b5383-159">What should a handler return?</span></span>

<span data-ttu-id="b5383-160">Beachten Sie, `Handle` dass die-Methode im [handlerbeispiel](#security-authorization-handler-example) keinen Wert zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="b5383-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="b5383-161">Wie wird der Status "erfolgreich" oder "Fehler" angegeben?</span><span class="sxs-lookup"><span data-stu-id="b5383-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="b5383-162">Ein Handler gibt den Erfolg durch `context.Succeed(IAuthorizationRequirement requirement)`Aufrufen von an und übergibt die Anforderung, die erfolgreich überprüft wurde.</span><span class="sxs-lookup"><span data-stu-id="b5383-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="b5383-163">Ein Handler muss im Allgemeinen keine Fehler behandeln, da andere Handler für dieselbe Anforderung erfolgreich ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="b5383-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="b5383-164">Um einen Fehler zu gewährleisten, müssen Sie, auch wenn andere `context.Fail`Anforderungs Handler erfolgreich sind, den Befehl</span><span class="sxs-lookup"><span data-stu-id="b5383-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="b5383-165">Wenn ein Handler oder `context.Succeed` `context.Fail`aufruft, werden alle anderen Handler immer noch aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="b5383-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="b5383-166">Dies ermöglicht die Generierung von Nebeneffekten, wie z. b. die Protokollierung, die auch dann stattfindet, wenn ein anderer Handler eine Anforderung erfolgreich überprüft oder nicht erfüllt hat.</span><span class="sxs-lookup"><span data-stu-id="b5383-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="b5383-167">Wenn diese Eigenschaft `false`auf festgelegt ist, wird die Ausführung von Handlern durch die [invokehandlersafterfailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) -Eigenschaft (verfügbar in ASP.net Core 1,1 `context.Fail` und höher) kurz beendet, wenn aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="b5383-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="b5383-168">`InvokeHandlersAfterFailure`der Standardwert ist. in diesem Fall werden alle Handler aufgerufen. `true`</span><span class="sxs-lookup"><span data-stu-id="b5383-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="b5383-169">Autorisierungs Handler werden auch dann aufgerufen, wenn die Authentifizierung fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="b5383-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="b5383-170">Warum sollte ich mehrere Handler für eine Anforderung wünschen?</span><span class="sxs-lookup"><span data-stu-id="b5383-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="b5383-171">Wenn Sie evaluieren möchten, können Sie mehrere Handler für eine einzelne Anforderung implementieren.</span><span class="sxs-lookup"><span data-stu-id="b5383-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="b5383-172">Beispielsweise verfügt Microsoft über Türen, die nur mit Schlüsselkarten geöffnet werden.</span><span class="sxs-lookup"><span data-stu-id="b5383-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="b5383-173">Wenn Sie Ihre Schlüsselkarte zuhause lassen, druckt die Rezeption einen temporären Aufkleber und öffnet die Tür für Sie.</span><span class="sxs-lookup"><span data-stu-id="b5383-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="b5383-174">In diesem Szenario verfügen Sie über eine einzige Anforderung, *buildingentry*, aber mehrere Handler, die jeweils eine einzelne Anforderung untersuchen.</span><span class="sxs-lookup"><span data-stu-id="b5383-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="b5383-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="b5383-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="b5383-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="b5383-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="b5383-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="b5383-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="b5383-178">Stellen Sie sicher, dass beide Handler [registriert](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)sind.</span><span class="sxs-lookup"><span data-stu-id="b5383-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="b5383-179">Wenn einer der beiden Handler erfolgreich ist, wenn `BuildingEntryRequirement`eine Richtlinie den auswertet, ist die Richtlinien Auswertung erfolgreich.</span><span class="sxs-lookup"><span data-stu-id="b5383-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="b5383-180">Verwenden eines Func zum erfüllen einer Richtlinie</span><span class="sxs-lookup"><span data-stu-id="b5383-180">Using a func to fulfill a policy</span></span>

<span data-ttu-id="b5383-181">Es kann Situationen geben, in denen das erfüllen einer Richtlinie einfach im Code ausgedrückt werden kann.</span><span class="sxs-lookup"><span data-stu-id="b5383-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="b5383-182">Beim Konfigurieren Ihrer Richtlinie mit `Func<AuthorizationHandlerContext, bool>` dem `RequireAssertion` Richtlinien Generator ist es möglich, eine bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="b5383-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="b5383-183">Die vorherige `BadgeEntryHandler` könnte z. b. wie folgt umgeschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="b5383-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="b5383-184">Zugreifen auf den MVC-Anforderungs Kontext in Handlern</span><span class="sxs-lookup"><span data-stu-id="b5383-184">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="b5383-185">Die `HandleRequirementAsync` Methode, die Sie in einem Autorisierungs Handler implementieren, verfügt `AuthorizationHandlerContext` über zwei `TRequirement` Parameter: einen und den, den Sie behandeln.</span><span class="sxs-lookup"><span data-stu-id="b5383-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="b5383-186">Frameworks wie z. b. MVC oder jabbr können ein beliebiges `Resource` Objekt zur- `AuthorizationHandlerContext` Eigenschaft hinzufügen, um zusätzliche Informationen zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="b5383-186">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="b5383-187">MVC übergibt z. b. eine Instanz von [authorizationfiltercontext](/dotnet/api/?term=AuthorizationFilterContext) in `Resource` der-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="b5383-187">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="b5383-188">Diese Eigenschaft ermöglicht den Zugriff `HttpContext`auf `RouteData`, und alles andere, was von MVC und Razor Pages bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="b5383-188">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="b5383-189">Die Verwendung `Resource` der-Eigenschaft ist Framework-spezifisch.</span><span class="sxs-lookup"><span data-stu-id="b5383-189">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="b5383-190">Die Verwendung von Informationen `Resource` in der-Eigenschaft schränkt Ihre Autorisierungs Richtlinien auf bestimmte Frameworks ein.</span><span class="sxs-lookup"><span data-stu-id="b5383-190">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="b5383-191">Sie sollten die `Resource` -Eigenschaft mit dem `is` -Schlüsselwort umwandeln und dann bestätigen, dass die Umwandlung erfolgreich war, um sicherzustellen `InvalidCastException` , dass der Code nicht mit einem stürzt</span><span class="sxs-lookup"><span data-stu-id="b5383-191">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```
