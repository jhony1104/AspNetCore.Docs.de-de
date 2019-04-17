---
title: Anbieter von benutzerdefinierten Autorisierungsrichtlinien in ASP.NET Core
author: mjrousos
description: Erfahren Sie, wie eine benutzerdefinierte IAuthorizationPolicyProvider in einer ASP.NET Core-app zu verwenden, um Autorisierungsrichtlinien dynamisch zu generieren.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: e17372bb0ec9091c385a70b1e907eaa3cff24003
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614408"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a><span data-ttu-id="f4114-103">Benutzerdefinierten Autorisierungsanbieter für Richtlinie mithilfe von IAuthorizationPolicyProvider in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f4114-103">Custom Authorization Policy Providers using IAuthorizationPolicyProvider in ASP.NET Core</span></span> 

<span data-ttu-id="f4114-104">Durch [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="f4114-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="f4114-105">In der Regel bei Verwendung [richtlinienbasierte Autorisierung](xref:security/authorization/policies), Richtlinien werden registriert, indem `AuthorizationOptions.AddPolicy` als Teil der Dienstkonfiguration für die Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="f4114-105">Typically when using [policy-based authorization](xref:security/authorization/policies), policies are registered by calling `AuthorizationOptions.AddPolicy` as part of authorization service configuration.</span></span> <span data-ttu-id="f4114-106">In einigen Szenarien ist es möglicherweise nicht möglich (oder wünschenswert) aller Autorisierungsrichtlinien auf diese Weise zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="f4114-106">In some scenarios, it may not be possible (or desirable) to register all authorization policies in this way.</span></span> <span data-ttu-id="f4114-107">In diesen Fällen können Sie eine benutzerdefinierte `IAuthorizationPolicyProvider` steuern, wie die Autorisierungsrichtlinien angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="f4114-107">In those cases, you can use a custom `IAuthorizationPolicyProvider` to control how authorization policies are supplied.</span></span>

<span data-ttu-id="f4114-108">Beispiele für Szenarien, bei einer benutzerdefinierten [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) kann nützlich sein, gehören:</span><span class="sxs-lookup"><span data-stu-id="f4114-108">Examples of scenarios where a custom [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) may be useful include:</span></span>

* <span data-ttu-id="f4114-109">Verwenden einen externen Dienst, um die Auswertung der Richtlinie bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="f4114-109">Using an external service to provide policy evaluation.</span></span>
* <span data-ttu-id="f4114-110">Verwenden einen großen Bereich von Richtlinien (bei anderen Raum Zahlen oder Altersgruppe, z. B.), daher es nicht sinnvoll, jede einzelne Autorisierungsrichtlinie mit Hinzufügen einer `AuthorizationOptions.AddPolicy` aufrufen.</span><span class="sxs-lookup"><span data-stu-id="f4114-110">Using a large range of policies (for different room numbers or ages, for example), so it doesn’t make sense to add each individual authorization policy with an `AuthorizationOptions.AddPolicy` call.</span></span>
* <span data-ttu-id="f4114-111">Erstellen von Richtlinien zur Laufzeit basierend auf Informationen in einer externen Datenquelle (z. B. eine Datenbank) oder dynamisch Festlegen der autorisierungsanforderungen für die mithilfe eines anderen Mechanismus.</span><span class="sxs-lookup"><span data-stu-id="f4114-111">Creating policies at runtime based on information in an external data source (like a database) or determining authorization requirements dynamically through another mechanism.</span></span>

<span data-ttu-id="f4114-112">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) aus der [AspNetCore-GitHub-Repository](https://github.com/aspnet/AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="f4114-112">[View or download sample code](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) from the [AspNetCore GitHub repository](https://github.com/aspnet/AspNetCore).</span></span> <span data-ttu-id="f4114-113">Laden Sie die ZIP-Datei von Aspnet/AspNetCore-Repository herunter.</span><span class="sxs-lookup"><span data-stu-id="f4114-113">Download the aspnet/AspNetCore repository ZIP file.</span></span> <span data-ttu-id="f4114-114">Entzippen Sie die Datei aus.</span><span class="sxs-lookup"><span data-stu-id="f4114-114">Unzip the file.</span></span> <span data-ttu-id="f4114-115">Navigieren Sie zu der *Src/Security/Samples/CustomPolicyProvider* Projektordner.</span><span class="sxs-lookup"><span data-stu-id="f4114-115">Navigate to the *src/Security/samples/CustomPolicyProvider* project folder.</span></span>

## <a name="customize-policy-retrieval"></a><span data-ttu-id="f4114-116">Anpassen des Abrufens von Richtlinien</span><span class="sxs-lookup"><span data-stu-id="f4114-116">Customize policy retrieval</span></span>

<span data-ttu-id="f4114-117">ASP.NET Core-apps mit einer Implementierung der der `IAuthorizationPolicyProvider` Schnittstelle, um Autorisierungsrichtlinien abzurufen.</span><span class="sxs-lookup"><span data-stu-id="f4114-117">ASP.NET Core apps use an implementation of the `IAuthorizationPolicyProvider` interface to retrieve authorization policies.</span></span> <span data-ttu-id="f4114-118">In der Standardeinstellung [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) registriert und verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f4114-118">By default, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) is registered and used.</span></span> <span data-ttu-id="f4114-119">`DefaultAuthorizationPolicyProvider` Gibt die Richtlinien aus der `AuthorizationOptions` bereitgestellt, die ein `IServiceCollection.AddAuthorization` aufrufen.</span><span class="sxs-lookup"><span data-stu-id="f4114-119">`DefaultAuthorizationPolicyProvider` returns policies from the `AuthorizationOptions` provided in an `IServiceCollection.AddAuthorization` call.</span></span>

<span data-ttu-id="f4114-120">Sie können dieses Verhalten anpassen, indem Sie die Registrierung einer anderen `IAuthorizationPolicyProvider` Implementierung der App [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) Container.</span><span class="sxs-lookup"><span data-stu-id="f4114-120">You can customize this behavior by registering a different `IAuthorizationPolicyProvider` implementation in the app’s [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> 

<span data-ttu-id="f4114-121">Die `IAuthorizationPolicyProvider` Schnittstelle enthält zwei APIs:</span><span class="sxs-lookup"><span data-stu-id="f4114-121">The `IAuthorizationPolicyProvider` interface contains two APIs:</span></span>

* <span data-ttu-id="f4114-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) gibt eine Autorisierungsrichtlinie für einen bestimmten Namen.</span><span class="sxs-lookup"><span data-stu-id="f4114-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) returns an authorization policy for a given name.</span></span>
* <span data-ttu-id="f4114-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) gibt die Standardrichtlinie für die Autorisierung (die Richtlinie zum `[Authorize]` Attribute ohne eine Richtlinie).</span><span class="sxs-lookup"><span data-stu-id="f4114-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) returns the default authorization policy (the policy used for `[Authorize]` attributes without a policy specified).</span></span> 

