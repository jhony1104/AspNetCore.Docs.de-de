---
title: Benutzerdefinierte Autorisierungs Richtlinien Anbieter in ASP.net Core
author: mjrousos
description: Erfahren Sie, wie Sie einen benutzerdefinierten iauthorizationpolicyprovider in einer ASP.net Core-App verwenden, um Autorisierungs Richtlinien dynamisch zu generieren.
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: bb9b52da08639680b05a102dd4df71ff1af00971
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399555"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a><span data-ttu-id="e7b52-103">Benutzerdefinierte Autorisierungs Richtlinien Anbieter, die iauthorizationpolicyprovider in ASP.net Core verwenden</span><span class="sxs-lookup"><span data-stu-id="e7b52-103">Custom Authorization Policy Providers using IAuthorizationPolicyProvider in ASP.NET Core</span></span> 

<span data-ttu-id="e7b52-104">Von [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="e7b52-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="e7b52-105">Wenn Sie die [Richtlinien basierte Autorisierung](xref:security/authorization/policies)verwenden, werden die Richtlinien in der Regel durch Aufrufen von `AuthorizationOptions.AddPolicy` als Teil der Autorisierungs Dienst Konfiguration registriert.</span><span class="sxs-lookup"><span data-stu-id="e7b52-105">Typically when using [policy-based authorization](xref:security/authorization/policies), policies are registered by calling `AuthorizationOptions.AddPolicy` as part of authorization service configuration.</span></span> <span data-ttu-id="e7b52-106">In einigen Szenarien ist es möglicherweise nicht möglich (oder wünschenswert), Alle Autorisierungs Richtlinien auf diese Weise zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="e7b52-106">In some scenarios, it may not be possible (or desirable) to register all authorization policies in this way.</span></span> <span data-ttu-id="e7b52-107">In diesen Fällen können Sie [eine benutzerdefinierte `IAuthorizationPolicyProvider` verwenden](#ci) , um zu steuern, wie Autorisierungs Richtlinien bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="e7b52-107">In those cases, you can [use a custom `IAuthorizationPolicyProvider`](#ci) to control how authorization policies are supplied.</span></span>

<span data-ttu-id="e7b52-108">Beispiele für Szenarien, in denen ein benutzerdefinierter [iauthorizationpolicyprovider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) nützlich sein kann, sind:</span><span class="sxs-lookup"><span data-stu-id="e7b52-108">Examples of scenarios where a custom [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) may be useful include:</span></span>

* <span data-ttu-id="e7b52-109">Verwenden eines externen Dienstanbieter zur Bereitstellung der Richtlinien Auswertung.</span><span class="sxs-lookup"><span data-stu-id="e7b52-109">Using an external service to provide policy evaluation.</span></span>
* <span data-ttu-id="e7b52-110">Die Verwendung einer großen Bandbreite an Richtlinien (z. b. für unterschiedliche Raum Nummern oder Alters Zahlen), daher ist es nicht sinnvoll, jede einzelne Autorisierungs Richtlinie mit einem-Befehl hinzuzufügen `AuthorizationOptions.AddPolicy` .</span><span class="sxs-lookup"><span data-stu-id="e7b52-110">Using a large range of policies (for different room numbers or ages, for example), so it doesn't make sense to add each individual authorization policy with an `AuthorizationOptions.AddPolicy` call.</span></span>
* <span data-ttu-id="e7b52-111">Erstellen von Richtlinien zur Laufzeit basierend auf Informationen in einer externen Datenquelle (z. b. einer Datenbank) oder durch das dynamische ermitteln der Autorisierungs Anforderungen über einen anderen Mechanismus</span><span class="sxs-lookup"><span data-stu-id="e7b52-111">Creating policies at runtime based on information in an external data source (like a database) or determining authorization requirements dynamically through another mechanism.</span></span>

<span data-ttu-id="e7b52-112">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) aus dem [aspnetcore-GitHub-Repository](https://github.com/dotnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="e7b52-112">[View or download sample code](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) from the [AspNetCore GitHub repository](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="e7b52-113">Laden Sie die dotnet/aspnetcore-Repository-ZIP-Datei herunter.</span><span class="sxs-lookup"><span data-stu-id="e7b52-113">Download the dotnet/AspNetCore repository ZIP file.</span></span> <span data-ttu-id="e7b52-114">Entzippen Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="e7b52-114">Unzip the file.</span></span> <span data-ttu-id="e7b52-115">Navigieren Sie zum Projektordner *src/Security/Samples/custompolicyprovider* .</span><span class="sxs-lookup"><span data-stu-id="e7b52-115">Navigate to the *src/Security/samples/CustomPolicyProvider* project folder.</span></span>

## <a name="customize-policy-retrieval"></a><span data-ttu-id="e7b52-116">Anpassen des Richtlinien Abrufs</span><span class="sxs-lookup"><span data-stu-id="e7b52-116">Customize policy retrieval</span></span>

<span data-ttu-id="e7b52-117">ASP.net Core-Apps verwenden eine Implementierung der- `IAuthorizationPolicyProvider` Schnittstelle zum Abrufen von Autorisierungs Richtlinien.</span><span class="sxs-lookup"><span data-stu-id="e7b52-117">ASP.NET Core apps use an implementation of the `IAuthorizationPolicyProvider` interface to retrieve authorization policies.</span></span> <span data-ttu-id="e7b52-118">Standardmäßig ist [defaultauthorizationpolicyprovider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) registriert und wird verwendet.</span><span class="sxs-lookup"><span data-stu-id="e7b52-118">By default, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) is registered and used.</span></span> <span data-ttu-id="e7b52-119">`DefaultAuthorizationPolicyProvider`gibt Richtlinien aus der in einem-Befehl zurück `AuthorizationOptions` `IServiceCollection.AddAuthorization` .</span><span class="sxs-lookup"><span data-stu-id="e7b52-119">`DefaultAuthorizationPolicyProvider` returns policies from the `AuthorizationOptions` provided in an `IServiceCollection.AddAuthorization` call.</span></span>

