---
title: Benutzerdefinierte Autorisierungs Richtlinien Anbieter in ASP.net Core
author: mjrousos
description: Erfahren Sie, wie Sie einen benutzerdefinierten iauthorizationpolicyprovider in einer ASP.net Core-App verwenden, um Autorisierungs Richtlinien dynamisch zu generieren.
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 4f6a4ea209ebe30759f9f14b15b0385399b36ead
ms.sourcegitcommit: 231780c8d7848943e5e9fd55e93f437f7e5a371d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74116055"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>Benutzerdefinierte Autorisierungs Richtlinien Anbieter, die iauthorizationpolicyprovider in ASP.net Core verwenden 

Von [Mike rousos](https://github.com/mjrousos)

Wenn Sie die [Richtlinien basierte Autorisierung](xref:security/authorization/policies)verwenden, werden die Richtlinien in der Regel durch Aufrufen von `AuthorizationOptions.AddPolicy` als Teil der Autorisierungs Dienst Konfiguration registriert. In einigen Szenarien ist es möglicherweise nicht möglich (oder wünschenswert), Alle Autorisierungs Richtlinien auf diese Weise zu registrieren. In diesen Fällen können Sie mithilfe eines benutzerdefinierten `IAuthorizationPolicyProvider` steuern, wie Autorisierungs Richtlinien bereitgestellt werden.

Beispiele für Szenarien, in denen ein benutzerdefinierter [iauthorizationpolicyprovider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) nützlich sein kann, sind:

* Verwenden eines externen Dienstanbieter zur Bereitstellung der Richtlinien Auswertung.
* Die Verwendung einer großen Bandbreite an Richtlinien (z. b. für unterschiedliche Raum Nummern oder Alters Zahlen), ist daher nicht sinnvoll, jede einzelne Autorisierungs Richtlinie mit einem `AuthorizationOptions.AddPolicy`-Befehl hinzuzufügen.
* Erstellen von Richtlinien zur Laufzeit basierend auf Informationen in einer externen Datenquelle (z. b. einer Datenbank) oder durch das dynamische ermitteln der Autorisierungs Anforderungen über einen anderen Mechanismus

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) aus dem [aspnetcore-GitHub-Repository](https://github.com/aspnet/AspNetCore). Laden Sie die ASPNET/aspnetcore-Repository-ZIP-Datei herunter. Entzippen Sie die Datei. Navigieren Sie zum Projektordner *src/Security/Samples/custompolicyprovider* .

## <a name="customize-policy-retrieval"></a>Anpassen des Richtlinien Abrufs

ASP.net Core-Apps verwenden eine Implementierung der `IAuthorizationPolicyProvider` Schnittstelle zum Abrufen von Autorisierungs Richtlinien. Standardmäßig ist [defaultauthorizationpolicyprovider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) registriert und wird verwendet. `DefaultAuthorizationPolicyProvider` gibt Richtlinien vom `AuthorizationOptions` zurück, die in einem `IServiceCollection.AddAuthorization`-Befehl bereitgestellt werden.

Sie können dieses Verhalten anpassen, indem Sie eine andere `IAuthorizationPolicyProvider` Implementierung im Container für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) der APP registrieren. 

Die `IAuthorizationPolicyProvider`-Schnittstelle enthält drei APIs:

* [Getpolicyasync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) gibt eine Autorisierungs Richtlinie für einen angegebenen Namen zurück.
* [Getdefaultpolicyasync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) gibt die Standard Autorisierungs Richtlinie zurück (die Richtlinie, die für `[Authorize]` Attribute verwendet wird, ohne dass eine Richtlinie angegeben ist) 
* [Getfallbackpolicyasync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) gibt die Fall Back Autorisierungs Richtlinie zurück (die Richtlinie, die von der Autorisierungs Middleware verwendet wird, wenn keine Richtlinie angegeben ist). 

Durch Implementieren dieser APIs können Sie die Art der Bereitstellung von Autorisierungs Richtlinien anpassen.

## <a name="parameterized-authorize-attribute-example"></a>Beispiel für ein parametrisiertes Autorisierungs Attribut

