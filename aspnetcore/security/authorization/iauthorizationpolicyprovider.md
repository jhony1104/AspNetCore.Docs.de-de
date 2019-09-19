---
title: Benutzerdefinierte Autorisierungs Richtlinien Anbieter in ASP.net Core
author: mjrousos
description: Erfahren Sie, wie Sie einen benutzerdefinierten iauthorizationpolicyprovider in einer ASP.net Core-App verwenden, um Autorisierungs Richtlinien dynamisch zu generieren.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: b11f7f94e1042e65d5f2ff8ab0fe9ea7838bebeb
ms.sourcegitcommit: b1e480e1736b0fe0e4d8dce4a4cf5c8e47fc2101
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/19/2019
ms.locfileid: "71108054"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a><span data-ttu-id="f51c6-103">Benutzerdefinierte Autorisierungs Richtlinien Anbieter, die iauthorizationpolicyprovider in ASP.net Core verwenden</span><span class="sxs-lookup"><span data-stu-id="f51c6-103">Custom Authorization Policy Providers using IAuthorizationPolicyProvider in ASP.NET Core</span></span> 

<span data-ttu-id="f51c6-104">Von [Mike rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="f51c6-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="f51c6-105">Wenn Sie die [Richtlinien basierte Autorisierung](xref:security/authorization/policies)verwenden, werden die Richtlinien `AuthorizationOptions.AddPolicy` in der Regel durch Aufrufen von als Teil der Autorisierungs Dienst Konfiguration registriert.</span><span class="sxs-lookup"><span data-stu-id="f51c6-105">Typically when using [policy-based authorization](xref:security/authorization/policies), policies are registered by calling `AuthorizationOptions.AddPolicy` as part of authorization service configuration.</span></span> <span data-ttu-id="f51c6-106">In einigen Szenarien ist es möglicherweise nicht möglich (oder wünschenswert), Alle Autorisierungs Richtlinien auf diese Weise zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="f51c6-106">In some scenarios, it may not be possible (or desirable) to register all authorization policies in this way.</span></span> <span data-ttu-id="f51c6-107">In diesen Fällen können Sie eine benutzerdefinierte `IAuthorizationPolicyProvider` verwenden, um zu steuern, wie Autorisierungs Richtlinien bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="f51c6-107">In those cases, you can use a custom `IAuthorizationPolicyProvider` to control how authorization policies are supplied.</span></span>

<span data-ttu-id="f51c6-108">Beispiele für Szenarien, in denen ein benutzerdefinierter [iauthorizationpolicyprovider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) nützlich sein kann, sind:</span><span class="sxs-lookup"><span data-stu-id="f51c6-108">Examples of scenarios where a custom [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) may be useful include:</span></span>

* <span data-ttu-id="f51c6-109">Verwenden eines externen Dienstanbieter zur Bereitstellung der Richtlinien Auswertung.</span><span class="sxs-lookup"><span data-stu-id="f51c6-109">Using an external service to provide policy evaluation.</span></span>
* <span data-ttu-id="f51c6-110">Die Verwendung einer großen Bandbreite an Richtlinien (z. b. für unterschiedliche Raum Nummern oder Alters Zahlen), daher ist es nicht sinnvoll, jede `AuthorizationOptions.AddPolicy` einzelne Autorisierungs Richtlinie mit einem-Befehl hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="f51c6-110">Using a large range of policies (for different room numbers or ages, for example), so it doesn’t make sense to add each individual authorization policy with an `AuthorizationOptions.AddPolicy` call.</span></span>
* <span data-ttu-id="f51c6-111">Erstellen von Richtlinien zur Laufzeit basierend auf Informationen in einer externen Datenquelle (z. b. einer Datenbank) oder durch das dynamische ermitteln der Autorisierungs Anforderungen über einen anderen Mechanismus</span><span class="sxs-lookup"><span data-stu-id="f51c6-111">Creating policies at runtime based on information in an external data source (like a database) or determining authorization requirements dynamically through another mechanism.</span></span>

<span data-ttu-id="f51c6-112">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) aus dem [aspnetcore-GitHub-Repository](https://github.com/aspnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="f51c6-112">[View or download sample code](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) from the [AspNetCore GitHub repository](https://github.com/aspnet/AspNetCore).</span></span> <span data-ttu-id="f51c6-113">Laden Sie die ASPNET/aspnetcore-Repository-ZIP-Datei herunter.</span><span class="sxs-lookup"><span data-stu-id="f51c6-113">Download the aspnet/AspNetCore repository ZIP file.</span></span> <span data-ttu-id="f51c6-114">Entzippen Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="f51c6-114">Unzip the file.</span></span> <span data-ttu-id="f51c6-115">Navigieren Sie zum Projektordner *src/Security/Samples/custompolicyprovider* .</span><span class="sxs-lookup"><span data-stu-id="f51c6-115">Navigate to the *src/Security/samples/CustomPolicyProvider* project folder.</span></span>

## <a name="customize-policy-retrieval"></a><span data-ttu-id="f51c6-116">Anpassen des Richtlinien Abrufs</span><span class="sxs-lookup"><span data-stu-id="f51c6-116">Customize policy retrieval</span></span>

<span data-ttu-id="f51c6-117">ASP.net Core-Apps verwenden eine Implementierung der `IAuthorizationPolicyProvider` -Schnittstelle zum Abrufen von Autorisierungs Richtlinien.</span><span class="sxs-lookup"><span data-stu-id="f51c6-117">ASP.NET Core apps use an implementation of the `IAuthorizationPolicyProvider` interface to retrieve authorization policies.</span></span> <span data-ttu-id="f51c6-118">Standardmäßig ist [defaultauthorizationpolicyprovider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) registriert und wird verwendet.</span><span class="sxs-lookup"><span data-stu-id="f51c6-118">By default, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) is registered and used.</span></span> <span data-ttu-id="f51c6-119">`DefaultAuthorizationPolicyProvider`gibt Richtlinien aus `AuthorizationOptions` der in einem `IServiceCollection.AddAuthorization` -Befehl zurück.</span><span class="sxs-lookup"><span data-stu-id="f51c6-119">`DefaultAuthorizationPolicyProvider` returns policies from the `AuthorizationOptions` provided in an `IServiceCollection.AddAuthorization` call.</span></span>

