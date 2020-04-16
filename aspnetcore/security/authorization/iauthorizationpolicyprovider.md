---
title: Benutzerdefinierte Autorisierungsrichtlinienanbieter in ASP.NET Core
author: mjrousos
description: Erfahren Sie, wie Sie einen benutzerdefinierten IAuthorizationPolicyProvider in einer ASP.NET Core-App verwenden, um Autorisierungsrichtlinien dynamisch zu generieren.
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 2a8b189cc9f17529a962a1f9642c7bb199d5781b
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440921"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a><span data-ttu-id="fefb9-103">Benutzerdefinierte Autorisierungsrichtlinienanbieter, die IAuthorizationPolicyProvider in ASP.NET Core verwenden</span><span class="sxs-lookup"><span data-stu-id="fefb9-103">Custom Authorization Policy Providers using IAuthorizationPolicyProvider in ASP.NET Core</span></span> 

<span data-ttu-id="fefb9-104">Von [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="fefb9-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="fefb9-105">In der Regel werden Richtlinien bei der `AuthorizationOptions.AddPolicy` Verwendung [von richtlinienbasierter Autorisierung](xref:security/authorization/policies)durch Aufruf als Teil der Autorisierungsdienstkonfiguration registriert.</span><span class="sxs-lookup"><span data-stu-id="fefb9-105">Typically when using [policy-based authorization](xref:security/authorization/policies), policies are registered by calling `AuthorizationOptions.AddPolicy` as part of authorization service configuration.</span></span> <span data-ttu-id="fefb9-106">In einigen Szenarien ist es möglicherweise nicht möglich (oder wünschenswert), alle Autorisierungsrichtlinien auf diese Weise zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="fefb9-106">In some scenarios, it may not be possible (or desirable) to register all authorization policies in this way.</span></span> <span data-ttu-id="fefb9-107">In diesen Fällen können Sie `IAuthorizationPolicyProvider` mithilfe einer benutzerdefinierten Verwendung steuern, wie Autorisierungsrichtlinien bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="fefb9-107">In those cases, you can use a custom `IAuthorizationPolicyProvider` to control how authorization policies are supplied.</span></span>

<span data-ttu-id="fefb9-108">Beispiele für Szenarien, in denen ein benutzerdefinierter [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) nützlich sein kann:</span><span class="sxs-lookup"><span data-stu-id="fefb9-108">Examples of scenarios where a custom [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) may be useful include:</span></span>

* <span data-ttu-id="fefb9-109">Verwenden eines externen Dienstes zum Bereitstellen einer Richtlinienbewertung.</span><span class="sxs-lookup"><span data-stu-id="fefb9-109">Using an external service to provide policy evaluation.</span></span>
* <span data-ttu-id="fefb9-110">Verwenden einer großen Auswahl an Richtlinien (z. B. für unterschiedliche Raumnummern oder Altersgruppen), ist `AuthorizationOptions.AddPolicy` es daher nicht sinnvoll, jede einzelne Autorisierungsrichtlinie mit einem Anruf hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="fefb9-110">Using a large range of policies (for different room numbers or ages, for example), so it doesn't make sense to add each individual authorization policy with an `AuthorizationOptions.AddPolicy` call.</span></span>
* <span data-ttu-id="fefb9-111">Erstellen von Richtlinien zur Laufzeit basierend auf Informationen in einer externen Datenquelle (z. B. einer Datenbank) oder dynamisches Bestimmen von Autorisierungsanforderungen über einen anderen Mechanismus.</span><span class="sxs-lookup"><span data-stu-id="fefb9-111">Creating policies at runtime based on information in an external data source (like a database) or determining authorization requirements dynamically through another mechanism.</span></span>

<span data-ttu-id="fefb9-112">[Beispielcode](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) aus dem [AspNetCore GitHub-Repository](https://github.com/dotnet/AspNetCore)anzeigen oder herunterladen.</span><span class="sxs-lookup"><span data-stu-id="fefb9-112">[View or download sample code](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) from the [AspNetCore GitHub repository](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="fefb9-113">Laden Sie die ZIP-Datei dotnet/AspNetCore Repository herunter.</span><span class="sxs-lookup"><span data-stu-id="fefb9-113">Download the dotnet/AspNetCore repository ZIP file.</span></span> <span data-ttu-id="fefb9-114">Entpacken Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="fefb9-114">Unzip the file.</span></span> <span data-ttu-id="fefb9-115">Navigieren Sie zum Projektordner *src/Security/samples/CustomPolicyProvider.*</span><span class="sxs-lookup"><span data-stu-id="fefb9-115">Navigate to the *src/Security/samples/CustomPolicyProvider* project folder.</span></span>

## <a name="customize-policy-retrieval"></a><span data-ttu-id="fefb9-116">Anpassen des Richtlinienabrufs</span><span class="sxs-lookup"><span data-stu-id="fefb9-116">Customize policy retrieval</span></span>

<span data-ttu-id="fefb9-117">ASP.NET Core-Apps verwenden `IAuthorizationPolicyProvider` eine Implementierung der Schnittstelle, um Autorisierungsrichtlinien abzurufen.</span><span class="sxs-lookup"><span data-stu-id="fefb9-117">ASP.NET Core apps use an implementation of the `IAuthorizationPolicyProvider` interface to retrieve authorization policies.</span></span> <span data-ttu-id="fefb9-118">Standardmäßig wird [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) registriert und verwendet.</span><span class="sxs-lookup"><span data-stu-id="fefb9-118">By default, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) is registered and used.</span></span> <span data-ttu-id="fefb9-119">`DefaultAuthorizationPolicyProvider`gibt Richtlinien `AuthorizationOptions` aus dem `IServiceCollection.AddAuthorization` in einem Anruf bereitgestellten zurück.</span><span class="sxs-lookup"><span data-stu-id="fefb9-119">`DefaultAuthorizationPolicyProvider` returns policies from the `AuthorizationOptions` provided in an `IServiceCollection.AddAuthorization` call.</span></span>

<span data-ttu-id="fefb9-120">Passen Sie dieses Verhalten `IAuthorizationPolicyProvider` an, indem Sie eine andere Implementierung im [Abhängigkeitsinjektionscontainer](xref:fundamentals/dependency-injection) der App registrieren.</span><span class="sxs-lookup"><span data-stu-id="fefb9-120">Customize this behavior by registering a different `IAuthorizationPolicyProvider` implementation in the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> 

<span data-ttu-id="fefb9-121">Die `IAuthorizationPolicyProvider` Schnittstelle enthält drei APIs:</span><span class="sxs-lookup"><span data-stu-id="fefb9-121">The `IAuthorizationPolicyProvider` interface contains three APIs:</span></span>

* <span data-ttu-id="fefb9-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) gibt eine Autorisierungsrichtlinie für einen bestimmten Namen zurück.</span><span class="sxs-lookup"><span data-stu-id="fefb9-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) returns an authorization policy for a given name.</span></span>
* <span data-ttu-id="fefb9-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) gibt die Standardautorisierungsrichtlinie `[Authorize]` zurück (die Richtlinie, die für Attribute ohne angegebene Richtlinie verwendet wird).</span><span class="sxs-lookup"><span data-stu-id="fefb9-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) returns the default authorization policy (the policy used for `[Authorize]` attributes without a policy specified).</span></span> 
* <span data-ttu-id="fefb9-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) gibt die Fallbackautorisierungsrichtlinie zurück (die Richtlinie, die von der Autorisierungsmiddleware verwendet wird, wenn keine Richtlinie angegeben ist).</span><span class="sxs-lookup"><span data-stu-id="fefb9-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) returns the fallback authorization policy (the policy used by the Authorization Middleware when no policy is specified).</span></span> 

