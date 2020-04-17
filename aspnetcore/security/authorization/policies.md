---
title: Policy-basierte Autorisierung in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie Autorisierungsrichtlinienhandler zum Erzwingen von Autorisierungsanforderungen in einer ASP.NET Core-App erstellen und verwenden.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
uid: security/authorization/policies
ms.openlocfilehash: 66412a6020ea8f12427c8c5b466e1b2eedccf0f9
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488760"
---
# <a name="policy-based-authorization-in-aspnet-core"></a>Policy-basierte Autorisierung in ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Unter den Abdeckungen verwenden [rollenbasierte Autorisierung](xref:security/authorization/roles) und [anspruchsbasierte Autorisierung](xref:security/authorization/claims) eine Anforderung, einen Anforderungshandler und eine vorkonfigurierte Richtlinie. Diese Bausteine unterstützen die Ausprägung von Autorisierungsauswertungen im Code. Das Ergebnis ist eine umfangreichere, wiederverwendbare, testbare Autorisierungsstruktur.

Eine Autorisierungsrichtlinie besteht aus einer oder mehreren Anforderungen. Es ist als Teil der Autorisierungsdienstkonfiguration `Startup.ConfigureServices` in der Methode registriert:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

Im vorherigen Beispiel wird eine "AtLeast21"-Richtlinie erstellt. Es hat eine&mdash;einzige Anforderung, dass ein Mindestalter, das als Parameter für die Anforderung angegeben wird.

## <a name="iauthorizationservice"></a>IAuthorizationService 

Der primäre Dienst, der bestimmt, ob die Autorisierung erfolgreich ist, ist: <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

Der vorherige Code hebt die beiden Methoden des [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)hervor.

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>ist ein Markerdienst ohne Methoden und der Mechanismus zum Nachverfolgen, ob die Autorisierung erfolgreich ist.

Jeder <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> ist für die Überprüfung verantwortlich, ob die Anforderungen erfüllt sind:
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

Die <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> Klasse verwendet der Handler, um zu markieren, ob anforderungenerfüllt wurden:

```csharp
 context.Succeed(requirement)
```

Der folgende Code zeigt die vereinfachte (und mit Kommentaren kommentierte) Standardimplementierung des Autorisierungsdienstes:

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

Der folgende Code `ConfigureServices`zeigt eine typische:

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

