---
title: Authentifizierungsbeispiele für ASP.NET Core
author: rick-anderson
description: Stellt Links zu den Authentifizierungsbeispielen im ASP.NET Core-Repository bereit.
ms.author: riande
ms.date: 01/31/2019
uid: security/authentication/samples
ms.openlocfilehash: b33eaa5c1bda9e23b2815cd1663e02ae06fec856
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81308214"
---
# <a name="authentication-samples-for-aspnet-core"></a>Authentifizierungsbeispiele für ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Das [ASP.NET Core-Repository](https://github.com/dotnet/AspNetCore) enthält die folgenden Authentifizierungsbeispiele im Ordner *AspNetCore/src/Security/samples:*

* [Transformation von Ansprüchen](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* [Cookie-Authentifizierung](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)
* [Benutzerdefinierter Richtlinienanbieter - IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [Dynamische Authentifizierungsschemata und -optionen](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* [Externe Ansprüche](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)
* [Auswählen zwischen Cookie und einem anderen Authentifizierungsschema basierend auf der Anforderung](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [Beschränkt den Zugriff auf statische Dateien](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Ausführen der Beispiele

* Wählen Sie einen [Zweig](https://github.com/dotnet/AspNetCore)aus. Beispiel: `release/3.1`
* Klonen oder Herunterladen des [ASP.NET Core-Repositorys](https://github.com/dotnet/AspNetCore).
* Stellen Sie sicher, dass Sie die [.NET Core SDK-Version](https://dotnet.microsoft.com/download/dotnet-core) installiert haben, die dem Klon des ASP.NET Core-Repositorys entspricht.
* Navigieren Sie zu einem Beispiel in *AspNetCore/src/Security/samples,* und führen Sie das Beispiel mit `dotnet run`aus.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Das [ASP.NET Core-Repository](https://github.com/dotnet/AspNetCore) enthält die folgenden Authentifizierungsbeispiele im Ordner *AspNetCore/src/Security/samples:*

* [Transformation von Ansprüchen](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/ClaimsTransformation)
* [Cookie-Authentifizierung](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Cookies)
* [Benutzerdefinierter Richtlinienanbieter - IAuthorizationPolicyProvider](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider)
* [Dynamische Authentifizierungsschemata und -optionen](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/DynamicSchemes)
* [Externe Ansprüche](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/Identity.ExternalClaims)
* [Auswählen zwischen Cookie und einem anderen Authentifizierungsschema basierend auf der Anforderung](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/PathSchemeSelection)
* [Beschränkt den Zugriff auf statische Dateien](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a>Ausführen der Beispiele

* Wählen Sie einen [Zweig](https://github.com/dotnet/AspNetCore)aus. Beispiel: `release/2.2`
* Klonen oder Herunterladen des [ASP.NET Core-Repositorys](https://github.com/dotnet/AspNetCore).
* Stellen Sie sicher, dass Sie die [.NET Core SDK-Version](https://dotnet.microsoft.com/download/dotnet-core) installiert haben, die dem Klon des ASP.NET Core-Repositorys entspricht.
* Navigieren Sie zu einem Beispiel in *AspNetCore/src/Security/samples,* und führen Sie das Beispiel mit `dotnet run`aus.

::: moniker-end