<span data-ttu-id="fefb9-125">Durch Implementieren dieser APIs können Sie anpassen, wie Autorisierungsrichtlinien bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="fefb9-125">By implementing these APIs, you can customize how authorization policies are provided.</span></span>

## <a name="parameterized-authorize-attribute-example"></a><span data-ttu-id="fefb9-126">Parameterisiertes Authorize-Attributbeispiel</span><span class="sxs-lookup"><span data-stu-id="fefb9-126">Parameterized authorize attribute example</span></span>

<span data-ttu-id="fefb9-127">Ein Szenario, in `IAuthorizationPolicyProvider` dem `[Authorize]` nützlich ist, ist das Aktivieren benutzerdefinierter Attribute, deren Anforderungen von einem Parameter abhängen.</span><span class="sxs-lookup"><span data-stu-id="fefb9-127">One scenario where `IAuthorizationPolicyProvider` is useful is enabling custom `[Authorize]` attributes whose requirements depend on a parameter.</span></span> <span data-ttu-id="fefb9-128">In der [richtlinienbasierten Autorisierungsdokumentation](xref:security/authorization/policies) wurde beispielsweise eine altersbasierte Richtlinie ("AtLeast21") als Beispiel verwendet.</span><span class="sxs-lookup"><span data-stu-id="fefb9-128">For example, in [policy-based authorization](xref:security/authorization/policies) documentation, an age-based (“AtLeast21”) policy was used as a sample.</span></span> <span data-ttu-id="fefb9-129">Wenn Benutzern unterschiedlicher Controlleraktionen in einer App Benutzern *unterschiedlichen* Alters zur Verfügung gestellt werden sollten, kann es nützlich sein, viele verschiedene altersbasierte Richtlinien zu haben.</span><span class="sxs-lookup"><span data-stu-id="fefb9-129">If different controller actions in an app should be made available to users of *different* ages, it might be useful to have many different age-based policies.</span></span> <span data-ttu-id="fefb9-130">Anstatt alle verschiedenen altersbasierten Richtlinien zu registrieren, die `AuthorizationOptions`die Anwendung in benötigt, `IAuthorizationPolicyProvider`können Sie die Richtlinien dynamisch mit einer benutzerdefinierten generieren.</span><span class="sxs-lookup"><span data-stu-id="fefb9-130">Instead of registering all the different age-based policies that the application will need in `AuthorizationOptions`, you can generate the policies dynamically with a custom `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="fefb9-131">Um die Verwendung der Richtlinien zu vereinfachen, können Sie `[MinimumAgeAuthorize(20)]`Aktionen mit benutzerdefinierten Autorisierungsattributen wie anken lassen.</span><span class="sxs-lookup"><span data-stu-id="fefb9-131">To make using the policies easier, you can annotate actions with custom authorization attribute like `[MinimumAgeAuthorize(20)]`.</span></span>

## <a name="custom-authorization-attributes"></a><span data-ttu-id="fefb9-132">Benutzerdefinierte Autorisierungsattribute</span><span class="sxs-lookup"><span data-stu-id="fefb9-132">Custom Authorization attributes</span></span>

<span data-ttu-id="fefb9-133">Autorisierungsrichtlinien werden durch ihre Namen identifiziert.</span><span class="sxs-lookup"><span data-stu-id="fefb9-133">Authorization policies are identified by their names.</span></span> <span data-ttu-id="fefb9-134">Die `MinimumAgeAuthorizeAttribute` zuvor beschriebene benutzerdefinierte Muss Argumente einer Zeichenfolge zuordnen, die zum Abrufen der entsprechenden Autorisierungsrichtlinie verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="fefb9-134">The custom `MinimumAgeAuthorizeAttribute` described previously needs to map arguments into a string that can be used to retrieve the corresponding authorization policy.</span></span> <span data-ttu-id="fefb9-135">Sie können dies tun, `AuthorizeAttribute` indem `Age` Sie von `AuthorizeAttribute.Policy` der Unterkunft ableiten und die Eigenschaft dazu bringen, die Eigenschaft zu umschließen.</span><span class="sxs-lookup"><span data-stu-id="fefb9-135">You can do this by deriving from `AuthorizeAttribute` and making the `Age` property wrap the `AuthorizeAttribute.Policy` property.</span></span>

```csharp
internal class MinimumAgeAuthorizeAttribute : AuthorizeAttribute
{
    const string POLICY_PREFIX = "MinimumAge";