<span data-ttu-id="f4114-124">Implementieren Sie diese beiden APIs, können Sie anpassen, wie die Autorisierungsrichtlinien bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="f4114-124">By implementing these two APIs, you can customize how authorization policies are provided.</span></span>

## <a name="parameterized-authorize-attribute-example"></a><span data-ttu-id="f4114-125">Parametrisierte autorisieren Sie die Beispiel-Attribut</span><span class="sxs-lookup"><span data-stu-id="f4114-125">Parameterized authorize attribute example</span></span>

<span data-ttu-id="f4114-126">Ein Szenario, in denen `IAuthorizationPolicyProvider` eignet sich besteht darin, benutzerdefinierte ermöglichen `[Authorize]` Attribute, deren Anforderungen richten sich nach einem Parameter.</span><span class="sxs-lookup"><span data-stu-id="f4114-126">One scenario where `IAuthorizationPolicyProvider` is useful is enabling custom `[Authorize]` attributes whose requirements depend on a parameter.</span></span> <span data-ttu-id="f4114-127">Z. B. in [richtlinienbasierte Autorisierung](xref:security/authorization/policies) Dokumentation, die eine Age-basierte ("AtLeast21") wurde die Richtlinie als Beispiel verwendet.</span><span class="sxs-lookup"><span data-stu-id="f4114-127">For example, in [policy-based authorization](xref:security/authorization/policies) documentation, an age-based (“AtLeast21”) policy was used as a sample.</span></span> <span data-ttu-id="f4114-128">Wenn anderen Controller-Aktionen in einer app für Benutzer verfügbar gemacht werden sollen *verschiedene* Zugriffe, es kann hilfreich sein, über viele verschiedene Age-basierte Richtlinien verfügen.</span><span class="sxs-lookup"><span data-stu-id="f4114-128">If different controller actions in an app should be made available to users of *different* ages, it might be useful to have many different age-based policies.</span></span> <span data-ttu-id="f4114-129">Statt die Registrierung aller anderen Age-basierten Richtlinien, die die Anwendung im benötigen `AuthorizationOptions`, Sie können die Richtlinien mit einem benutzerdefinierten dynamisch generieren `IAuthorizationPolicyProvider`.</span><span class="sxs-lookup"><span data-stu-id="f4114-129">Instead of registering all the different age-based policies that the application will need in `AuthorizationOptions`, you can generate the policies dynamically with a custom `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="f4114-130">Um mit den Richtlinien einfacher zu gestalten, können Sie Aktionen mit benutzerdefinierten autorisierungsattributs wie versehen `[MinimumAgeAuthorize(20)]`.</span><span class="sxs-lookup"><span data-stu-id="f4114-130">To make using the policies easier, you can annotate actions with custom authorization attribute like `[MinimumAgeAuthorize(20)]`.</span></span>

## <a name="custom-authorization-attributes"></a><span data-ttu-id="f4114-131">Benutzerdefinierte Autorisierung-Attribute</span><span class="sxs-lookup"><span data-stu-id="f4114-131">Custom Authorization attributes</span></span>

<span data-ttu-id="f4114-132">Autorisierungsrichtlinien werden anhand ihrer Namen identifiziert.</span><span class="sxs-lookup"><span data-stu-id="f4114-132">Authorization policies are identified by their names.</span></span> <span data-ttu-id="f4114-133">Die benutzerdefinierte `MinimumAgeAuthorizeAttribute` beschrieben muss zuvor Zuordnen von Argumenten in eine Zeichenfolge, die verwendet werden können, um die entsprechende Autorisierungsrichtlinie abzurufen.</span><span class="sxs-lookup"><span data-stu-id="f4114-133">The custom `MinimumAgeAuthorizeAttribute` described previously needs to map arguments into a string that can be used to retrieve the corresponding authorization policy.</span></span> <span data-ttu-id="f4114-134">Sie erreichen dies durch Ableiten von `AuthorizeAttribute` und die `Age` Eigenschaft Wrap der `AuthorizeAttribute.Policy` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="f4114-134">You can do this by deriving from `AuthorizeAttribute` and making the `Age` property wrap the `AuthorizeAttribute.Policy` property.</span></span>

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

<span data-ttu-id="f4114-135">Dieser Attributtyp verfügt über eine `Policy` Zeichenfolge auf Grundlage der fest programmierte-Präfix (`"MinimumAge"`) und eine ganze Zahl, die über den Konstruktor übergeben.</span><span class="sxs-lookup"><span data-stu-id="f4114-135">This attribute type has a `Policy` string based on the hard-coded prefix (`"MinimumAge"`) and an integer passed in via the constructor.</span></span>

<span data-ttu-id="f4114-136">Sie können Aktionen in der gleichen Weise wie andere anwenden `Authorize` Attribute, außer dass es sich um eine ganze Zahl als Parameter verwendet.</span><span class="sxs-lookup"><span data-stu-id="f4114-136">You can apply it to actions in the same way as other `Authorize` attributes except that it takes an integer as a parameter.</span></span>

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a><span data-ttu-id="f4114-137">Benutzerdefinierte IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="f4114-137">Custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="f4114-138">Die benutzerdefinierte `MinimumAgeAuthorizeAttribute` erleichtert Autorisierungsrichtlinien für die Anforderung für alle gewünschten Mindestalter.</span><span class="sxs-lookup"><span data-stu-id="f4114-138">The custom `MinimumAgeAuthorizeAttribute` makes it easy to request authorization policies for any minimum age desired.</span></span> <span data-ttu-id="f4114-139">Das nächste Problem lösen Sie sicherstellen, dass Autorisierungsrichtlinien für alle diese verschiedenen Altersgruppen verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="f4114-139">The next problem to solve is making sure that authorization policies are available for all of those different ages.</span></span> <span data-ttu-id="f4114-140">Hier kommt ein `IAuthorizationPolicyProvider` eignet.</span><span class="sxs-lookup"><span data-stu-id="f4114-140">This is where an `IAuthorizationPolicyProvider` is useful.</span></span>

<span data-ttu-id="f4114-141">Bei Verwendung `MinimumAgeAuthorizationAttribute`, Richtliniennamen Autorisierung werden das folgende Namensgebungsmuster `"MinimumAge" + Age`, sodass die benutzerdefinierte `IAuthorizationPolicyProvider` Autorisierungsrichtlinien durch generieren soll:</span><span class="sxs-lookup"><span data-stu-id="f4114-141">When using `MinimumAgeAuthorizationAttribute`, the authorization policy names will follow the pattern `"MinimumAge" + Age`, so the custom `IAuthorizationPolicyProvider` should generate authorization policies by:</span></span>

* <span data-ttu-id="f4114-142">Analysieren das Alter von den Richtliniennamen an.</span><span class="sxs-lookup"><span data-stu-id="f4114-142">Parsing the age from the policy name.</span></span>
* <span data-ttu-id="f4114-143">Mithilfe von `AuthorizationPolicyBuilder` zum Erstellen eines neuen `AuthorizationPolicy`</span><span class="sxs-lookup"><span data-stu-id="f4114-143">Using `AuthorizationPolicyBuilder` to create a new `AuthorizationPolicy`</span></span>
* <span data-ttu-id="f4114-144">Hinzufügen von Anforderungen an die Richtlinie aufgrund des Alters mit `AuthorizationPolicyBuilder.AddRequirements`.</span><span class="sxs-lookup"><span data-stu-id="f4114-144">Adding requirements to the policy based on the age with `AuthorizationPolicyBuilder.AddRequirements`.</span></span> <span data-ttu-id="f4114-145">Sie können in anderen Szenarien verwenden `RequireClaim`, `RequireRole`, oder `RequireUserName` stattdessen.</span><span class="sxs-lookup"><span data-stu-id="f4114-145">In other scenarios, you might use `RequireClaim`, `RequireRole`, or `RequireUserName` instead.</span></span>

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
            var policy = new AuthorizationPolicyBuilder();
            policy.AddRequirements(new MinimumAgeRequirement(age));
            return Task.FromResult(policy.Build());
        }

        return Task.FromResult<AuthorizationPolicy>(null);
    }
}
```

