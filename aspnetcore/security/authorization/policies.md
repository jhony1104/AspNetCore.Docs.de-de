---
title: Richtlinienbasierte Autorisierung in ASP.NET Core
author: rick-anderson
description: Informationen Sie zum Erstellen und Verwenden von Authorization Policy-Handler für das Erzwingen von autorisierungsanforderungen in einer ASP.NET Core-app.
ms.author: riande
ms.custom: mvc
ms.date: 11/21/2017
uid: security/authorization/policies
ms.openlocfilehash: e72f15c28fb7b62c671dd6475cc323cacce42de6
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208320"
---
# <a name="policy-based-authorization-in-aspnet-core"></a>Richtlinienbasierte Autorisierung in ASP.NET Core

Im Hintergrund [rollenbasierte Autorisierung](xref:security/authorization/roles) und [anspruchsbasierte Autorisierung](xref:security/authorization/claims) verwenden Sie eine Anforderung, einen Handler für die Anforderung und einer vorkonfigurierten Richtlinie. Diese Bausteine unterstützen den Ausdruck der auswertungen der Autorisierung im Code. Das Ergebnis ist eine umfangreichere, wieder verwendbare, testfähigen Autorisierung-Struktur.

Eine Autorisierungsrichtlinie besteht eine oder mehrere Anforderungen aus. Er ist als Teil der autorisierungsdienstkonfiguration registriert, der `Startup.ConfigureServices` Methode:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

Im vorherigen Beispiel wird eine Richtlinie für "AtLeast21" erstellt. Es wurde eine einzelne Anforderung&mdash;mit einem Mindestalter, die als Parameter für die Anforderung bereitgestellt wird.

## <a name="applying-policies-to-mvc-controllers"></a>Anwenden von Richtlinien auf MVC-Controller