<span data-ttu-id="e7b52-120">Passen Sie dieses Verhalten an, indem Sie eine andere `IAuthorizationPolicyProvider` Implementierung im Container für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) der APP registrieren.</span><span class="sxs-lookup"><span data-stu-id="e7b52-120">Customize this behavior by registering a different `IAuthorizationPolicyProvider` implementation in the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> 

<span data-ttu-id="e7b52-121">Die `IAuthorizationPolicyProvider` Schnittstelle enthält drei APIs:</span><span class="sxs-lookup"><span data-stu-id="e7b52-121">The `IAuthorizationPolicyProvider` interface contains three APIs:</span></span>

* <span data-ttu-id="e7b52-122">[Getpolicyasync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) gibt eine Autorisierungs Richtlinie für einen angegebenen Namen zurück.</span><span class="sxs-lookup"><span data-stu-id="e7b52-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) returns an authorization policy for a given name.</span></span>
* <span data-ttu-id="e7b52-123">[Getdefaultpolicyasync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) gibt die Standard Autorisierungs Richtlinie zurück (die Richtlinie, die für `[Authorize]` Attribute ohne angegebene Richtlinie verwendet wird).</span><span class="sxs-lookup"><span data-stu-id="e7b52-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) returns the default authorization policy (the policy used for `[Authorize]` attributes without a policy specified).</span></span> 
* <span data-ttu-id="e7b52-124">[Getfallbackpolicyasync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) gibt die Fall Back Autorisierungs Richtlinie zurück (die Richtlinie, die von der Autorisierungs Middleware verwendet wird, wenn keine Richtlinie angegeben ist).</span><span class="sxs-lookup"><span data-stu-id="e7b52-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) returns the fallback authorization policy (the policy used by the Authorization Middleware when no policy is specified).</span></span> 

<span data-ttu-id="e7b52-125">Durch Implementieren dieser APIs können Sie die Art der Bereitstellung von Autorisierungs Richtlinien anpassen.</span><span class="sxs-lookup"><span data-stu-id="e7b52-125">By implementing these APIs, you can customize how authorization policies are provided.</span></span>

## <a name="parameterized-authorize-attribute-example"></a><span data-ttu-id="e7b52-126">Beispiel für ein parametrisiertes Autorisierungs Attribut</span><span class="sxs-lookup"><span data-stu-id="e7b52-126">Parameterized authorize attribute example</span></span>