## <a name="multiple-authorization-policy-providers"></a><span data-ttu-id="f4114-146">Anbieter von mehreren Autorisierung</span><span class="sxs-lookup"><span data-stu-id="f4114-146">Multiple authorization policy providers</span></span>

<span data-ttu-id="f4114-147">Bei Verwendung von benutzerdefinierten `IAuthorizationPolicyProvider` Implementierungen, Bedenken Sie, die ASP.NET Core verwendet nur eine Instanz des `IAuthorizationPolicyProvider`.</span><span class="sxs-lookup"><span data-stu-id="f4114-147">When using custom `IAuthorizationPolicyProvider` implementations, keep in mind that ASP.NET Core only uses one instance of `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="f4114-148">Wenn Sie ein benutzerdefinierter Anbieter kann nicht auf alle Richtliniennamen Autorisierungsrichtlinien bereit, die verwendet werden, sollte es zu einem backup-Anbieter zurückgegriffen.</span><span class="sxs-lookup"><span data-stu-id="f4114-148">If a custom provider isn't able to provide authorization policies for all policy names that will be used, it should fall back to a backup provider.</span></span> 

<span data-ttu-id="f4114-149">Betrachten Sie beispielsweise eine Anwendung, die sowohl für benutzerdefinierte Alter Richtlinien als auch für Herkömmliche rollenbasierte richtlinienabrufs benötigt.</span><span class="sxs-lookup"><span data-stu-id="f4114-149">For example, consider an application that needs both custom age policies and more traditional role-based policy retrieval.</span></span> <span data-ttu-id="f4114-150">Eine solche Anwendung können einen benutzerdefinierten Autorisierungs-Richtlinienanbieter, die:</span><span class="sxs-lookup"><span data-stu-id="f4114-150">Such an app could use a custom authorization policy provider that:</span></span>

* <span data-ttu-id="f4114-151">Versucht, Richtliniennamen zu analysieren.</span><span class="sxs-lookup"><span data-stu-id="f4114-151">Attempts to parse policy names.</span></span> 
* <span data-ttu-id="f4114-152">Aufrufe an eine andere Richtlinie-Anbieter (z. B. `DefaultAuthorizationPolicyProvider`) Wenn der Richtlinienname nicht Alter enthält.</span><span class="sxs-lookup"><span data-stu-id="f4114-152">Calls into a different policy provider (like `DefaultAuthorizationPolicyProvider`) if the policy name doesn't contain an age.</span></span>

<span data-ttu-id="f4114-153">Im Beispiel `IAuthorizationPolicyProvider` obenstehend dargestellte Implementierung kann aktualisiert werden, damit die `DefaultAuthorizationPolicyProvider` durch Erstellen eines fallback-Richtlinie-Anbieters in seinem Konstruktor (auf verwendet werden, für den Fall, dass Sie der Namen der Richtlinie nicht mit der erwarteten Muster "MinimumAge" + Age übereinstimmt).</span><span class="sxs-lookup"><span data-stu-id="f4114-153">The example `IAuthorizationPolicyProvider` implementation shown above can be updated to use the `DefaultAuthorizationPolicyProvider` by creating a fallback policy provider in its constructor (to be used in case the policy name doesn't match its expected pattern of 'MinimumAge' + age).</span></span>

```csharp
private DefaultAuthorizationPolicyProvider FallbackPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    FallbackPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