Ein Szenario, in dem `IAuthorizationPolicyProvider` nützlich ist, ist das Aktivieren benutzerdefinierter `[Authorize]` Attribute, deren Anforderungen von einem Parameter abhängen. Beispielsweise wurde in der Dokumentation zur [Richtlinien basierten Autorisierung](xref:security/authorization/policies) eine Alters basierte Richtlinie ("AtLeast21") als Beispiel verwendet. Wenn verschiedene Controller Aktionen in einer APP für Benutzer unter *schiedlicher* Zeiten verfügbar gemacht werden sollen, kann es hilfreich sein, viele verschiedene Alters basierte Richtlinien zu haben. Anstatt alle unterschiedlichen Alters basierten Richtlinien zu registrieren, die von der Anwendung in `AuthorizationOptions`benötigt werden, können Sie die Richtlinien dynamisch mit einem benutzerdefinierten `IAuthorizationPolicyProvider`generieren. Um die Verwendung der Richtlinien zu vereinfachen, können Sie Aktionen mit benutzerdefiniertem Autorisierungs Attribut wie `[MinimumAgeAuthorize(20)]`kommentieren.

## <a name="custom-authorization-attributes"></a>Benutzerdefinierte Autorisierungs Attribute

Autorisierungs Richtlinien werden anhand ihrer Namen identifiziert. Der zuvor beschriebene benutzerdefinierte `MinimumAgeAuthorizeAttribute` muss Argumente zu einer Zeichenfolge zuordnen, die zum Abrufen der entsprechenden Autorisierungs Richtlinie verwendet werden kann. Dies können Sie erreichen, indem Sie von `AuthorizeAttribute` ableiten und die `Age`-Eigenschaft in die `AuthorizeAttribute.Policy`-Eigenschaft einbinden.

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

Dieser Attributtyp verfügt über eine `Policy` Zeichenfolge, die auf dem hart codierten Präfix (`"MinimumAge"`) basiert, und eine ganze Zahl, die über den Konstruktor übergeben wird

Sie können es auf die gleiche Weise wie andere `Authorize` Attribute anwenden, außer dass es eine ganze Zahl als Parameter annimmt.

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>Benutzerdefinierter iauthorizationpolicyprovider

Mit dem benutzerdefinierten `MinimumAgeAuthorizeAttribute` können Sie auf einfache Weise Autorisierungs Richtlinien für ein beliebiges minimal Alter anfordern. Das nächste Problem, das gelöst werden muss, besteht darin, sicherzustellen, dass Autorisierungs Richtlinien für alle unterschiedlichen Alters Bereiche verfügbar sind. An dieser Stelle ist eine `IAuthorizationPolicyProvider` nützlich.

Wenn Sie `MinimumAgeAuthorizationAttribute`verwenden, folgen die Namen der Autorisierungs Richtlinien dem Muster `"MinimumAge" + Age`, sodass die benutzerdefinierte `IAuthorizationPolicyProvider` Autorisierungs Richtlinien generieren soll:

* Das Alter wird anhand des Richtlinien namens verarbeitet.
* Verwenden von `AuthorizationPolicyBuilder` zum Erstellen eines neuen `AuthorizationPolicy`
* In diesem und den folgenden Beispielen wird davon ausgegangen, dass der Benutzer über ein Cookie authentifiziert wird. Die `AuthorizationPolicyBuilder` sollte entweder mit mindestens einem Autorisierungs Schema Namen erstellt werden oder immer erfolgreich sein. Andernfalls gibt es keine Informationen zum Bereitstellen einer Herausforderung für den Benutzer, und es wird eine Ausnahme ausgelöst.
* Hinzufügen von Anforderungen zur Richtlinie basierend auf dem Alter mit `AuthorizationPolicyBuilder.AddRequirements`. In anderen Szenarien können Sie stattdessen `RequireClaim`, `RequireRole`oder `RequireUserName` verwenden.

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

## <a name="multiple-authorization-policy-providers"></a>Mehrere Autorisierungs Richtlinien Anbieter