<span data-ttu-id="e7b52-127">Ein Szenario, in dem nützlich ist, `IAuthorizationPolicyProvider` ist das Aktivieren benutzerdefinierter `[Authorize]` Attribute, deren Anforderungen von einem Parameter abhängen.</span><span class="sxs-lookup"><span data-stu-id="e7b52-127">One scenario where `IAuthorizationPolicyProvider` is useful is enabling custom `[Authorize]` attributes whose requirements depend on a parameter.</span></span> <span data-ttu-id="e7b52-128">Beispielsweise wurde in der Dokumentation zur [Richtlinien basierten Autorisierung](xref:security/authorization/policies) eine Alters basierte Richtlinie ("AtLeast21") als Beispiel verwendet.</span><span class="sxs-lookup"><span data-stu-id="e7b52-128">For example, in [policy-based authorization](xref:security/authorization/policies) documentation, an age-based (“AtLeast21”) policy was used as a sample.</span></span> <span data-ttu-id="e7b52-129">Wenn verschiedene Controller Aktionen in einer APP für Benutzer unter *schiedlicher* Zeiten verfügbar gemacht werden sollen, kann es hilfreich sein, viele verschiedene Alters basierte Richtlinien zu haben.</span><span class="sxs-lookup"><span data-stu-id="e7b52-129">If different controller actions in an app should be made available to users of *different* ages, it might be useful to have many different age-based policies.</span></span> <span data-ttu-id="e7b52-130">Anstatt alle unterschiedlichen Alters basierten Richtlinien zu registrieren, die von der Anwendung benötigt werden `AuthorizationOptions` , können Sie die Richtlinien dynamisch mit einem benutzerdefinierten generieren `IAuthorizationPolicyProvider` .</span><span class="sxs-lookup"><span data-stu-id="e7b52-130">Instead of registering all the different age-based policies that the application will need in `AuthorizationOptions`, you can generate the policies dynamically with a custom `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="e7b52-131">Um die Verwendung der Richtlinien zu vereinfachen, können Sie Aktionen mit benutzerdefiniertem Autorisierungs Attribut wie mit Anmerkungen versehen `[MinimumAgeAuthorize(20)]` .</span><span class="sxs-lookup"><span data-stu-id="e7b52-131">To make using the policies easier, you can annotate actions with custom authorization attribute like `[MinimumAgeAuthorize(20)]`.</span></span>

## <a name="custom-authorization-attributes"></a><span data-ttu-id="e7b52-132">Benutzerdefinierte Autorisierungs Attribute</span><span class="sxs-lookup"><span data-stu-id="e7b52-132">Custom Authorization attributes</span></span>

<span data-ttu-id="e7b52-133">Autorisierungs Richtlinien werden anhand ihrer Namen identifiziert.</span><span class="sxs-lookup"><span data-stu-id="e7b52-133">Authorization policies are identified by their names.</span></span> <span data-ttu-id="e7b52-134">Der `MinimumAgeAuthorizeAttribute` zuvor beschriebene benutzerdefinierte muss Argumente einer Zeichenfolge zuordnen, die zum Abrufen der entsprechenden Autorisierungs Richtlinie verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="e7b52-134">The custom `MinimumAgeAuthorizeAttribute` described previously needs to map arguments into a string that can be used to retrieve the corresponding authorization policy.</span></span> <span data-ttu-id="e7b52-135">Dies können Sie erreichen, indem Sie von ableiten `AuthorizeAttribute` und die- `Age` Eigenschaft in die-Eigenschaft einbinden `AuthorizeAttribute.Policy` .</span><span class="sxs-lookup"><span data-stu-id="e7b52-135">You can do this by deriving from `AuthorizeAttribute` and making the `Age` property wrap the `AuthorizeAttribute.Policy` property.</span></span>

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

<span data-ttu-id="e7b52-136">Dieser Attributtyp hat eine `Policy` Zeichenfolge, die auf dem hart codierten Präfix () basiert, `"MinimumAge"` und eine ganze Zahl, die über den Konstruktor übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="e7b52-136">This attribute type has a `Policy` string based on the hard-coded prefix (`"MinimumAge"`) and an integer passed in via the constructor.</span></span>

<span data-ttu-id="e7b52-137">Sie können es auf die gleiche Weise wie andere `Authorize` Attribute anwenden, außer dass es eine ganze Zahl als Parameter annimmt.</span><span class="sxs-lookup"><span data-stu-id="e7b52-137">You can apply it to actions in the same way as other `Authorize` attributes except that it takes an integer as a parameter.</span></span>

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a><span data-ttu-id="e7b52-138">Benutzerdefinierter iauthorizationpolicyprovider</span><span class="sxs-lookup"><span data-stu-id="e7b52-138">Custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="e7b52-139">Das benutzerdefinierte vereinfacht das `MinimumAgeAuthorizeAttribute` anfordern von Autorisierungs Richtlinien für jedes gewünschte minimal Alter.</span><span class="sxs-lookup"><span data-stu-id="e7b52-139">The custom `MinimumAgeAuthorizeAttribute` makes it easy to request authorization policies for any minimum age desired.</span></span> <span data-ttu-id="e7b52-140">Das nächste Problem, das gelöst werden muss, besteht darin, sicherzustellen, dass Autorisierungs Richtlinien für alle unterschiedlichen Alters Bereiche verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="e7b52-140">The next problem to solve is making sure that authorization policies are available for all of those different ages.</span></span> <span data-ttu-id="e7b52-141">An dieser Stelle `IAuthorizationPolicyProvider` ist ein nützlich.</span><span class="sxs-lookup"><span data-stu-id="e7b52-141">This is where an `IAuthorizationPolicyProvider` is useful.</span></span>

<span data-ttu-id="e7b52-142">Bei Verwendung `MinimumAgeAuthorizationAttribute` von folgen die Namen der Autorisierungs Richtlinien dem Muster `"MinimumAge" + Age` , sodass die benutzerdefinierte `IAuthorizationPolicyProvider` Autorisierungs Richtlinien generieren soll:</span><span class="sxs-lookup"><span data-stu-id="e7b52-142">When using `MinimumAgeAuthorizationAttribute`, the authorization policy names will follow the pattern `"MinimumAge" + Age`, so the custom `IAuthorizationPolicyProvider` should generate authorization policies by:</span></span>

* <span data-ttu-id="e7b52-143">Das Alter wird anhand des Richtlinien namens verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="e7b52-143">Parsing the age from the policy name.</span></span>
* <span data-ttu-id="e7b52-144">Verwenden `AuthorizationPolicyBuilder` von zum Erstellen eines neuen`AuthorizationPolicy`</span><span class="sxs-lookup"><span data-stu-id="e7b52-144">Using `AuthorizationPolicyBuilder` to create a new `AuthorizationPolicy`</span></span>
* <span data-ttu-id="e7b52-145">In diesem und den folgenden Beispielen wird davon ausgegangen, dass der Benutzer über ein Cookie authentifiziert wird.</span><span class="sxs-lookup"><span data-stu-id="e7b52-145">In this and following examples it will be assumed that the user is authenticated via a cookie.</span></span> <span data-ttu-id="e7b52-146">`AuthorizationPolicyBuilder`Muss entweder mit mindestens einem Autorisierungs Schema Namen erstellt werden oder immer erfolgreich sein.</span><span class="sxs-lookup"><span data-stu-id="e7b52-146">The `AuthorizationPolicyBuilder` should either be constructed with at least one authorization scheme name or always succeed.</span></span> <span data-ttu-id="e7b52-147">Andernfalls gibt es keine Informationen zum Bereitstellen einer Herausforderung für den Benutzer, und es wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="e7b52-147">Otherwise there is no information on how to provide a challenge to the user and an exception will be thrown.</span></span>
* <span data-ttu-id="e7b52-148">Hinzufügen von Anforderungen zur Richtlinie basierend auf dem Alter mit `AuthorizationPolicyBuilder.AddRequirements` .</span><span class="sxs-lookup"><span data-stu-id="e7b52-148">Adding requirements to the policy based on the age with `AuthorizationPolicyBuilder.AddRequirements`.</span></span> <span data-ttu-id="e7b52-149">In anderen Szenarien können Sie `RequireClaim` `RequireRole` stattdessen, oder verwenden `RequireUserName` .</span><span class="sxs-lookup"><span data-stu-id="e7b52-149">In other scenarios, you might use `RequireClaim`, `RequireRole`, or `RequireUserName` instead.</span></span>

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

## <a name="multiple-authorization-policy-providers"></a><span data-ttu-id="e7b52-150">Mehrere Autorisierungs Richtlinien Anbieter</span><span class="sxs-lookup"><span data-stu-id="e7b52-150">Multiple authorization policy providers</span></span>

<span data-ttu-id="e7b52-151">Beachten Sie bei der Verwendung von benutzerdefinierten `IAuthorizationPolicyProvider` Implementierungen, dass ASP.net Core nur eine Instanz von verwendet `IAuthorizationPolicyProvider` .</span><span class="sxs-lookup"><span data-stu-id="e7b52-151">When using custom `IAuthorizationPolicyProvider` implementations, keep in mind that ASP.NET Core only uses one instance of `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="e7b52-152">Wenn ein benutzerdefinierter Anbieter keine Autorisierungs Richtlinien für alle zu verwendenden Richtlinien Namen bereitstellen kann, sollte er an einen Sicherungs Anbieter zurückgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="e7b52-152">If a custom provider isn't able to provide authorization policies for all policy names that will be used, it should defer to a backup provider.</span></span> 

