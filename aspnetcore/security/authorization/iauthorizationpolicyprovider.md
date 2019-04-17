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
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>Benutzerdefinierten Autorisierungsanbieter für Richtlinie mithilfe von IAuthorizationPolicyProvider in ASP.NET Core 

Durch [Mike Rousos](https://github.com/mjrousos)

In der Regel bei Verwendung [richtlinienbasierte Autorisierung](xref:security/authorization/policies), Richtlinien werden registriert, indem `AuthorizationOptions.AddPolicy` als Teil der Dienstkonfiguration für die Autorisierung. In einigen Szenarien ist es möglicherweise nicht möglich (oder wünschenswert) aller Autorisierungsrichtlinien auf diese Weise zu registrieren. In diesen Fällen können Sie eine benutzerdefinierte `IAuthorizationPolicyProvider` steuern, wie die Autorisierungsrichtlinien angegeben werden.

Beispiele für Szenarien, bei einer benutzerdefinierten [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) kann nützlich sein, gehören:

* Verwenden einen externen Dienst, um die Auswertung der Richtlinie bereitstellen.
* Verwenden einen großen Bereich von Richtlinien (bei anderen Raum Zahlen oder Altersgruppe, z. B.), daher es nicht sinnvoll, jede einzelne Autorisierungsrichtlinie mit Hinzufügen einer `AuthorizationOptions.AddPolicy` aufrufen.
* Erstellen von Richtlinien zur Laufzeit basierend auf Informationen in einer externen Datenquelle (z. B. eine Datenbank) oder dynamisch Festlegen der autorisierungsanforderungen für die mithilfe eines anderen Mechanismus.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) aus der [AspNetCore-GitHub-Repository](https://github.com/aspnet/AspNetCore). Laden Sie die ZIP-Datei von Aspnet/AspNetCore-Repository herunter. Entzippen Sie die Datei aus. Navigieren Sie zu der *Src/Security/Samples/CustomPolicyProvider* Projektordner.

## <a name="customize-policy-retrieval"></a>Anpassen des Abrufens von Richtlinien

ASP.NET Core-apps mit einer Implementierung der der `IAuthorizationPolicyProvider` Schnittstelle, um Autorisierungsrichtlinien abzurufen. In der Standardeinstellung [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) registriert und verwendet wird. `DefaultAuthorizationPolicyProvider` Gibt die Richtlinien aus der `AuthorizationOptions` bereitgestellt, die ein `IServiceCollection.AddAuthorization` aufrufen.

Sie können dieses Verhalten anpassen, indem Sie die Registrierung einer anderen `IAuthorizationPolicyProvider` Implementierung der App [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) Container. 

Die `IAuthorizationPolicyProvider` Schnittstelle enthält zwei APIs:

* [GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) gibt eine Autorisierungsrichtlinie für einen bestimmten Namen.
* [GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) gibt die Standardrichtlinie für die Autorisierung (die Richtlinie zum `[Authorize]` Attribute ohne eine Richtlinie). 

Implementieren Sie diese beiden APIs, können Sie anpassen, wie die Autorisierungsrichtlinien bereitgestellt werden.

## <a name="parameterized-authorize-attribute-example"></a>Parametrisierte autorisieren Sie die Beispiel-Attribut

Ein Szenario, in denen `IAuthorizationPolicyProvider` eignet sich besteht darin, benutzerdefinierte ermöglichen `[Authorize]` Attribute, deren Anforderungen richten sich nach einem Parameter. Z. B. in [richtlinienbasierte Autorisierung](xref:security/authorization/policies) Dokumentation, die eine Age-basierte ("AtLeast21") wurde die Richtlinie als Beispiel verwendet. Wenn anderen Controller-Aktionen in einer app für Benutzer verfügbar gemacht werden sollen *verschiedene* Zugriffe, es kann hilfreich sein, über viele verschiedene Age-basierte Richtlinien verfügen. Statt die Registrierung aller anderen Age-basierten Richtlinien, die die Anwendung im benötigen `AuthorizationOptions`, Sie können die Richtlinien mit einem benutzerdefinierten dynamisch generieren `IAuthorizationPolicyProvider`. Um mit den Richtlinien einfacher zu gestalten, können Sie Aktionen mit benutzerdefinierten autorisierungsattributs wie versehen `[MinimumAgeAuthorize(20)]`.

## <a name="custom-authorization-attributes"></a>Benutzerdefinierte Autorisierung-Attribute

Autorisierungsrichtlinien werden anhand ihrer Namen identifiziert. Die benutzerdefinierte `MinimumAgeAuthorizeAttribute` beschrieben muss zuvor Zuordnen von Argumenten in eine Zeichenfolge, die verwendet werden können, um die entsprechende Autorisierungsrichtlinie abzurufen. Sie erreichen dies durch Ableiten von `AuthorizeAttribute` und die `Age` Eigenschaft Wrap der `AuthorizeAttribute.Policy` Eigenschaft.

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

Dieser Attributtyp verfügt über eine `Policy` Zeichenfolge auf Grundlage der fest programmierte-Präfix (`"MinimumAge"`) und eine ganze Zahl, die über den Konstruktor übergeben.

Sie können Aktionen in der gleichen Weise wie andere anwenden `Authorize` Attribute, außer dass es sich um eine ganze Zahl als Parameter verwendet.

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>Benutzerdefinierte IAuthorizationPolicyProvider

Die benutzerdefinierte `MinimumAgeAuthorizeAttribute` erleichtert Autorisierungsrichtlinien für die Anforderung für alle gewünschten Mindestalter. Das nächste Problem lösen Sie sicherstellen, dass Autorisierungsrichtlinien für alle diese verschiedenen Altersgruppen verfügbar sind. Hier kommt ein `IAuthorizationPolicyProvider` eignet.

Bei Verwendung `MinimumAgeAuthorizationAttribute`, Richtliniennamen Autorisierung werden das folgende Namensgebungsmuster `"MinimumAge" + Age`, sodass die benutzerdefinierte `IAuthorizationPolicyProvider` Autorisierungsrichtlinien durch generieren soll:

* Analysieren das Alter von den Richtliniennamen an.
* Mithilfe von `AuthorizationPolicyBuilder` zum Erstellen eines neuen `AuthorizationPolicy`
* Hinzufügen von Anforderungen an die Richtlinie aufgrund des Alters mit `AuthorizationPolicyBuilder.AddRequirements`. Sie können in anderen Szenarien verwenden `RequireClaim`, `RequireRole`, oder `RequireUserName` stattdessen.

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

## <a name="multiple-authorization-policy-providers"></a>Anbieter von mehreren Autorisierung

Bei Verwendung von benutzerdefinierten `IAuthorizationPolicyProvider` Implementierungen, Bedenken Sie, die ASP.NET Core verwendet nur eine Instanz des `IAuthorizationPolicyProvider`. Wenn Sie ein benutzerdefinierter Anbieter kann nicht auf alle Richtliniennamen Autorisierungsrichtlinien bereit, die verwendet werden, sollte es zu einem backup-Anbieter zurückgegriffen. 

Betrachten Sie beispielsweise eine Anwendung, die sowohl für benutzerdefinierte Alter Richtlinien als auch für Herkömmliche rollenbasierte richtlinienabrufs benötigt. Eine solche Anwendung können einen benutzerdefinierten Autorisierungs-Richtlinienanbieter, die:

* Versucht, Richtliniennamen zu analysieren. 
* Aufrufe an eine andere Richtlinie-Anbieter (z. B. `DefaultAuthorizationPolicyProvider`) Wenn der Richtlinienname nicht Alter enthält.

Im Beispiel `IAuthorizationPolicyProvider` obenstehend dargestellte Implementierung kann aktualisiert werden, damit die `DefaultAuthorizationPolicyProvider` durch Erstellen eines fallback-Richtlinie-Anbieters in seinem Konstruktor (auf verwendet werden, für den Fall, dass Sie der Namen der Richtlinie nicht mit der erwarteten Muster "MinimumAge" + Age übereinstimmt).

```csharp
private DefaultAuthorizationPolicyProvider FallbackPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    FallbackPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

Anschließend wird die `GetPolicyAsync` Methode kann aktualisiert werden, damit die `FallbackPolicyProvider` anstelle von Null:

```csharp
...
return FallbackPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>Standardrichtlinie

Zusätzlich zur Bereitstellung von benannten Autorisierungsrichtlinien, die eine benutzerdefinierte `IAuthorizationPolicyProvider` implementiert werden muss `GetDefaultPolicyAsync` , geben Sie eine Autorisierungsrichtlinie für `[Authorize]` Attribute ohne ein Richtlinienname angegeben.

In vielen Fällen ein authentifiziertes Benutzers, dieses Autorisierungsattribut nur erfordert, damit Sie die erforderlichen Richtlinie mit einem Aufruf von treffen können `RequireAuthenticatedUser`:

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder().RequireAuthenticatedUser().Build());
```

Wie bei der alle Aspekte einer benutzerdefinierten `IAuthorizationPolicyProvider`, Sie können dies, je nach Bedarf anpassen. In einigen Fällen ist es möglicherweise wünschenswert, zum Abrufen der Standardrichtlinie von Fallback `IAuthorizationPolicyProvider`.

## <a name="required-policy"></a>Erforderliche Richtlinie

Eine benutzerdefinierte `IAuthorizationPolicyProvider` implementiert werden muss `GetRequiredPolicyAsync` , geben Sie optional eine Richtlinie, die immer erforderlich ist. Wenn `GetRequiredPolicyAsync` eine Richtlinie ungleich Null, gibt die Richtlinie wird bei einem anderen (benannt oder Standard) kombiniert werden-Richtlinie, die angefordert wird.

Wenn keine erforderliche Richtlinie erforderlich ist, kann nur der Anbieter gibt null zurück, oder basierend auf den fallback-Anbieter:

```csharp
public Task<AuthorizationPolicy> GetRequiredPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>Verwenden Sie eine benutzerdefinierte IAuthorizationPolicyProvider

Zum Verwenden benutzerdefinierter Richtlinien aus einer `IAuthorizationPolicyProvider`, müssen Sie:

* Registrieren Sie die entsprechende `AuthorizationHandler` Typen mit Dependency Injection (beschrieben [richtlinienbasierte Autorisierung](xref:security/authorization/policies#authorization-handlers)), wie bei allen richtlinienbasierte Autorisierung-Szenarien.
* Registrieren Sie die benutzerdefinierte `IAuthorizationPolicyProvider` Typ in der app Dependency Injection-Dienst-Auflistung (in `Startup.ConfigureServices`) um den Standardanbieter für die Richtlinie zu ersetzen.

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

Eine vollständige benutzerdefinierte `IAuthorizationPolicyProvider` Beispiel steht in der [Aspnet/AuthSamples GitHub-Repository](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider).