    public MinimumAgeAuthorizeAttribute(int age) => Age = age;

    // Get or set the Age property by manipulating the underlying Policy property
    public int Age
    {
        get
        {
            if (int.TryParse(Policy.Substring(POLICY_PREFIX.Length), out var age))
            {
                return age;
            }
            return default(int);
        }
        set
        {
            Policy = $"{POLICY_PREFIX}{value.ToString()}";
        }
    }
}
```

<span data-ttu-id="fefb9-136">Dieser Attributtyp `Policy` verfügt über eine Zeichenfolge, die`"MinimumAge"`auf dem hartcodierten Präfix ( ) basiert, und eine ganze Zahl, die über den Konstruktor übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="fefb9-136">This attribute type has a `Policy` string based on the hard-coded prefix (`"MinimumAge"`) and an integer passed in via the constructor.</span></span>

<span data-ttu-id="fefb9-137">Sie können sie auf Aktionen auf `Authorize` die gleiche Weise wie andere Attribute anwenden, mit der Ausnahme, dass eine ganze Zahl als Parameter verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="fefb9-137">You can apply it to actions in the same way as other `Authorize` attributes except that it takes an integer as a parameter.</span></span>

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a><span data-ttu-id="fefb9-138">Benutzerdefinierter IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="fefb9-138">Custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="fefb9-139">Die `MinimumAgeAuthorizeAttribute` Benutzerdefinierte macht es einfach, Autorisierungsrichtlinien für jedes gewünschte Mindestalter anzufordern.</span><span class="sxs-lookup"><span data-stu-id="fefb9-139">The custom `MinimumAgeAuthorizeAttribute` makes it easy to request authorization policies for any minimum age desired.</span></span> <span data-ttu-id="fefb9-140">Das nächste Problem, das gelöst werden muss, besteht darin, sicherzustellen, dass Autorisierungsrichtlinien für alle unterschiedlichen Altersgruppen verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="fefb9-140">The next problem to solve is making sure that authorization policies are available for all of those different ages.</span></span> <span data-ttu-id="fefb9-141">Dies ist, wo eine `IAuthorizationPolicyProvider` nützlich ist.</span><span class="sxs-lookup"><span data-stu-id="fefb9-141">This is where an `IAuthorizationPolicyProvider` is useful.</span></span>

<span data-ttu-id="fefb9-142">Bei `MinimumAgeAuthorizationAttribute`verwendung folgen die Autorisierungsrichtliniennamen dem `"MinimumAge" + Age` `IAuthorizationPolicyProvider` Muster , daher sollte die benutzerdefinierte Autorisierungsrichtlinien generieren, indem sie:</span><span class="sxs-lookup"><span data-stu-id="fefb9-142">When using `MinimumAgeAuthorizationAttribute`, the authorization policy names will follow the pattern `"MinimumAge" + Age`, so the custom `IAuthorizationPolicyProvider` should generate authorization policies by:</span></span>

* <span data-ttu-id="fefb9-143">Analysieren des Alters anhand des Richtliniennamens.</span><span class="sxs-lookup"><span data-stu-id="fefb9-143">Parsing the age from the policy name.</span></span>
* <span data-ttu-id="fefb9-144">Verwenden `AuthorizationPolicyBuilder` zum Erstellen eines neuen`AuthorizationPolicy`</span><span class="sxs-lookup"><span data-stu-id="fefb9-144">Using `AuthorizationPolicyBuilder` to create a new `AuthorizationPolicy`</span></span>
* <span data-ttu-id="fefb9-145">In diesem und folgenden Beispielen wird davon ausgegangen, dass der Benutzer über ein Cookie authentifiziert wird.</span><span class="sxs-lookup"><span data-stu-id="fefb9-145">In this and following examples it will be assumed that the user is authenticated via a cookie.</span></span> <span data-ttu-id="fefb9-146">Der `AuthorizationPolicyBuilder` sollte entweder mit mindestens einem Berechtigungsschemanamen erstellt werden oder immer erfolgreich sein.</span><span class="sxs-lookup"><span data-stu-id="fefb9-146">The `AuthorizationPolicyBuilder` should either be constructed with at least one authorization scheme name or always succeed.</span></span> <span data-ttu-id="fefb9-147">Andernfalls gibt es keine Informationen darüber, wie eine Herausforderung für den Benutzer zur Verfügung gestellt werden kann, und es wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="fefb9-147">Otherwise there is no information on how to provide a challenge to the user and an exception will be thrown.</span></span>
* <span data-ttu-id="fefb9-148">Hinzufügen von Anforderungen zur Richtlinie `AuthorizationPolicyBuilder.AddRequirements`basierend auf dem Alter mit .</span><span class="sxs-lookup"><span data-stu-id="fefb9-148">Adding requirements to the policy based on the age with `AuthorizationPolicyBuilder.AddRequirements`.</span></span> <span data-ttu-id="fefb9-149">In anderen Szenarien können `RequireClaim` `RequireRole`Sie `RequireUserName` stattdessen , oder verwenden.</span><span class="sxs-lookup"><span data-stu-id="fefb9-149">In other scenarios, you might use `RequireClaim`, `RequireRole`, or `RequireUserName` instead.</span></span>

```csharp
internal class MinimumAgePolicyProvider : IAuthorizationPolicyProvider
{
    const string POLICY_PREFIX = "MinimumAge";