Wenn Sie Razor-Seiten verwenden, finden Sie unter [Anwenden von Richtlinien auf Razor Pages](#applying-policies-to-razor-pages) in diesem Dokument.

Richtlinien auf Controller angewendet werden, mithilfe der `[Authorize]` Attribut mit dem Richtliniennamen. Zum Beispiel:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>Anwenden von Richtlinien auf Razor-Seiten

Richtlinien werden angewendet, Razor Pages unter Verwendung der `[Authorize]` Attribut mit dem Richtliniennamen. Zum Beispiel:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

Richtlinien können auch auf Razor Pages angewendet werden, mithilfe einer [Autorisierung Konvention](xref:security/authorization/razor-pages-authorization).

## <a name="requirements"></a>Anforderungen

Eine autorisierungsanforderung ist eine Auflistung von Datenparametern, die eine Richtlinie zum Auswerten des aktuelle Benutzerprinzipals verwenden können. In unserer "AtLeast21"-Richtlinie die Anforderung ist ein einzelner Parameter&mdash;das Mindestalter. Eine Anforderung implementiert [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), dies ist eine leere Markierungsschnittstelle. Eine parametrisierte Mindestalter-Anforderung kann wie folgt implementiert werden:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Eine Autorisierungsrichtlinie mehrere autorisierungsanforderungen enthält, müssen alle Anforderungen in der Reihenfolge für die richtlinienauswertung erfolgreich übergeben. Anders ausgedrückt, sind mehrere autorisierungsanforderungen, die eine einzelne Autorisierungsrichtlinie hinzugefügt auf behandelt eine **und** Basis.

> [!NOTE]
> Eine Anforderung benötigen nicht Daten oder Eigenschaften.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Authorization-Handler

Ein autorisierungshandler ist verantwortlich für die Auswertung von Eigenschaften für die Anforderung. Der autorisierungshandler für die wertet die Anforderungen für ein bereitgestelltes [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) zu bestimmen, ob der Zugriff zulässig ist.

Eine Anforderung haben [mehrere Handler](#security-authorization-policies-based-multiple-handlers). Ein Handler kann erben [AuthorizationHandler\<TRequirement >](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), wobei `TRequirement` ist die Anforderung behandelt werden. Alternativ kann ein Handler implementieren [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) um mehr als eine Art von Anforderung zu verarbeiten.

### <a name="use-a-handler-for-one-requirement"></a>Verwenden Sie einen Handler für eine Anforderung

<a name="security-authorization-handler-example"></a>

Im folgenden finden ein Beispiel für eine direkte Beziehung, in der ein Mindestalter-Handler eine einzelne Anforderung verwendet:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

Der vorangehende Code wird bestimmt, ob der aktuelle Benutzer, der Dienstprinzipal verfügt ein Geburtsdatum Anspruch, der von einem bekannten und vertrauenswürdigen Aussteller ausgegeben wurde. Autorisierung kann nicht auftreten, wenn der Anspruch nicht vorhanden ist, wird in diesem Fall eine abgeschlossene Aufgabe zurückgegeben. Wenn ein Anspruch vorhanden ist, wird das Alter des Benutzers berechnet. Wenn der Benutzer das Mindestalter, definiert durch die Anforderung erfüllt, wird die Autorisierung erfolgreich angesehen. Wenn eine Autorisierung erfolgreich ist, wird `context.Succeed` wird aufgerufen, mit der Anforderung erfüllt, als einzigen Parameter.

### <a name="use-a-handler-for-multiple-requirements"></a>Verwenden Sie einen Handler für mehrere Anforderungen

Im folgenden finden ein Beispiel für eine 1: n Beziehung, in der ein Berechtigung-Handler drei verschiedene Typen von Anforderungen verarbeiten kann:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

Der vorangehende Code durchläuft [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;eine Eigenschaft, die sowohl die Anforderungen nicht als erfolgreich gekennzeichnet. Für eine `ReadPermission` Anforderung entweder "Besitzer" oder "Sponsor" aus, um die angeforderte Ressource zuzugreifen, muss der Benutzer sein. Im Fall von einem `EditPermission` oder `DeletePermission` erforderlich, er oder sie muss einen Besitzer aus, um die angeforderte Ressource zuzugreifen.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Registrierung

Handler werden in der dienstauflistung während der Konfiguration registriert. Zum Beispiel:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

Der vorangehende Code registriert `MinimumAgeHandler` als Singleton durch den Aufruf `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`. Handler können registriert werden, mithilfe der integrierten [service Lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).

## <a name="what-should-a-handler-return"></a>Was sollte es sich um ein Handler zurückgeben?

Beachten Sie, dass die `Handle` -Methode in der die [Handler Beispiel](#security-authorization-handler-example) keinen Wert zurückgibt. Wie wird ein Status, der Erfolg oder Fehler angegeben?

* Ein Handler für die erfolgreiche Ausführung durch den Aufruf `context.Succeed(IAuthorizationRequirement requirement)`, übergeben der Anforderung, die erfolgreich überprüft wurde.

* Ein Ereignishandler muss nicht in der Regel Fehler zu behandeln, wie andere Handler für die gleiche Anforderung erfolgreich ausgeführt werden können.

* Um Fehler zu gewährleisten, selbst wenn andere Handler für die Anforderung erfolgreich ist, rufen Sie `context.Fail`.

Bei Festlegung auf `false`, [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) Eigenschaft (verfügbar in ASP.NET Core 1.1 und höher) wird verkürzt, die Ausführung der Handler bei `context.Fail` aufgerufen wird. `InvokeHandlersAfterFailure` Standardmäßig `true`, in diesem Fall alle Ereignishandler aufgerufen werden. Dadurch können Anforderungen zum Erzeugen von Nebeneffekten, z. B. die Protokollierung, die immer erfolgen, wenn `context.Fail` in einen anderen Handler aufgerufen wurde.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Warum sollte ich mehrere Handler für eine Anforderung die?

In Fällen, in dem Sie Auswertung auf eine **oder** Basis, mehrere Handler für eine einzelne Anforderung zu implementieren. Microsoft hat z. B. Türen, die nur mit Schlüssel Karten geöffnet werden. Wenn Sie Ihre Schlüsselkarte zu Hause lassen, wird die Empfangsdame druckt einen temporären Aufkleber und öffnet die Tür für Sie. In diesem Fall müssten Sie eine einzelne Anforderung *BuildingEntry*, aber mehrere Handler, die jeweils eine einzelne Anforderung zu untersuchen.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Stellen Sie sicher, dass beide Handler sind [registriert](xref:security/authorization/policies#security-authorization-policies-based-handler-registration). Wenn beide Handler erfolgreich ist, wenn eine Richtlinie ausgewertet wird die `BuildingEntryRequirement`, die richtlinienauswertung erfolgreich ausgeführt wird.

## <a name="using-a-func-to-fulfill-a-policy"></a>Eine Funktion verwenden, um eine Richtlinie zu erfüllen.

Es gibt möglicherweise Situationen, in die Ausführen eine Richtlinie einfach in Code Ausdrücken ist. Es ist möglich, geben Sie einen `Func<AuthorizationHandlerContext, bool>` bei der Konfiguration Ihrer Richtlinie mit den `RequireAssertion` Richtlinie-Generator.

Z. B. den vorherigen `BadgeEntryHandler` könnte wie folgt umgeschrieben werden:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a>Zugriff auf MVC-Anforderungskontext in Ereignishandlern

Die `HandleRequirementAsync` Methode, die Sie in einem autorisierungshandler implementieren, hat zwei Parameter: eine `AuthorizationHandlerContext` und `TRequirement` behandelnden. Frameworks wie MVC oder Jabbr sind kostenlos, fügen jedes beliebige Objekt an die `Resource` Eigenschaft für die `AuthorizationHandlerContext` um zusätzliche Informationen zu übergeben.

Z. B. MVC übergibt eine Instanz der [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in die `Resource` Eigenschaft. Diese Eigenschaft bietet Zugriff auf `HttpContext`, `RouteData`, und alles, was andernfalls von MVC und Razor-Seiten bereitgestellt.

Die Verwendung der `Resource` -Eigenschaft ist die spezifische Framework. Mithilfe der Informationen in den `Resource` Eigenschaft schränkt Ihre Autorisierungsrichtlinien auf bestimmten Frameworks. Sollten Sie eine Umwandlung der `Resource` Eigenschaft mithilfe der `is` -Schlüsselwort, und bestätigen Sie dann die Umwandlung erfolgreich war, um sicherzustellen, dass Ihr Code nicht abstürzt. mit einer `InvalidCastException` bei Ausführung auf anderen Frameworks:

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```