Beachten Sie bei der Verwendung von benutzerdefinierten `IAuthorizationPolicyProvider` Implementierungen, dass ASP.net Core nur eine Instanz von `IAuthorizationPolicyProvider`verwendet. Wenn ein benutzerdefinierter Anbieter keine Autorisierungs Richtlinien für alle zu verwendenden Richtlinien Namen bereitstellen kann, sollte er an einen Sicherungs Anbieter zurückgestellt werden. 

Stellen Sie sich beispielsweise eine Anwendung vor, die sowohl benutzerdefinierte Alters Richtlinien als auch herkömmlicheren rollenbasierten Richtlinien Abruf benötigt. Eine solche APP könnte einen benutzerdefinierten Autorisierungs Richtlinien Anbieter verwenden, der Folgendes:

* Versucht, Richtlinien Namen zu analysieren. 
* Ruft einen anderen Richtlinien Anbieter auf (z. b. `DefaultAuthorizationPolicyProvider`), wenn der Richtlinien Name kein Alter enthält.

Das oben gezeigte Beispiel `IAuthorizationPolicyProvider`-Implementierung kann so aktualisiert werden, dass die `DefaultAuthorizationPolicyProvider` verwendet wird, indem ein Sicherungs Richtlinien Anbieter im Konstruktor erstellt wird (für den Fall, dass der Richtlinien Name nicht mit dem erwarteten Muster "minimumAge" + Age identisch ist).

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

Anschließend kann die `GetPolicyAsync`-Methode so aktualisiert werden, dass Sie die `BackupPolicyProvider` anstelle von NULL zurückgibt:

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>Standard Richtlinie

Zusätzlich zur Bereitstellung benannter Autorisierungs Richtlinien muss ein benutzerdefinierter `IAuthorizationPolicyProvider` `GetDefaultPolicyAsync` implementieren, um eine Autorisierungs Richtlinie für `[Authorize]` Attribute bereitzustellen, ohne dass ein Richtlinien Name angegeben wird.

In vielen Fällen ist für dieses Autorisierungs Attribut nur ein authentifizierter Benutzer erforderlich, sodass Sie die erforderliche Richtlinie mit einem `RequireAuthenticatedUser`-aufrufmachen können:

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

Wie bei allen Aspekten eines benutzerdefinierten `IAuthorizationPolicyProvider`können Sie dies nach Bedarf anpassen. In einigen Fällen kann es wünschenswert sein, die Standard Richtlinie von einem Fall Back `IAuthorizationPolicyProvider`abzurufen.

## <a name="fallback-policy"></a>Fallback-Richtlinie

Eine benutzerdefinierte `IAuthorizationPolicyProvider` kann optional `GetFallbackPolicyAsync` implementieren, um eine Richtlinie bereitzustellen, die beim [Kombinieren von Richtlinien](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) verwendet wird, und wenn keine Richtlinien angegeben werden Wenn `GetFallbackPolicyAsync` eine Richtlinie ungleich NULL zurückgibt, wird die zurückgegebene Richtlinie von der Autorisierungs Middleware verwendet, wenn keine Richtlinien für die Anforderung angegeben werden.

Wenn keine Fall Back Richtlinie erforderlich ist, kann der Anbieter `null` zurückgeben oder auf den Fall Back Anbieter zurückgreifen:

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>Verwenden eines benutzerdefinierten iauthorizationpolicyprovider

Wenn Sie benutzerdefinierte Richtlinien aus einer `IAuthorizationPolicyProvider`verwenden möchten, müssen Sie

* Registrieren Sie die entsprechenden `AuthorizationHandler` Typen mit Abhängigkeitsinjektion (in [Richtlinien basierter Autorisierung](xref:security/authorization/policies#authorization-handlers)beschrieben), wie bei allen Richtlinien basierten Autorisierungs Szenarios.
* Registrieren Sie den benutzerdefinierten `IAuthorizationPolicyProvider` Typ in der Abhängigkeitsinjektion-Dienst Auflistung (`Startup.ConfigureServices`) der APP, um den Standardrichtlinien Anbieter zu ersetzen.

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

Ein Beispiel für eine komplette benutzerdefinierte `IAuthorizationPolicyProvider` finden Sie im [GitHub-Repository ASPNET/authsamples](https://github.com/aspnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider).