    // Policies are looked up by string name, so expect 'parameters' (like age)
    // to be embedded in the policy names. This is abstracted away from developers
    // by the more strongly-typed attributes derived from AuthorizeAttribute
    // (like [MinimumAgeAuthorize()] in this sample)
    public Task<AuthorizationPolicy> GetPolicyAsync(string policyName)
    {
        if (policyName.StartsWith(POLICY_PREFIX, StringComparison.OrdinalIgnoreCase) &&
            int.TryParse(policyName.Substring(POLICY_PREFIX.Length), out var age))
        {
            var policy = new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme);
            policy.AddRequirements(new MinimumAgeRequirement(age));
            return Task.FromResult(policy.Build());
        }

        return Task.FromResult<AuthorizationPolicy>(null);
    }
}
```

## <a name="multiple-authorization-policy-providers"></a><span data-ttu-id="fefb9-150">Mehrere Autorisierungsrichtlinienanbieter</span><span class="sxs-lookup"><span data-stu-id="fefb9-150">Multiple authorization policy providers</span></span>

<span data-ttu-id="fefb9-151">Beachten Sie `IAuthorizationPolicyProvider` bei der Verwendung benutzerdefinierter Implementierungen, `IAuthorizationPolicyProvider`dass ASP.NET Core nur eine Instanz von verwendet.</span><span class="sxs-lookup"><span data-stu-id="fefb9-151">When using custom `IAuthorizationPolicyProvider` implementations, keep in mind that ASP.NET Core only uses one instance of `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="fefb9-152">Wenn ein benutzerdefinierter Anbieter nicht in der Lage ist, Autorisierungsrichtlinien für alle verwendeten Richtliniennamen bereitzustellen, sollte er an einen Sicherungsanbieter zurücksetzen.</span><span class="sxs-lookup"><span data-stu-id="fefb9-152">If a custom provider isn't able to provide authorization policies for all policy names that will be used, it should defer to a backup provider.</span></span> 

