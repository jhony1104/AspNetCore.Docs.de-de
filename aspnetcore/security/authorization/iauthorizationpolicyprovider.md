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
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>Benutzerdefinierte Autorisierungsrichtlinienanbieter, die IAuthorizationPolicyProvider in ASP.NET Core verwenden 

Von [Mike Rousos](https://github.com/mjrousos)

In der Regel werden Richtlinien bei der `AuthorizationOptions.AddPolicy` Verwendung [von richtlinienbasierter Autorisierung](xref:security/authorization/policies)durch Aufruf als Teil der Autorisierungsdienstkonfiguration registriert. In einigen Szenarien ist es möglicherweise nicht möglich (oder wünschenswert), alle Autorisierungsrichtlinien auf diese Weise zu registrieren. In diesen Fällen können Sie `IAuthorizationPolicyProvider` mithilfe einer benutzerdefinierten Verwendung steuern, wie Autorisierungsrichtlinien bereitgestellt werden.

Beispiele für Szenarien, in denen ein benutzerdefinierter [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) nützlich sein kann:

* Verwenden eines externen Dienstes zum Bereitstellen einer Richtlinienbewertung.
* Verwenden einer großen Auswahl an Richtlinien (z. B. für unterschiedliche Raumnummern oder Altersgruppen), ist `AuthorizationOptions.AddPolicy` es daher nicht sinnvoll, jede einzelne Autorisierungsrichtlinie mit einem Anruf hinzuzufügen.
* Erstellen von Richtlinien zur Laufzeit basierend auf Informationen in einer externen Datenquelle (z. B. einer Datenbank) oder dynamisches Bestimmen von Autorisierungsanforderungen über einen anderen Mechanismus.

[Beispielcode](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) aus dem [AspNetCore GitHub-Repository](https://github.com/dotnet/AspNetCore)anzeigen oder herunterladen. Laden Sie die ZIP-Datei dotnet/AspNetCore Repository herunter. Entpacken Sie die Datei. Navigieren Sie zum Projektordner *src/Security/samples/CustomPolicyProvider.*

## <a name="customize-policy-retrieval"></a>Anpassen des Richtlinienabrufs

ASP.NET Core-Apps verwenden `IAuthorizationPolicyProvider` eine Implementierung der Schnittstelle, um Autorisierungsrichtlinien abzurufen. Standardmäßig wird [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) registriert und verwendet. `DefaultAuthorizationPolicyProvider`gibt Richtlinien `AuthorizationOptions` aus dem `IServiceCollection.AddAuthorization` in einem Anruf bereitgestellten zurück.

Passen Sie dieses Verhalten `IAuthorizationPolicyProvider` an, indem Sie eine andere Implementierung im [Abhängigkeitsinjektionscontainer](xref:fundamentals/dependency-injection) der App registrieren. 

Die `IAuthorizationPolicyProvider` Schnittstelle enthält drei APIs:

* [GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) gibt eine Autorisierungsrichtlinie für einen bestimmten Namen zurück.
* [GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) gibt die Standardautorisierungsrichtlinie `[Authorize]` zurück (die Richtlinie, die für Attribute ohne angegebene Richtlinie verwendet wird). 
* [GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) gibt die Fallbackautorisierungsrichtlinie zurück (die Richtlinie, die von der Autorisierungsmiddleware verwendet wird, wenn keine Richtlinie angegeben ist). 

Durch Implementieren dieser APIs können Sie anpassen, wie Autorisierungsrichtlinien bereitgestellt werden.

## <a name="parameterized-authorize-attribute-example"></a>Parameterisiertes Authorize-Attributbeispiel

Ein Szenario, in `IAuthorizationPolicyProvider` dem `[Authorize]` nützlich ist, ist das Aktivieren benutzerdefinierter Attribute, deren Anforderungen von einem Parameter abhängen. In der [richtlinienbasierten Autorisierungsdokumentation](xref:security/authorization/policies) wurde beispielsweise eine altersbasierte Richtlinie ("AtLeast21") als Beispiel verwendet. Wenn Benutzern unterschiedlicher Controlleraktionen in einer App Benutzern *unterschiedlichen* Alters zur Verfügung gestellt werden sollten, kann es nützlich sein, viele verschiedene altersbasierte Richtlinien zu haben. Anstatt alle verschiedenen altersbasierten Richtlinien zu registrieren, die `AuthorizationOptions`die Anwendung in benötigt, `IAuthorizationPolicyProvider`können Sie die Richtlinien dynamisch mit einer benutzerdefinierten generieren. Um die Verwendung der Richtlinien zu vereinfachen, können Sie `[MinimumAgeAuthorize(20)]`Aktionen mit benutzerdefinierten Autorisierungsattributen wie anken lassen.

## <a name="custom-authorization-attributes"></a>Benutzerdefinierte Autorisierungsattribute

Autorisierungsrichtlinien werden durch ihre Namen identifiziert. Die `MinimumAgeAuthorizeAttribute` zuvor beschriebene benutzerdefinierte Muss Argumente einer Zeichenfolge zuordnen, die zum Abrufen der entsprechenden Autorisierungsrichtlinie verwendet werden kann. Sie können dies tun, `AuthorizeAttribute` indem `Age` Sie von `AuthorizeAttribute.Policy` der Unterkunft ableiten und die Eigenschaft dazu bringen, die Eigenschaft zu umschließen.

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

Dieser Attributtyp `Policy` verfügt über eine Zeichenfolge, die`"MinimumAge"`auf dem hartcodierten Präfix ( ) basiert, und eine ganze Zahl, die über den Konstruktor übergeben wird.

Sie können sie auf Aktionen auf `Authorize` die gleiche Weise wie andere Attribute anwenden, mit der Ausnahme, dass eine ganze Zahl als Parameter verwendet wird.

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>Benutzerdefinierter IAuthorizationPolicyProvider

Die `MinimumAgeAuthorizeAttribute` Benutzerdefinierte macht es einfach, Autorisierungsrichtlinien für jedes gewünschte Mindestalter anzufordern. Das nächste Problem, das gelöst werden muss, besteht darin, sicherzustellen, dass Autorisierungsrichtlinien für alle unterschiedlichen Altersgruppen verfügbar sind. Dies ist, wo eine `IAuthorizationPolicyProvider` nützlich ist.

Bei `MinimumAgeAuthorizationAttribute`verwendung folgen die Autorisierungsrichtliniennamen dem `"MinimumAge" + Age` `IAuthorizationPolicyProvider` Muster , daher sollte die benutzerdefinierte Autorisierungsrichtlinien generieren, indem sie:

* Analysieren des Alters anhand des Richtliniennamens.
* Verwenden `AuthorizationPolicyBuilder` zum Erstellen eines neuen`AuthorizationPolicy`
* In diesem und folgenden Beispielen wird davon ausgegangen, dass der Benutzer über ein Cookie authentifiziert wird. Der `AuthorizationPolicyBuilder` sollte entweder mit mindestens einem Berechtigungsschemanamen erstellt werden oder immer erfolgreich sein. Andernfalls gibt es keine Informationen darüber, wie eine Herausforderung für den Benutzer zur Verfügung gestellt werden kann, und es wird eine Ausnahme ausgelöst.
* Hinzufügen von Anforderungen zur Richtlinie `AuthorizationPolicyBuilder.AddRequirements`basierend auf dem Alter mit . In anderen Szenarien können `RequireClaim` `RequireRole`Sie `RequireUserName` stattdessen , oder verwenden.

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

## <a name="multiple-authorization-policy-providers"></a>Mehrere Autorisierungsrichtlinienanbieter

Beachten Sie `IAuthorizationPolicyProvider` bei der Verwendung benutzerdefinierter Implementierungen, `IAuthorizationPolicyProvider`dass ASP.NET Core nur eine Instanz von verwendet. Wenn ein benutzerdefinierter Anbieter nicht in der Lage ist, Autorisierungsrichtlinien für alle verwendeten Richtliniennamen bereitzustellen, sollte er an einen Sicherungsanbieter zurücksetzen. 

Betrachten Sie beispielsweise eine Anwendung, die sowohl benutzerdefinierte Altersrichtlinien als auch traditionellere rollenbasierte Richtlinienabruferfordert. Eine solche App könnte einen benutzerdefinierten Autorisierungsrichtlinienanbieter verwenden, der:

* Versucht, Richtliniennamen zu analysieren. 
* Ruft einen anderen Richtlinienanbieter `DefaultAuthorizationPolicyProvider`(z. B.) auf, wenn der Richtlinienname kein Alter enthält.

Die `IAuthorizationPolicyProvider` oben gezeigte Beispielimplementierung kann `DefaultAuthorizationPolicyProvider` aktualisiert werden, um die zu verwenden, indem ein Sicherungsrichtlinienanbieter in seinem Konstruktor erstellt wird (für den Fall, dass der Richtlinienname nicht mit dem erwarteten Muster von 'MinimumAge' + Alter übereinstimmt).

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

Anschließend kann `GetPolicyAsync` die Methode aktualisiert `BackupPolicyProvider` werden, um die anstelle der Rückgabe null zu verwenden:

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>Standardrichtlinie

Zusätzlich zur Bereitstellung benannter Autorisierungsrichtlinien muss eine benutzerdefinierte `IAuthorizationPolicyProvider` Person implementiert werden, `GetDefaultPolicyAsync` um eine Autorisierungsrichtlinie für `[Authorize]` Attribute ohne angegebenen Richtliniennamen bereitzustellen.

In vielen Fällen erfordert dieses Autorisierungsattribut nur einen authentifizierten Benutzer, sodass Sie die erforderliche Richtlinie mit einem Aufruf von: `RequireAuthenticatedUser`

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

Wie bei allen Aspekten `IAuthorizationPolicyProvider`einer benutzerdefinierte, können Sie dies anpassen, nach Bedarf. In einigen Fällen kann es wünschenswert sein, die `IAuthorizationPolicyProvider`Standardrichtlinie aus einem Fallback abzurufen.

## <a name="fallback-policy"></a>Fallback-Politik

Eine `IAuthorizationPolicyProvider` benutzerdefinierte Benutzerkannien kann optional implementiert `GetFallbackPolicyAsync` werden, um eine Richtlinie bereitzustellen, die beim Kombinieren von [Richtlinien](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) und beim Angeben von Richtlinien verwendet wird. Wenn `GetFallbackPolicyAsync` eine Richtlinie für nicht NULL zurückgegeben wird, wird die zurückgegebene Richtlinie von der Autorisierungsmiddleware verwendet, wenn keine Richtlinien für die Anforderung angegeben werden.

Wenn keine Fallbackrichtlinie erforderlich ist, `null` kann der Anbieter den Fallbackanbieter zurückgeben oder zurückstellen:

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>Verwenden eines benutzerdefinierten IAuthorizationPolicyProvider

Um benutzerdefinierte Richtlinien `IAuthorizationPolicyProvider`aus einem zu verwenden, müssen Sie:

* Registrieren Sie `AuthorizationHandler` die entsprechenden Typen mit Abhängigkeitsinjektion (beschrieben in [der richtlinienbasierten Autorisierung](xref:security/authorization/policies#authorization-handlers)), wie bei allen richtlinienbasierten Autorisierungsszenarien.
* Registrieren Sie `IAuthorizationPolicyProvider` den benutzerdefinierten Typ in der `Startup.ConfigureServices`Abhängigkeitsinjektionsdienstauflistung (in ) der App, um den Standardrichtlinienanbieter zu ersetzen.

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

Ein vollständiges benutzerdefiniertes `IAuthorizationPolicyProvider` Beispiel ist im [dotnet/aspnetcore GitHub-Repository](https://github.com/dotnet/aspnetcore/tree/ea555458dc61e04314598c25b3ab8c56362a5123/src/Security/samples/CustomPolicyProvider)verfügbar.