Verwendung <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` oder Autorisierung.

## <a name="applying-policies-to-mvc-controllers"></a>Anwenden von Richtlinien auf MVC-Controller

Wenn Sie Razor-Seiten verwenden, finden Sie weitere Informationen unter [Anwenden von Richtlinien auf Razor-Seiten](#applying-policies-to-razor-pages) in diesem Dokument.

Richtlinien werden auf Controller `[Authorize]` angewendet, indem das Attribut mit dem Richtliniennamen verwendet wird. Beispiel:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>Anwenden von Richtlinien auf Razor-Seiten

Richtlinien werden auf Razor Pages `[Authorize]` angewendet, indem das Attribut mit dem Richtliniennamen verwendet wird. Beispiel:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

Richtlinien können auch mithilfe einer [Autorisierungskonvention](xref:security/authorization/razor-pages-authorization)auf Razor-Seiten angewendet werden.

## <a name="requirements"></a>Requirements (Anforderungen)

Eine Autorisierungsanforderung ist eine Sammlung von Datenparametern, die eine Richtlinie zum Auswerten des aktuellen Benutzerprinzipals verwenden kann. In unserer "AtLeast21"-Richtlinie ist die&mdash;Anforderung ein einzelner Parameter das Mindestalter. Eine Anforderung implementiert [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), die eine leere Markerschnittstelle ist. Eine parametrisierte Mindestaltersanforderung könnte wie folgt implementiert werden:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Wenn eine Autorisierungsrichtlinie mehrere Autorisierungsanforderungen enthält, müssen alle Anforderungen erfüllt sein, damit die Richtlinienbewertung erfolgreich ist. Mit anderen Worten, mehrere Autorisierungsanforderungen, die einer einzelnen Autorisierungsrichtlinie hinzugefügt werden, werden auf **UND-Basis** behandelt.

> [!NOTE]
> Eine Anforderung muss keine Daten oder Eigenschaften enthalten.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Autorisierungshandler

Ein Autorisierungshandler ist für die Bewertung der Eigenschaften einer Anforderung verantwortlich. Der Autorisierungshandler wertet die Anforderungen anhand eines bereitgestellten [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) aus, um zu ermitteln, ob der Zugriff zulässig ist.

Eine Anforderung kann [über mehrere Handler](#security-authorization-policies-based-multiple-handlers)verfügen. Ein Handler kann [AuthorizationHandler\<TRequirement ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)>`TRequirement` erben, wobei die serkende Anforderung ist. Alternativ kann ein Handler [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) implementieren, um mehr als einen Anforderungstyp zu verarbeiten.

### <a name="use-a-handler-for-one-requirement"></a>Verwenden eines Handlers für eine Anforderung

<a name="security-authorization-handler-example"></a>

Im Folgenden finden Sie ein Beispiel für eine 1:1-Beziehung, in der ein Mindestaltershandler eine einzelne Anforderung verwendet:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

Der vorstehende Code bestimmt, ob der aktuelle Benutzerprinzipal über einen Geburtsdatumsanspruch verfügt, der von einem bekannten und vertrauenswürdigen Aussteller ausgestellt wurde. Die Autorisierung kann nicht erfolgen, wenn der Anspruch fehlt, in diesem Fall wird eine abgeschlossene Aufgabe zurückgegeben. Wenn ein Anspruch vorhanden ist, wird das Alter des Benutzers berechnet. Erfüllt der Benutzer das in der Anforderung festgelegte Mindestalter, gilt die Autorisierung als erfolgreich. Wenn die Autorisierung `context.Succeed` erfolgreich ist, wird mit der erfüllten Anforderung als einziger Parameter aufgerufen.

### <a name="use-a-handler-for-multiple-requirements"></a>Verwenden eines Handlers für mehrere Anforderungen

Im Folgenden finden Sie ein Beispiel für eine 1:n-Beziehung, in der ein Berechtigungshandler drei verschiedene Arten von Anforderungen verarbeiten kann:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

Der vorherige Code durchläuft [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;eine Eigenschaft, die Anforderungen enthält, die nicht als erfolgreich markiert sind. Für `ReadPermission` eine Anforderung muss der Benutzer entweder ein Besitzer oder ein Sponsor sein, um auf die angeforderte Ressource zugreifen zu können. Im Falle einer `EditPermission` `DeletePermission` Anforderung oder Anforderung muss er ein Besitzer sein, um auf die angeforderte Ressource zugreifen zu können.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Handlerregistrierung

Handler werden während der Konfiguration in der Dienstauflistung registriert. Beispiel:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

Der vorhergehende `MinimumAgeHandler` Code registriert sich als `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`Singleton, indem er auf . Handler können mit einer der integrierten [Dienstlebensdauern](xref:fundamentals/dependency-injection#service-lifetimes)registriert werden.

## <a name="what-should-a-handler-return"></a>Was sollte ein Handler zurückgeben?

Beachten Sie, dass die `Handle` Methode im [Handlerbeispiel](#security-authorization-handler-example) keinen Wert zurückgibt. Wie wird der Status von Erfolg oder Misserfolg angezeigt?

* Ein Handler gibt `context.Succeed(IAuthorizationRequirement requirement)`den Erfolg an, indem er die Anforderung, die erfolgreich überprüft wurde, übergibt.

* Ein Handler muss Fehler nicht im Allgemeinen behandeln, da andere Handler für dieselbe Anforderung erfolgreich sein können.

* Um einen Fehler zu gewährleisten, rufen `context.Fail`Sie auch dann auf, wenn andere Anforderungshandler erfolgreich sind.

Wenn ein `context.Succeed` Handler `context.Fail`aufruft oder , werden alle anderen Handler weiterhin aufgerufen. Dadurch können Anforderungen Nebenwirkungen wie Protokollierung erzeugen, die auch dann stattfindet, wenn ein anderer Handler eine Anforderung erfolgreich validiert oder nicht bestanden hat. Wenn auf `false`festgelegt, verkürzt die [InvokeHandlersAfterFailure-Eigenschaft](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (verfügbar in ASP.NET Core 1.1 `context.Fail` und höher) die Ausführung von Handlern, wenn sie aufgerufen werden. `InvokeHandlersAfterFailure`Standardwerte `true`auf , in diesem Fall werden alle Handler aufgerufen.

> [!NOTE]
> Autorisierungshandler werden auch dann aufgerufen, wenn die Authentifizierung fehlschlägt.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Warum sollte ich mehrere Handler für eine Anforderung verwenden?

In Fällen, in denen die Evaluierung auf **ODER-Basis** erfolgen soll, implementieren Sie mehrere Handler für eine einzelne Anforderung. Microsoft verfügt beispielsweise über Türen, die sich nur mit Schlüsselkarten öffnen. Wenn Sie Ihre Schlüsselkarte zu Hause lassen, druckt die Rezeptionistin einen temporären Aufkleber und öffnet Ihnen die Tür. In diesem Szenario haben Sie eine einzelne Anforderung, *BuildingEntry*, aber mehrere Handler, wobei jeder eine einzelne Anforderung untersucht.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Stellen Sie sicher, dass beide Handler [registriert](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)sind. Wenn einer der Handler erfolgreich ist, wenn eine Richtlinie die `BuildingEntryRequirement`auswertet, ist die Richtlinienauswertung erfolgreich.

## <a name="using-a-func-to-fulfill-a-policy"></a>Verwenden eines Func zur Erfüllung einer Richtlinie

Es kann Situationen geben, in denen die Erfüllung einer Richtlinie einfach im Code auszudrücken ist. Es ist möglich, `Func<AuthorizationHandlerContext, bool>` eine beim Konfigurieren `RequireAssertion` Ihrer Richtlinie mit dem Richtlinien-Generator zu liefern.

Die vorherige `BadgeEntryHandler` könnte z. B. wie folgt umgeschrieben werden:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a>Zugriff auf MVC-Anforderungskontext in Handlern

Die `HandleRequirementAsync` Methode, die Sie in einem `AuthorizationHandlerContext` Autorisierungshandler implementieren, hat zwei Parameter: eine und die, die `TRequirement` Sie behandeln. Frameworks wie MVC oder Jabbr können der `Resource` Eigenschaft auf `AuthorizationHandlerContext` der beliebige Objektdatei hinzufügen, um zusätzliche Informationen zu übergeben.

MVC übergibt z. B. eine `Resource` Instanz von [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in der Eigenschaft. Diese Eigenschaft bietet `HttpContext` `RouteData`Zugriff auf , und alles andere, was von MVC und Razor Pages bereitgestellt wird.

Die Verwendung `Resource` der Eigenschaft ist frameworkspezifisch. Die Verwendung `Resource` von Informationen in der Eigenschaft beschränkt Ihre Autorisierungsrichtlinien auf bestimmte Frameworks. Sie sollten `Resource` die Eigenschaft `is` mit dem Schlüsselwort umwerfen und dann bestätigen, dass `InvalidCastException` es der Umwandlung gelungen ist, sicherzustellen, dass Ihr Code nicht mit einem abstürzt, wenn er auf anderen Frameworks ausgeführt wird:

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

Unter den Abdeckungen verwenden [rollenbasierte Autorisierung](xref:security/authorization/roles) und [anspruchsbasierte Autorisierung](xref:security/authorization/claims) eine Anforderung, einen Anforderungshandler und eine vorkonfigurierte Richtlinie. Diese Bausteine unterstützen die Ausprägung von Autorisierungsauswertungen im Code. Das Ergebnis ist eine umfangreichere, wiederverwendbare, testbare Autorisierungsstruktur.

Eine Autorisierungsrichtlinie besteht aus einer oder mehreren Anforderungen. Es ist als Teil der Autorisierungsdienstkonfiguration `Startup.ConfigureServices` in der Methode registriert:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

Im vorherigen Beispiel wird eine "AtLeast21"-Richtlinie erstellt. Es hat eine&mdash;einzige Anforderung, dass ein Mindestalter, das als Parameter für die Anforderung angegeben wird.

## <a name="iauthorizationservice"></a>IAuthorizationService 

Der primäre Dienst, der bestimmt, ob die Autorisierung erfolgreich ist, ist: <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

Der vorherige Code hebt die beiden Methoden des [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)hervor.

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>ist ein Markerdienst ohne Methoden und der Mechanismus zum Nachverfolgen, ob die Autorisierung erfolgreich ist.

Jeder <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> ist für die Überprüfung verantwortlich, ob die Anforderungen erfüllt sind:
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

Die <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> Klasse verwendet der Handler, um zu markieren, ob anforderungenerfüllt wurden:

```csharp
 context.Succeed(requirement)