<span data-ttu-id="fefb9-153">Betrachten Sie beispielsweise eine Anwendung, die sowohl benutzerdefinierte Altersrichtlinien als auch traditionellere rollenbasierte Richtlinienabruferfordert.</span><span class="sxs-lookup"><span data-stu-id="fefb9-153">For example, consider an application that needs both custom age policies and more traditional role-based policy retrieval.</span></span> <span data-ttu-id="fefb9-154">Eine solche App könnte einen benutzerdefinierten Autorisierungsrichtlinienanbieter verwenden, der:</span><span class="sxs-lookup"><span data-stu-id="fefb9-154">Such an app could use a custom authorization policy provider that:</span></span>

* <span data-ttu-id="fefb9-155">Versucht, Richtliniennamen zu analysieren.</span><span class="sxs-lookup"><span data-stu-id="fefb9-155">Attempts to parse policy names.</span></span> 
* <span data-ttu-id="fefb9-156">Ruft einen anderen Richtlinienanbieter `DefaultAuthorizationPolicyProvider`(z. B.) auf, wenn der Richtlinienname kein Alter enthält.</span><span class="sxs-lookup"><span data-stu-id="fefb9-156">Calls into a different policy provider (like `DefaultAuthorizationPolicyProvider`) if the policy name doesn't contain an age.</span></span>