<span data-ttu-id="e7b52-153">Stellen Sie sich beispielsweise eine Anwendung vor, die sowohl benutzerdefinierte Alters Richtlinien als auch herkömmlicheren rollenbasierten Richtlinien Abruf benötigt.</span><span class="sxs-lookup"><span data-stu-id="e7b52-153">For example, consider an application that needs both custom age policies and more traditional role-based policy retrieval.</span></span> <span data-ttu-id="e7b52-154">Eine solche APP könnte einen benutzerdefinierten Autorisierungs Richtlinien Anbieter verwenden, der Folgendes:</span><span class="sxs-lookup"><span data-stu-id="e7b52-154">Such an app could use a custom authorization policy provider that:</span></span>

* <span data-ttu-id="e7b52-155">Versucht, Richtlinien Namen zu analysieren.</span><span class="sxs-lookup"><span data-stu-id="e7b52-155">Attempts to parse policy names.</span></span> 
* <span data-ttu-id="e7b52-156">Ruft einen anderen Richtlinien Anbieter auf (z `DefaultAuthorizationPolicyProvider` . b.), wenn der Richtlinien Name kein Alter enthält.</span><span class="sxs-lookup"><span data-stu-id="e7b52-156">Calls into a different policy provider (like `DefaultAuthorizationPolicyProvider`) if the policy name doesn't contain an age.</span></span>