<span data-ttu-id="f4114-154">Anschließend wird die `GetPolicyAsync` Methode kann aktualisiert werden, damit die `FallbackPolicyProvider` anstelle von Null:</span><span class="sxs-lookup"><span data-stu-id="f4114-154">Then, the `GetPolicyAsync` method can be updated to use the `FallbackPolicyProvider` instead of returning null:</span></span>

```csharp
...
return FallbackPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a><span data-ttu-id="f4114-155">Standardrichtlinie</span><span class="sxs-lookup"><span data-stu-id="f4114-155">Default policy</span></span>

<span data-ttu-id="f4114-156">Zusätzlich zur Bereitstellung von benannten Autorisierungsrichtlinien, die eine benutzerdefinierte `IAuthorizationPolicyProvider` implementiert werden muss `GetDefaultPolicyAsync` , geben Sie eine Autorisierungsrichtlinie für `[Authorize]` Attribute ohne ein Richtlinienname angegeben.</span><span class="sxs-lookup"><span data-stu-id="f4114-156">In addition to providing named authorization policies, a custom `IAuthorizationPolicyProvider` needs to implement `GetDefaultPolicyAsync` to provide an authorization policy for `[Authorize]` attributes without a policy name specified.</span></span>

<span data-ttu-id="f4114-157">In vielen Fällen ein authentifiziertes Benutzers, dieses Autorisierungsattribut nur erfordert, damit Sie die erforderlichen Richtlinie mit einem Aufruf von treffen können `RequireAuthenticatedUser`:</span><span class="sxs-lookup"><span data-stu-id="f4114-157">In many cases, this authorization attribute only requires an authenticated user, so you can make the necessary policy with a call to `RequireAuthenticatedUser`:</span></span>

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder().RequireAuthenticatedUser().Build());
```