```

Der folgende Code zeigt die vereinfachte (und mit Kommentaren kommentierte) Standardimplementierung des Autorisierungsdienstes:

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

Der folgende Code `ConfigureServices`zeigt eine typische:

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

Verwendung <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` oder Autorisierung.

## <a name="applying-policies-to-mvc-controllers"></a>Anwenden von Richtlinien auf MVC-Controller

Wenn Sie Razor-Seiten verwenden, finden Sie weitere Informationen unter [Anwenden von Richtlinien auf Razor-Seiten](#applying-policies-to-razor-pages) in diesem Dokument.

Richtlinien werden auf Controller `[Authorize]` angewendet, indem das Attribut mit dem Richtliniennamen verwendet wird. Beispiel:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>Anwenden von Richtlinien auf Razor-Seiten

Richtlinien werden auf Razor Pages `[Authorize]` angewendet, indem das Attribut mit dem Richtliniennamen verwendet wird. Beispiel:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

Richtlinien können auch mithilfe einer [Autorisierungskonvention](xref:security/authorization/razor-pages-authorization)auf Razor-Seiten angewendet werden.

## <a name="requirements"></a>Requirements (Anforderungen)

Eine Autorisierungsanforderung ist eine Sammlung von Datenparametern, die eine Richtlinie zum Auswerten des aktuellen Benutzerprinzipals verwenden kann. In unserer "AtLeast21"-Richtlinie ist die&mdash;Anforderung ein einzelner Parameter das Mindestalter. Eine Anforderung implementiert [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), die eine leere Markerschnittstelle ist. Eine parametrisierte Mindestaltersanforderung könnte wie folgt implementiert werden:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Wenn eine Autorisierungsrichtlinie mehrere Autorisierungsanforderungen enthält, müssen alle Anforderungen erfüllt sein, damit die Richtlinienbewertung erfolgreich ist. Mit anderen Worten, mehrere Autorisierungsanforderungen, die einer einzelnen Autorisierungsrichtlinie hinzugefügt werden, werden auf **UND-Basis** behandelt.

> [!NOTE]
> Eine Anforderung muss keine Daten oder Eigenschaften enthalten.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Autorisierungshandler

Ein Autorisierungshandler ist für die Bewertung der Eigenschaften einer Anforderung verantwortlich. Der Autorisierungshandler wertet die Anforderungen anhand eines bereitgestellten [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) aus, um zu ermitteln, ob der Zugriff zulässig ist.

Eine Anforderung kann [über mehrere Handler](#security-authorization-policies-based-multiple-handlers)verfügen. Ein Handler kann [AuthorizationHandler\<TRequirement ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)>`TRequirement` erben, wobei die serkende Anforderung ist. Alternativ kann ein Handler [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) implementieren, um mehr als einen Anforderungstyp zu verarbeiten.

### <a name="use-a-handler-for-one-requirement"></a>Verwenden eines Handlers für eine Anforderung

<a name="security-authorization-handler-example"></a>

Im Folgenden finden Sie ein Beispiel für eine 1:1-Beziehung, in der ein Mindestaltershandler eine einzelne Anforderung verwendet:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

Der vorstehende Code bestimmt, ob der aktuelle Benutzerprinzipal über einen Geburtsdatumsanspruch verfügt, der von einem bekannten und vertrauenswürdigen Aussteller ausgestellt wurde. Die Autorisierung kann nicht erfolgen, wenn der Anspruch fehlt, in diesem Fall wird eine abgeschlossene Aufgabe zurückgegeben. Wenn ein Anspruch vorhanden ist, wird das Alter des Benutzers berechnet. Erfüllt der Benutzer das in der Anforderung festgelegte Mindestalter, gilt die Autorisierung als erfolgreich. Wenn die Autorisierung `context.Succeed` erfolgreich ist, wird mit der erfüllten Anforderung als einziger Parameter aufgerufen.

### <a name="use-a-handler-for-multiple-requirements"></a>Verwenden eines Handlers für mehrere Anforderungen

Im Folgenden finden Sie ein Beispiel für eine 1:n-Beziehung, in der ein Berechtigungshandler drei verschiedene Arten von Anforderungen verarbeiten kann:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

Der vorherige Code durchläuft [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;eine Eigenschaft, die Anforderungen enthält, die nicht als erfolgreich markiert sind. Für `ReadPermission` eine Anforderung muss der Benutzer entweder ein Besitzer oder ein Sponsor sein, um auf die angeforderte Ressource zugreifen zu können. Im Falle einer `EditPermission` `DeletePermission` Anforderung oder Anforderung muss er ein Besitzer sein, um auf die angeforderte Ressource zugreifen zu können.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Handlerregistrierung

Handler werden während der Konfiguration in der Dienstauflistung registriert. Beispiel:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

Der vorhergehende `MinimumAgeHandler` Code registriert sich als `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`Singleton, indem er auf . Handler können mit einer der integrierten [Dienstlebensdauern](xref:fundamentals/dependency-injection#service-lifetimes)registriert werden.

## <a name="what-should-a-handler-return"></a>Was sollte ein Handler zurückgeben?

Beachten Sie, dass die `Handle` Methode im [Handlerbeispiel](#security-authorization-handler-example) keinen Wert zurückgibt. Wie wird der Status von Erfolg oder Misserfolg angezeigt?

* Ein Handler gibt `context.Succeed(IAuthorizationRequirement requirement)`den Erfolg an, indem er die Anforderung, die erfolgreich überprüft wurde, übergibt.

* Ein Handler muss Fehler nicht im Allgemeinen behandeln, da andere Handler für dieselbe Anforderung erfolgreich sein können.

* Um einen Fehler zu gewährleisten, rufen `context.Fail`Sie auch dann auf, wenn andere Anforderungshandler erfolgreich sind.

Wenn ein `context.Succeed` Handler `context.Fail`aufruft oder , werden alle anderen Handler weiterhin aufgerufen. Dadurch können Anforderungen Nebenwirkungen wie Protokollierung erzeugen, die auch dann stattfindet, wenn ein anderer Handler eine Anforderung erfolgreich validiert oder nicht bestanden hat. Wenn auf `false`festgelegt, verkürzt die [InvokeHandlersAfterFailure-Eigenschaft](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) (verfügbar in ASP.NET Core 1.1 `context.Fail` und höher) die Ausführung von Handlern, wenn sie aufgerufen werden. `InvokeHandlersAfterFailure`Standardwerte `true`auf , in diesem Fall werden alle Handler aufgerufen.

> [!NOTE]
> Autorisierungshandler werden auch dann aufgerufen, wenn die Authentifizierung fehlschlägt.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Warum sollte ich mehrere Handler für eine Anforderung verwenden?

In Fällen, in denen die Evaluierung auf **ODER-Basis** erfolgen soll, implementieren Sie mehrere Handler für eine einzelne Anforderung. Microsoft verfügt beispielsweise über Türen, die sich nur mit Schlüsselkarten öffnen. Wenn Sie Ihre Schlüsselkarte zu Hause lassen, druckt die Rezeptionistin einen temporären Aufkleber und öffnet Ihnen die Tür. In diesem Szenario haben Sie eine einzelne Anforderung, *BuildingEntry*, aber mehrere Handler, wobei jeder eine einzelne Anforderung untersucht.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Stellen Sie sicher, dass beide Handler [registriert](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)sind. Wenn einer der Handler erfolgreich ist, wenn eine Richtlinie die `BuildingEntryRequirement`auswertet, ist die Richtlinienauswertung erfolgreich.

## <a name="using-a-func-to-fulfill-a-policy"></a>Verwenden eines Func zur Erfüllung einer Richtlinie

Es kann Situationen geben, in denen die Erfüllung einer Richtlinie einfach im Code auszudrücken ist. Es ist möglich, `Func<AuthorizationHandlerContext, bool>` eine beim Konfigurieren `RequireAssertion` Ihrer Richtlinie mit dem Richtlinien-Generator zu liefern.

Die vorherige `BadgeEntryHandler` könnte z. B. wie folgt umgeschrieben werden:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a>Zugriff auf MVC-Anforderungskontext in Handlern

Die `HandleRequirementAsync` Methode, die Sie in einem `AuthorizationHandlerContext` Autorisierungshandler implementieren, hat zwei Parameter: eine und die, die `TRequirement` Sie behandeln. Frameworks wie MVC oder SignalR können der `Resource` Eigenschaft auf `AuthorizationHandlerContext` der beliebige Objektdatei hinzufügen, um zusätzliche Informationen zu übergeben.

Bei der Verwendung des Endpunktroutings wird die Autorisierung in der Regel von der Authorization Middleware durchgeführt. In diesem Fall `Resource` ist die <xref:Microsoft.AspNetCore.Http.Endpoint>Eigenschaft eine Instanz von . Der Endpunkt kann verwendet werden, um den zugrunde liegenden Ressourcen zu untersuchen, an die Sie routingen. Beispiel:

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

Bei herkömmlichem Routing oder wenn die Autorisierung als Teil des `Resource` MVC-Autorisierungsfilters erfolgt, ist der Wert von einer <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> Instanz. Diese Eigenschaft bietet `HttpContext` `RouteData`Zugriff auf , und alles andere, was von MVC und Razor Pages bereitgestellt wird.

Die Verwendung `Resource` der Eigenschaft ist frameworkspezifisch. Die Verwendung `Resource` von Informationen in der Eigenschaft beschränkt Ihre Autorisierungsrichtlinien auf bestimmte Frameworks. Sie sollten `Resource` die Eigenschaft `is` mit dem Schlüsselwort umwerfen und dann bestätigen, dass `InvalidCastException` es der Umwandlung gelungen ist, sicherzustellen, dass Ihr Code nicht mit einem abstürzt, wenn er auf anderen Frameworks ausgeführt wird:

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
