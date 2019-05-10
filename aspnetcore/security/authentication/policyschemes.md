---
title: Richtlinie für Schemas, die in ASP.NET Core
author: rick-anderson
description: Richtlinie Authentifizierungsschemas erleichtern es, eine einzelne logische Authentifizierungsschema haben
ms.author: riande
ms.date: 2/28/2019
uid: security/authentication/policyschemes
ms.openlocfilehash: c310b61e14df2b7846e32a602bb75914a5850aff
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64895197"
---
# <a name="policy-schemes-in-aspnet-core"></a>Richtlinie für Schemas, die in ASP.NET Core

Richtlinie Authentifizierungsschemas erleichtern es, damit eine einzelne logische-Authentifizierungsschema, die potenziell mehrere Ansätze verwenden. Beispielsweise kann ein Schema für die Richtlinie für alle anderen Google-Authentifizierung für die Herausforderungen und Cookie-Authentifizierung verwenden. Richtlinie Authentifizierungsschemas erleichtern:

* Einfache Maßnahmen Authentifizierung zu einem anderen Schema weiterleiten.
* Vorlauf dynamisch basierend auf der Anforderung.

Alle Authentifizierungsschemas, die verwenden abgeleiteter <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> und den zugehörigen [ `AuthenticationHandler<TOptions>` ](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1):

* Werden automatisch Richtlinie Schemas in ASP.NET Core 2.1 und höher.
* Kann über das Konfigurieren von Optionen für das Schema der aktiviert werden.

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a>Beispiele

Das folgende Beispiel zeigt eine höhere Ebene Schema, in der unteren Ebene Schemas kombiniert. Google-Authentifizierung wird verwendet, für die Herausforderungen und Cookieauthentifizierung für alle anderen verwendet wird:

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

Im folgenden Beispiel wird die dynamische Auswahl der Schemas auf Grundlage individueller Anforderungen. D. h., wie Sie Cookies und API-Authentifizierung zu kombinieren.

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