<span data-ttu-id="f4114-158">Wie bei der alle Aspekte einer benutzerdefinierten `IAuthorizationPolicyProvider`, Sie können dies, je nach Bedarf anpassen.</span><span class="sxs-lookup"><span data-stu-id="f4114-158">As with all aspects of a custom `IAuthorizationPolicyProvider`, you can customize this, as needed.</span></span> <span data-ttu-id="f4114-159">In einigen Fällen ist es möglicherweise wünschenswert, zum Abrufen der Standardrichtlinie von Fallback `IAuthorizationPolicyProvider`.</span><span class="sxs-lookup"><span data-stu-id="f4114-159">In some cases, it may be desirable to retrieve the default policy from a fallback `IAuthorizationPolicyProvider`.</span></span>

## <a name="required-policy"></a><span data-ttu-id="f4114-160">Erforderliche Richtlinie</span><span class="sxs-lookup"><span data-stu-id="f4114-160">Required policy</span></span>

<span data-ttu-id="f4114-161">Eine benutzerdefinierte `IAuthorizationPolicyProvider` implementiert werden muss `GetRequiredPolicyAsync` , geben Sie optional eine Richtlinie, die immer erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="f4114-161">A custom `IAuthorizationPolicyProvider` needs to implement `GetRequiredPolicyAsync` to, optionally, provide a policy that is always required.</span></span> <span data-ttu-id="f4114-162">Wenn `GetRequiredPolicyAsync` eine Richtlinie ungleich Null, gibt die Richtlinie wird bei einem anderen (benannt oder Standard) kombiniert werden-Richtlinie, die angefordert wird.</span><span class="sxs-lookup"><span data-stu-id="f4114-162">If `GetRequiredPolicyAsync` returns a non-null policy, that policy will be combined with any other (named or default) policy that is requested.</span></span>