<span data-ttu-id="fefb9-157">Die `IAuthorizationPolicyProvider` oben gezeigte Beispielimplementierung kann `DefaultAuthorizationPolicyProvider` aktualisiert werden, um die zu verwenden, indem ein Sicherungsrichtlinienanbieter in seinem Konstruktor erstellt wird (für den Fall, dass der Richtlinienname nicht mit dem erwarteten Muster von 'MinimumAge' + Alter übereinstimmt).</span><span class="sxs-lookup"><span data-stu-id="fefb9-157">The example `IAuthorizationPolicyProvider` implementation shown above can be updated to use the `DefaultAuthorizationPolicyProvider` by creating a backup policy provider in its constructor (to be used in case the policy name doesn't match its expected pattern of 'MinimumAge' + age).</span></span>

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

<span data-ttu-id="fefb9-158">Anschließend kann `GetPolicyAsync` die Methode aktualisiert `BackupPolicyProvider` werden, um die anstelle der Rückgabe null zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="fefb9-158">Then, the `GetPolicyAsync` method can be updated to use the `BackupPolicyProvider` instead of returning null:</span></span>

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a><span data-ttu-id="fefb9-159">Standardrichtlinie</span><span class="sxs-lookup"><span data-stu-id="fefb9-159">Default policy</span></span>

<span data-ttu-id="fefb9-160">Zusätzlich zur Bereitstellung benannter Autorisierungsrichtlinien muss eine benutzerdefinierte `IAuthorizationPolicyProvider` Person implementiert werden, `GetDefaultPolicyAsync` um eine Autorisierungsrichtlinie für `[Authorize]` Attribute ohne angegebenen Richtliniennamen bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="fefb9-160">In addition to providing named authorization policies, a custom `IAuthorizationPolicyProvider` needs to implement `GetDefaultPolicyAsync` to provide an authorization policy for `[Authorize]` attributes without a policy name specified.</span></span>

<span data-ttu-id="fefb9-161">In vielen Fällen erfordert dieses Autorisierungsattribut nur einen authentifizierten Benutzer, sodass Sie die erforderliche Richtlinie mit einem Aufruf von: `RequireAuthenticatedUser`</span><span class="sxs-lookup"><span data-stu-id="fefb9-161">In many cases, this authorization attribute only requires an authenticated user, so you can make the necessary policy with a call to `RequireAuthenticatedUser`:</span></span>

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

<span data-ttu-id="fefb9-162">Wie bei allen Aspekten `IAuthorizationPolicyProvider`einer benutzerdefinierte, können Sie dies anpassen, nach Bedarf.</span><span class="sxs-lookup"><span data-stu-id="fefb9-162">As with all aspects of a custom `IAuthorizationPolicyProvider`, you can customize this, as needed.</span></span> <span data-ttu-id="fefb9-163">In einigen Fällen kann es wünschenswert sein, die `IAuthorizationPolicyProvider`Standardrichtlinie aus einem Fallback abzurufen.</span><span class="sxs-lookup"><span data-stu-id="fefb9-163">In some cases, it may be desirable to retrieve the default policy from a fallback `IAuthorizationPolicyProvider`.</span></span>

## <a name="fallback-policy"></a><span data-ttu-id="fefb9-164">Fallback-Politik</span><span class="sxs-lookup"><span data-stu-id="fefb9-164">Fallback policy</span></span>

<span data-ttu-id="fefb9-165">Eine `IAuthorizationPolicyProvider` benutzerdefinierte Benutzerkannien kann optional implementiert `GetFallbackPolicyAsync` werden, um eine Richtlinie bereitzustellen, die beim Kombinieren von [Richtlinien](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) und beim Angeben von Richtlinien verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="fefb9-165">A custom `IAuthorizationPolicyProvider` can optionally implement `GetFallbackPolicyAsync` to provide a policy that's used when [combining policies](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) and when no policies are specified.</span></span> <span data-ttu-id="fefb9-166">Wenn `GetFallbackPolicyAsync` eine Richtlinie für nicht NULL zurückgegeben wird, wird die zurückgegebene Richtlinie von der Autorisierungsmiddleware verwendet, wenn keine Richtlinien für die Anforderung angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="fefb9-166">If `GetFallbackPolicyAsync` returns a non-null policy, the returned policy is used by the Authorization Middleware when no policies are specified for the request.</span></span>

<span data-ttu-id="fefb9-167">Wenn keine Fallbackrichtlinie erforderlich ist, `null` kann der Anbieter den Fallbackanbieter zurückgeben oder zurückstellen:</span><span class="sxs-lookup"><span data-stu-id="fefb9-167">If no fallback policy is required, the provider can return `null` or defer to the fallback provider:</span></span>

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a><span data-ttu-id="fefb9-168">Verwenden eines benutzerdefinierten IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="fefb9-168">Use a custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="fefb9-169">Um benutzerdefinierte Richtlinien `IAuthorizationPolicyProvider`aus einem zu verwenden, müssen Sie:</span><span class="sxs-lookup"><span data-stu-id="fefb9-169">To use custom policies from an `IAuthorizationPolicyProvider`, you must:</span></span>

* <span data-ttu-id="fefb9-170">Registrieren Sie `AuthorizationHandler` die entsprechenden Typen mit Abhängigkeitsinjektion (beschrieben in [der richtlinienbasierten Autorisierung](xref:security/authorization/policies#authorization-handlers)), wie bei allen richtlinienbasierten Autorisierungsszenarien.</span><span class="sxs-lookup"><span data-stu-id="fefb9-170">Register the appropriate `AuthorizationHandler` types with dependency injection (described in [policy-based authorization](xref:security/authorization/policies#authorization-handlers)), as with all policy-based authorization scenarios.</span></span>
* <span data-ttu-id="fefb9-171">Registrieren Sie `IAuthorizationPolicyProvider` den benutzerdefinierten Typ in der `Startup.ConfigureServices`Abhängigkeitsinjektionsdienstauflistung (in ) der App, um den Standardrichtlinienanbieter zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="fefb9-171">Register the custom `IAuthorizationPolicyProvider` type in the app's dependency injection service collection (in `Startup.ConfigureServices`) to replace the default policy provider.</span></span>

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

<span data-ttu-id="fefb9-172">Ein vollständiges benutzerdefiniertes `IAuthorizationPolicyProvider` Beispiel ist im [dotnet/aspnetcore GitHub-Repository](https://github.com/dotnet/aspnetcore/tree/ea555458dc61e04314598c25b3ab8c56362a5123/src/Security/samples/CustomPolicyProvider)verfügbar.</span><span class="sxs-lookup"><span data-stu-id="fefb9-172">A complete custom `IAuthorizationPolicyProvider` sample is available in the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore/tree/ea555458dc61e04314598c25b3ab8c56362a5123/src/Security/samples/CustomPolicyProvider).</span></span>