<span data-ttu-id="e7b52-157">Die `IAuthorizationPolicyProvider` oben gezeigte Beispiel Implementierung kann aktualisiert werden, um die zu verwenden, `DefaultAuthorizationPolicyProvider` indem ein Sicherungs Richtlinien Anbieter im Konstruktor erstellt wird (für den Fall, dass der Richtlinien Name nicht mit dem erwarteten Muster von "minimumAge" + Age identisch ist).</span><span class="sxs-lookup"><span data-stu-id="e7b52-157">The example `IAuthorizationPolicyProvider` implementation shown above can be updated to use the `DefaultAuthorizationPolicyProvider` by creating a backup policy provider in its constructor (to be used in case the policy name doesn't match its expected pattern of 'MinimumAge' + age).</span></span>

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

<span data-ttu-id="e7b52-158">Anschließend kann die- `GetPolicyAsync` Methode so aktualisiert werden, dass `BackupPolicyProvider` anstelle von NULL verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="e7b52-158">Then, the `GetPolicyAsync` method can be updated to use the `BackupPolicyProvider` instead of returning null:</span></span>

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a><span data-ttu-id="e7b52-159">Standardrichtlinie</span><span class="sxs-lookup"><span data-stu-id="e7b52-159">Default policy</span></span>

<span data-ttu-id="e7b52-160">Zusätzlich zur Bereitstellung benannter Autorisierungs Richtlinien muss ein benutzerdefiniertes `IAuthorizationPolicyProvider` implementieren, `GetDefaultPolicyAsync` um eine Autorisierungs Richtlinie für Attribute bereitzustellen, `[Authorize]` ohne dass ein Richtlinien Name angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="e7b52-160">In addition to providing named authorization policies, a custom `IAuthorizationPolicyProvider` needs to implement `GetDefaultPolicyAsync` to provide an authorization policy for `[Authorize]` attributes without a policy name specified.</span></span>

<span data-ttu-id="e7b52-161">In vielen Fällen erfordert dieses Autorisierungs Attribut nur einen authentifizierten Benutzer, sodass Sie die erforderliche Richtlinie mit einem-Befehl erstellen können `RequireAuthenticatedUser` :</span><span class="sxs-lookup"><span data-stu-id="e7b52-161">In many cases, this authorization attribute only requires an authenticated user, so you can make the necessary policy with a call to `RequireAuthenticatedUser`:</span></span>

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

<span data-ttu-id="e7b52-162">Wie bei allen Aspekten eines benutzerdefinierten `IAuthorizationPolicyProvider` können Sie dies nach Bedarf anpassen.</span><span class="sxs-lookup"><span data-stu-id="e7b52-162">As with all aspects of a custom `IAuthorizationPolicyProvider`, you can customize this, as needed.</span></span> <span data-ttu-id="e7b52-163">In einigen Fällen kann es wünschenswert sein, die Standard Richtlinie von einem Fall Back abzurufen `IAuthorizationPolicyProvider` .</span><span class="sxs-lookup"><span data-stu-id="e7b52-163">In some cases, it may be desirable to retrieve the default policy from a fallback `IAuthorizationPolicyProvider`.</span></span>

## <a name="fallback-policy"></a><span data-ttu-id="e7b52-164">Fallback-Richtlinie</span><span class="sxs-lookup"><span data-stu-id="e7b52-164">Fallback policy</span></span>

<span data-ttu-id="e7b52-165">Ein benutzerdefiniertes `IAuthorizationPolicyProvider` kann optional implementieren `GetFallbackPolicyAsync` , um eine Richtlinie bereitzustellen, die beim [Kombinieren von Richtlinien](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) verwendet wird, und wenn keine Richtlinien</span><span class="sxs-lookup"><span data-stu-id="e7b52-165">A custom `IAuthorizationPolicyProvider` can optionally implement `GetFallbackPolicyAsync` to provide a policy that's used when [combining policies](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) and when no policies are specified.</span></span> <span data-ttu-id="e7b52-166">Wenn `GetFallbackPolicyAsync` eine Richtlinie ungleich NULL zurückgibt, wird die zurückgegebene Richtlinie von der Autorisierungs Middleware verwendet, wenn keine Richtlinien für die Anforderung angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="e7b52-166">If `GetFallbackPolicyAsync` returns a non-null policy, the returned policy is used by the Authorization Middleware when no policies are specified for the request.</span></span>

<span data-ttu-id="e7b52-167">Wenn keine Fall Back Richtlinie erforderlich ist, kann der Anbieter `null` den Fall Back Anbieter zurückgeben oder Zurückstellen:</span><span class="sxs-lookup"><span data-stu-id="e7b52-167">If no fallback policy is required, the provider can return `null` or defer to the fallback provider:</span></span>

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

<a name="ci"></a>

## <a name="use-a-custom-iauthorizationpolicyprovider"></a><span data-ttu-id="e7b52-168">Verwenden eines benutzerdefinierten iauthorizationpolicyprovider</span><span class="sxs-lookup"><span data-stu-id="e7b52-168">Use a custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="e7b52-169">Zum Verwenden von benutzerdefinierten Richtlinien aus einer `IAuthorizationPolicyProvider` ***müssen***Sie folgende Schritte ausführen:</span><span class="sxs-lookup"><span data-stu-id="e7b52-169">To use custom policies from an `IAuthorizationPolicyProvider`, you ***must***:</span></span>

* <span data-ttu-id="e7b52-170">Registrieren der entsprechenden `AuthorizationHandler` Typen mit Abhängigkeitsinjektion (in [Richtlinien basierter Autorisierung](xref:security/authorization/policies#authorization-handlers)beschrieben), wie bei allen Richtlinien basierten Autorisierungs Szenarios.</span><span class="sxs-lookup"><span data-stu-id="e7b52-170">Register the appropriate `AuthorizationHandler` types with dependency injection (described in [policy-based authorization](xref:security/authorization/policies#authorization-handlers)), as with all policy-based authorization scenarios.</span></span>
* <span data-ttu-id="e7b52-171">Registrieren Sie den benutzerdefinierten `IAuthorizationPolicyProvider` Typ in der Abhängigkeitsinjektion-Dienst Auflistung von der app in `Startup.ConfigureServices` , um den Standardrichtlinien Anbieter zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="e7b52-171">Register the custom `IAuthorizationPolicyProvider` type in the app's dependency injection service collection in `Startup.ConfigureServices` to replace the default policy provider.</span></span>

  ```csharp
  services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
  ```

<span data-ttu-id="e7b52-172">Eine komplette benutzerdefinierte `IAuthorizationPolicyProvider` Stichprobe finden Sie im [GitHub-Repository dotnet/aspnetcore](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider).</span><span class="sxs-lookup"><span data-stu-id="e7b52-172">A complete custom `IAuthorizationPolicyProvider` sample is available in the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider).</span></span>
