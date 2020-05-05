---
title: Richtlinien Schemas in ASP.net Core
author: rick-anderson
description: Authentifizierungs Richtlinien Schemas vereinfachen die Erstellung eines einzelnen logischen Authentifizierungs Schemas.
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/policyschemes
ms.openlocfilehash: ddedf62c5e8363bd93c9948fd2d3418abc566539
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82767316"
---
# <a name="policy-schemes-in-aspnet-core"></a>Richtlinien Schemas in ASP.net Core

Authentifizierungs Richtlinien Schemas vereinfachen die Verwendung eines einzelnen logischen Authentifizierungs Schemas, das möglicherweise mehrere Ansätze verwendet. Beispielsweise kann ein Richtlinien Schema die Google-Authentifizierung für Herausforderungen und die Cookie-Authentifizierung für alles andere verwenden. Die Authentifizierungs Richtlinien Schemas machen Folgendes:

* Einfache Weiterleiten beliebiger Authentifizierungs Aktionen an ein anderes Schema.
* Basierend auf der Anforderung dynamisch weiterleiten.

Alle Authentifizierungs Schemas, die <xref:Microsoft.AspNetCore.Authentication.AuthenticationSchemeOptions> abgeleitete und die zugeordneten [authenticationhandler\<-toptions>](/dotnet/api/microsoft.aspnetcore.authentication.authenticationhandler-1)verwenden:

* Sind automatisch Richtlinien Schemas in ASP.net Core 2,1 und höher.
* Kann durch Konfigurieren der Schema Optionen aktiviert werden.

[!code-csharp[sample](policyschemes/samples/AuthenticationSchemeOptions.cs?name=snippet)]

## <a name="examples"></a>Beispiele

Das folgende Beispiel zeigt ein Schema höherer Ebene, das Schemata auf niedrigerer Ebene kombiniert. Die Google-Authentifizierung wird für Herausforderungen verwendet, und die Cookie-Authentifizierung wird für alles andere verwendet:

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet1)]

Im folgenden Beispiel wird die dynamische Auswahl von Schemas pro Anforderung aktiviert. Das heißt, es wird erläutert, wie Cookies und API-Authentifizierung gemischt werden:

 <!-- REVIEW, missing If set in public Func<HttpContext, string> ForwardDefaultSelector -->

[!code-csharp[sample](policyschemes/samples/Startup.cs?name=snippet2)]