<span data-ttu-id="f51c6-120">Sie können dieses Verhalten anpassen, indem Sie eine `IAuthorizationPolicyProvider` andere Implementierung im Container für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) der APP registrieren.</span><span class="sxs-lookup"><span data-stu-id="f51c6-120">You can customize this behavior by registering a different `IAuthorizationPolicyProvider` implementation in the app’s [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> 

<span data-ttu-id="f51c6-121">Die `IAuthorizationPolicyProvider` Schnittstelle enthält zwei APIs:</span><span class="sxs-lookup"><span data-stu-id="f51c6-121">The `IAuthorizationPolicyProvider` interface contains two APIs:</span></span>

* <span data-ttu-id="f51c6-122">[Getpolicyasync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) gibt eine Autorisierungs Richtlinie für einen angegebenen Namen zurück.</span><span class="sxs-lookup"><span data-stu-id="f51c6-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) returns an authorization policy for a given name.</span></span>
* <span data-ttu-id="f51c6-123">[Getdefaultpolicyasync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) gibt die Standard Autorisierungs Richtlinie zurück (die Richt `[Authorize]` Linie, die für Attribute ohne angegebene Richtlinie verwendet wird).</span><span class="sxs-lookup"><span data-stu-id="f51c6-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) returns the default authorization policy (the policy used for `[Authorize]` attributes without a policy specified).</span></span> 

<span data-ttu-id="f51c6-124">Durch die Implementierung dieser beiden APIs können Sie die Art der Bereitstellung von Autorisierungs Richtlinien anpassen.</span><span class="sxs-lookup"><span data-stu-id="f51c6-124">By implementing these two APIs, you can customize how authorization policies are provided.</span></span>

## <a name="parameterized-authorize-attribute-example"></a><span data-ttu-id="f51c6-125">Beispiel für ein parametrisiertes Autorisierungs Attribut</span><span class="sxs-lookup"><span data-stu-id="f51c6-125">Parameterized authorize attribute example</span></span>

<span data-ttu-id="f51c6-126">Ein Szenario, `IAuthorizationPolicyProvider` in dem nützlich ist, `[Authorize]` ist das Aktivieren benutzerdefinierter Attribute, deren Anforderungen von einem Parameter abhängen.</span><span class="sxs-lookup"><span data-stu-id="f51c6-126">One scenario where `IAuthorizationPolicyProvider` is useful is enabling custom `[Authorize]` attributes whose requirements depend on a parameter.</span></span> <span data-ttu-id="f51c6-127">Beispielsweise wurde in der Dokumentation zur [Richtlinien basierten Autorisierung](xref:security/authorization/policies) eine Alters basierte Richtlinie ("AtLeast21") als Beispiel verwendet.</span><span class="sxs-lookup"><span data-stu-id="f51c6-127">For example, in [policy-based authorization](xref:security/authorization/policies) documentation, an age-based (“AtLeast21”) policy was used as a sample.</span></span> <span data-ttu-id="f51c6-128">Wenn verschiedene Controller Aktionen in einer APP für Benutzer unter *schiedlicher* Zeiten verfügbar gemacht werden sollen, kann es hilfreich sein, viele verschiedene Alters basierte Richtlinien zu haben.</span><span class="sxs-lookup"><span data-stu-id="f51c6-128">If different controller actions in an app should be made available to users of *different* ages, it might be useful to have many different age-based policies.</span></span> <span data-ttu-id="f51c6-129">Anstatt alle unterschiedlichen Alters basierten Richtlinien zu registrieren `AuthorizationOptions`, die von der Anwendung benötigt werden, können Sie die Richtlinien dynamisch mit einem benutzerdefinierten `IAuthorizationPolicyProvider`generieren.</span><span class="sxs-lookup"><span data-stu-id="f51c6-129">Instead of registering all the different age-based policies that the application will need in `AuthorizationOptions`, you can generate the policies dynamically with a custom `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="f51c6-130">Um die Verwendung der Richtlinien zu vereinfachen, können Sie Aktionen mit benutzerdefiniertem Autorisierungs `[MinimumAgeAuthorize(20)]`Attribut wie mit Anmerkungen versehen.</span><span class="sxs-lookup"><span data-stu-id="f51c6-130">To make using the policies easier, you can annotate actions with custom authorization attribute like `[MinimumAgeAuthorize(20)]`.</span></span>

## <a name="custom-authorization-attributes"></a><span data-ttu-id="f51c6-131">Benutzerdefinierte Autorisierungs Attribute</span><span class="sxs-lookup"><span data-stu-id="f51c6-131">Custom Authorization attributes</span></span>

<span data-ttu-id="f51c6-132">Autorisierungs Richtlinien werden anhand ihrer Namen identifiziert.</span><span class="sxs-lookup"><span data-stu-id="f51c6-132">Authorization policies are identified by their names.</span></span> <span data-ttu-id="f51c6-133">Der zuvor `MinimumAgeAuthorizeAttribute` beschriebene benutzerdefinierte muss Argumente einer Zeichenfolge zuordnen, die zum Abrufen der entsprechenden Autorisierungs Richtlinie verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="f51c6-133">The custom `MinimumAgeAuthorizeAttribute` described previously needs to map arguments into a string that can be used to retrieve the corresponding authorization policy.</span></span> <span data-ttu-id="f51c6-134">Dies können Sie erreichen, indem Sie `AuthorizeAttribute` von ableiten und `Age` die-Eigenschaft `AuthorizeAttribute.Policy` in die-Eigenschaft einbinden.</span><span class="sxs-lookup"><span data-stu-id="f51c6-134">You can do this by deriving from `AuthorizeAttribute` and making the `Age` property wrap the `AuthorizeAttribute.Policy` property.</span></span>

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

<span data-ttu-id="f51c6-135">Dieser Attributtyp hat `Policy` eine Zeichenfolge, die auf dem hart codierten Präfix (`"MinimumAge"`) basiert, und eine ganze Zahl, die über den Konstruktor übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="f51c6-135">This attribute type has a `Policy` string based on the hard-coded prefix (`"MinimumAge"`) and an integer passed in via the constructor.</span></span>

<span data-ttu-id="f51c6-136">Sie können es auf die gleiche Weise wie andere `Authorize` Attribute anwenden, außer dass es eine ganze Zahl als Parameter annimmt.</span><span class="sxs-lookup"><span data-stu-id="f51c6-136">You can apply it to actions in the same way as other `Authorize` attributes except that it takes an integer as a parameter.</span></span>

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a><span data-ttu-id="f51c6-137">Benutzerdefinierter iauthorizationpolicyprovider</span><span class="sxs-lookup"><span data-stu-id="f51c6-137">Custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="f51c6-138">Das Benutzer `MinimumAgeAuthorizeAttribute` definierte vereinfacht das Anfordern von Autorisierungs Richtlinien für jedes gewünschte minimal Alter.</span><span class="sxs-lookup"><span data-stu-id="f51c6-138">The custom `MinimumAgeAuthorizeAttribute` makes it easy to request authorization policies for any minimum age desired.</span></span> <span data-ttu-id="f51c6-139">Das nächste Problem, das gelöst werden muss, besteht darin, sicherzustellen, dass Autorisierungs Richtlinien für alle unterschiedlichen Alters Bereiche verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="f51c6-139">The next problem to solve is making sure that authorization policies are available for all of those different ages.</span></span> <span data-ttu-id="f51c6-140">An dieser Stelle ist `IAuthorizationPolicyProvider` ein nützlich.</span><span class="sxs-lookup"><span data-stu-id="f51c6-140">This is where an `IAuthorizationPolicyProvider` is useful.</span></span>

<span data-ttu-id="f51c6-141">Bei Verwendung `MinimumAgeAuthorizationAttribute`von folgen die Namen der Autorisierungs Richtlinien dem `"MinimumAge" + Age`Muster, sodass die `IAuthorizationPolicyProvider` benutzerdefinierte Autorisierungs Richtlinien generieren soll:</span><span class="sxs-lookup"><span data-stu-id="f51c6-141">When using `MinimumAgeAuthorizationAttribute`, the authorization policy names will follow the pattern `"MinimumAge" + Age`, so the custom `IAuthorizationPolicyProvider` should generate authorization policies by:</span></span>

* <span data-ttu-id="f51c6-142">Das Alter wird anhand des Richtlinien namens verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="f51c6-142">Parsing the age from the policy name.</span></span>
* <span data-ttu-id="f51c6-143">Verwenden `AuthorizationPolicyBuilder` von zum Erstellen eines neuen`AuthorizationPolicy`</span><span class="sxs-lookup"><span data-stu-id="f51c6-143">Using `AuthorizationPolicyBuilder` to create a new `AuthorizationPolicy`</span></span>
* <span data-ttu-id="f51c6-144">In diesem und den folgenden Beispielen wird davon ausgegangen, dass der Benutzer über ein Cookie authentifiziert wird.</span><span class="sxs-lookup"><span data-stu-id="f51c6-144">In this and following examples it will be assumed that the user is authenticated via a cookie.</span></span> <span data-ttu-id="f51c6-145">`AuthorizationPolicyBuilder` Muss entweder mit mindestens einem Autorisierungs Schema Namen erstellt werden oder immer erfolgreich sein.</span><span class="sxs-lookup"><span data-stu-id="f51c6-145">The `AuthorizationPolicyBuilder` should either be constructed with at least one authorization scheme name or always succeed.</span></span> <span data-ttu-id="f51c6-146">Andernfalls gibt es keine Informationen zum Bereitstellen einer Herausforderung für den Benutzer, und es wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="f51c6-146">Otherwise there is no information on how to provide a challenge to the user and an exception will be thrown.</span></span>
* <span data-ttu-id="f51c6-147">Hinzufügen von Anforderungen zur Richtlinie basierend auf dem `AuthorizationPolicyBuilder.AddRequirements`Alter mit.</span><span class="sxs-lookup"><span data-stu-id="f51c6-147">Adding requirements to the policy based on the age with `AuthorizationPolicyBuilder.AddRequirements`.</span></span> <span data-ttu-id="f51c6-148">In anderen Szenarien können Sie stattdessen, `RequireClaim` `RequireRole`oder `RequireUserName` verwenden.</span><span class="sxs-lookup"><span data-stu-id="f51c6-148">In other scenarios, you might use `RequireClaim`, `RequireRole`, or `RequireUserName` instead.</span></span>

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

## <a name="multiple-authorization-policy-providers"></a><span data-ttu-id="f51c6-149">Mehrere Autorisierungs Richtlinien Anbieter</span><span class="sxs-lookup"><span data-stu-id="f51c6-149">Multiple authorization policy providers</span></span>

<span data-ttu-id="f51c6-150">Beachten Sie bei `IAuthorizationPolicyProvider` der Verwendung von benutzerdefinierten Implementierungen, dass ASP.net Core nur eine Instanz `IAuthorizationPolicyProvider`von verwendet.</span><span class="sxs-lookup"><span data-stu-id="f51c6-150">When using custom `IAuthorizationPolicyProvider` implementations, keep in mind that ASP.NET Core only uses one instance of `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="f51c6-151">Wenn ein benutzerdefinierter Anbieter keine Autorisierungs Richtlinien für alle zu verwendenden Richtlinien Namen bereitstellen kann, sollte er auf einen Sicherungs Anbieter zurückgreifen.</span><span class="sxs-lookup"><span data-stu-id="f51c6-151">If a custom provider isn't able to provide authorization policies for all policy names that will be used, it should fall back to a backup provider.</span></span> 

<span data-ttu-id="f51c6-152">Stellen Sie sich beispielsweise eine Anwendung vor, die sowohl benutzerdefinierte Alters Richtlinien als auch herkömmlicheren rollenbasierten Richtlinien Abruf benötigt.</span><span class="sxs-lookup"><span data-stu-id="f51c6-152">For example, consider an application that needs both custom age policies and more traditional role-based policy retrieval.</span></span> <span data-ttu-id="f51c6-153">Eine solche APP könnte einen benutzerdefinierten Autorisierungs Richtlinien Anbieter verwenden, der Folgendes:</span><span class="sxs-lookup"><span data-stu-id="f51c6-153">Such an app could use a custom authorization policy provider that:</span></span>

* <span data-ttu-id="f51c6-154">Versucht, Richtlinien Namen zu analysieren.</span><span class="sxs-lookup"><span data-stu-id="f51c6-154">Attempts to parse policy names.</span></span> 
* <span data-ttu-id="f51c6-155">Ruft einen anderen Richtlinien Anbieter auf ( `DefaultAuthorizationPolicyProvider`z. b.), wenn der Richtlinien Name kein Alter enthält.</span><span class="sxs-lookup"><span data-stu-id="f51c6-155">Calls into a different policy provider (like `DefaultAuthorizationPolicyProvider`) if the policy name doesn't contain an age.</span></span>

<span data-ttu-id="f51c6-156">Die oben `IAuthorizationPolicyProvider` gezeigte Beispiel Implementierung kann aktualisiert werden, um die `DefaultAuthorizationPolicyProvider` zu verwenden, indem ein Fall Back Richtlinien Anbieter im Konstruktor erstellt wird (für den Fall, dass der Richtlinien Name nicht mit dem erwarteten Muster von "minimumAge" + Age identisch ist).</span><span class="sxs-lookup"><span data-stu-id="f51c6-156">The example `IAuthorizationPolicyProvider` implementation shown above can be updated to use the `DefaultAuthorizationPolicyProvider` by creating a fallback policy provider in its constructor (to be used in case the policy name doesn't match its expected pattern of 'MinimumAge' + age).</span></span>

```csharp
private DefaultAuthorizationPolicyProvider FallbackPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    FallbackPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

<span data-ttu-id="f51c6-157">Anschließend kann die `GetPolicyAsync` -Methode so aktualisiert werden, dass `FallbackPolicyProvider` anstelle von NULL verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="f51c6-157">Then, the `GetPolicyAsync` method can be updated to use the `FallbackPolicyProvider` instead of returning null:</span></span>

```csharp
...
return FallbackPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a><span data-ttu-id="f51c6-158">Standard Richtlinie</span><span class="sxs-lookup"><span data-stu-id="f51c6-158">Default policy</span></span>

<span data-ttu-id="f51c6-159">Zusätzlich zur Bereitstellung benannter Autorisierungs Richtlinien `IAuthorizationPolicyProvider` muss ein Benutzer `GetDefaultPolicyAsync` definiertes implementieren, um eine `[Authorize]` Autorisierungs Richtlinie für Attribute bereitzustellen, ohne dass ein Richtlinien Name angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="f51c6-159">In addition to providing named authorization policies, a custom `IAuthorizationPolicyProvider` needs to implement `GetDefaultPolicyAsync` to provide an authorization policy for `[Authorize]` attributes without a policy name specified.</span></span>

<span data-ttu-id="f51c6-160">In vielen Fällen erfordert dieses Autorisierungs Attribut nur einen authentifizierten Benutzer, sodass Sie die erforderliche Richtlinie mit einem `RequireAuthenticatedUser`-Befehl erstellen können:</span><span class="sxs-lookup"><span data-stu-id="f51c6-160">In many cases, this authorization attribute only requires an authenticated user, so you can make the necessary policy with a call to `RequireAuthenticatedUser`:</span></span>

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

<span data-ttu-id="f51c6-161">Wie bei allen Aspekten eines benutzerdefinierten `IAuthorizationPolicyProvider`können Sie dies nach Bedarf anpassen.</span><span class="sxs-lookup"><span data-stu-id="f51c6-161">As with all aspects of a custom `IAuthorizationPolicyProvider`, you can customize this, as needed.</span></span> <span data-ttu-id="f51c6-162">In einigen Fällen kann es wünschenswert sein, die Standard Richtlinie von einem Fall Back `IAuthorizationPolicyProvider`abzurufen.</span><span class="sxs-lookup"><span data-stu-id="f51c6-162">In some cases, it may be desirable to retrieve the default policy from a fallback `IAuthorizationPolicyProvider`.</span></span>

## <a name="required-policy"></a><span data-ttu-id="f51c6-163">Erforderliche Richtlinie</span><span class="sxs-lookup"><span data-stu-id="f51c6-163">Required policy</span></span>

<span data-ttu-id="f51c6-164">Eine Benutzer `IAuthorizationPolicyProvider` definierte muss implementieren `GetRequiredPolicyAsync` , um optional eine Richtlinie bereitzustellen, die immer erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="f51c6-164">A custom `IAuthorizationPolicyProvider` needs to implement `GetRequiredPolicyAsync` to, optionally, provide a policy that is always required.</span></span> <span data-ttu-id="f51c6-165">Wenn `GetRequiredPolicyAsync` eine Richtlinie ungleich NULL zurückgibt, wird diese Richtlinie mit jeder anderen (benannten oder Standard-) Richtlinie kombiniert, die angefordert wird.</span><span class="sxs-lookup"><span data-stu-id="f51c6-165">If `GetRequiredPolicyAsync` returns a non-null policy, that policy will be combined with any other (named or default) policy that is requested.</span></span>

<span data-ttu-id="f51c6-166">Wenn keine erforderliche Richtlinie erforderlich ist, kann der Anbieter einfach Null zurückgeben oder auf den Fall Back Anbieter zurückgreifen:</span><span class="sxs-lookup"><span data-stu-id="f51c6-166">If no required policy is needed, the provider can just return null or defer to the fallback provider:</span></span>

```csharp
public Task<AuthorizationPolicy> GetRequiredPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a><span data-ttu-id="f51c6-167">Verwenden eines benutzerdefinierten iauthorizationpolicyprovider</span><span class="sxs-lookup"><span data-stu-id="f51c6-167">Use a custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="f51c6-168">Zum Verwenden von benutzerdefinierten Richt `IAuthorizationPolicyProvider`Linien aus einer müssen Sie folgende Schritte ausführen:</span><span class="sxs-lookup"><span data-stu-id="f51c6-168">To use custom policies from an `IAuthorizationPolicyProvider`, you must:</span></span>

* <span data-ttu-id="f51c6-169">Registrieren der entsprechenden `AuthorizationHandler` Typen mit Abhängigkeitsinjektion (in [Richtlinien basierter Autorisierung](xref:security/authorization/policies#authorization-handlers)beschrieben), wie bei allen Richtlinien basierten Autorisierungs Szenarios.</span><span class="sxs-lookup"><span data-stu-id="f51c6-169">Register the appropriate `AuthorizationHandler` types with dependency injection (described in [policy-based authorization](xref:security/authorization/policies#authorization-handlers)), as with all policy-based authorization scenarios.</span></span>
* <span data-ttu-id="f51c6-170">Registrieren Sie den `IAuthorizationPolicyProvider` benutzerdefinierten Typ in der Abhängigkeitsinjektion-Dienst Auflistung `Startup.ConfigureServices`(in) der APP, um den Standardrichtlinien Anbieter zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="f51c6-170">Register the custom `IAuthorizationPolicyProvider` type in the app's dependency injection service collection (in `Startup.ConfigureServices`) to replace the default policy provider.</span></span>

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

<span data-ttu-id="f51c6-171">Eine komplette Benutzer `IAuthorizationPolicyProvider` definierte Stichprobe finden Sie im [GitHub-Repository ASPNET/authsamples](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider).</span><span class="sxs-lookup"><span data-stu-id="f51c6-171">A complete custom `IAuthorizationPolicyProvider` sample is available in the [aspnet/AuthSamples GitHub repository](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider).</span></span>