<span data-ttu-id="f4114-163">Wenn keine erforderliche Richtlinie erforderlich ist, kann nur der Anbieter gibt null zurück, oder basierend auf den fallback-Anbieter:</span><span class="sxs-lookup"><span data-stu-id="f4114-163">If no required policy is needed, the provider can just return null or defer to the fallback provider:</span></span>

```csharp
public Task<AuthorizationPolicy> GetRequiredPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a><span data-ttu-id="f4114-164">Verwenden Sie eine benutzerdefinierte IAuthorizationPolicyProvider</span><span class="sxs-lookup"><span data-stu-id="f4114-164">Use a custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="f4114-165">Zum Verwenden benutzerdefinierter Richtlinien aus einer `IAuthorizationPolicyProvider`, müssen Sie:</span><span class="sxs-lookup"><span data-stu-id="f4114-165">To use custom policies from an `IAuthorizationPolicyProvider`, you must:</span></span>

* <span data-ttu-id="f4114-166">Registrieren Sie die entsprechende `AuthorizationHandler` Typen mit Dependency Injection (beschrieben [richtlinienbasierte Autorisierung](xref:security/authorization/policies#authorization-handlers)), wie bei allen richtlinienbasierte Autorisierung-Szenarien.</span><span class="sxs-lookup"><span data-stu-id="f4114-166">Register the appropriate `AuthorizationHandler` types with dependency injection (described in [policy-based authorization](xref:security/authorization/policies#authorization-handlers)), as with all policy-based authorization scenarios.</span></span>
* <span data-ttu-id="f4114-167">Registrieren Sie die benutzerdefinierte `IAuthorizationPolicyProvider` Typ in der app Dependency Injection-Dienst-Auflistung (in `Startup.ConfigureServices`) um den Standardanbieter für die Richtlinie zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="f4114-167">Register the custom `IAuthorizationPolicyProvider` type in the app's dependency injection service collection (in `Startup.ConfigureServices`) to replace the default policy provider.</span></span>

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

<span data-ttu-id="f4114-168">Eine vollständige benutzerdefinierte `IAuthorizationPolicyProvider` Beispiel steht in der [Aspnet/AuthSamples GitHub-Repository](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider).</span><span class="sxs-lookup"><span data-stu-id="f4114-168">A complete custom `IAuthorizationPolicyProvider` sample is available in the [aspnet/AuthSamples GitHub repository](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider).</span></span>
