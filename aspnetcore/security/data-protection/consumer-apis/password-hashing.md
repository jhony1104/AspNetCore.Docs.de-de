---
title: Hash Kennwörter in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie Kenn Wörter mithilfe der ASP.net Core-Datenschutz-APIs Kenn Wörtern.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: 6a5e0e4378241671905f2a759aad88372901e7d2
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769779"
---
# <a name="hash-passwords-in-aspnet-core"></a>Hash Kennwörter in ASP.net Core

Die Codebasis für den Datenschutz umfasst das Paket *Microsoft. aspnetcore. Cryptography. keyderivations* , das die Funktionen der kryptografieschlüsselableitung enthält. Bei diesem Paket handelt es sich um eine eigenständige Komponente, die keine Abhängigkeiten vom Rest des Datenschutzsystems aufweist. Sie kann vollständig unabhängig verwendet werden. Die Quelle ist neben der Datenschutz-Codebasis vorhanden.

Das Paket bietet derzeit eine Methode `KeyDerivation.Pbkdf2` , die das Hashen eines Kennworts mithilfe des [PBKDF2-Algorithmus](https://tools.ietf.org/html/rfc2898#section-5.2)zulässt. Diese API ähnelt dem vorhandenen [Rfc2898DeriveBytes-Typ](/dotnet/api/system.security.cryptography.rfc2898derivebytes)der .NET Framework, aber es gibt drei wichtige Unterschiede:

1. Die `KeyDerivation.Pbkdf2` -Methode unterstützt `HMACSHA1`die Verwendung mehrerer prfs `HMACSHA256`(zurzeit, und) `Rfc2898DeriveBytes` , während der `HMACSHA1`- `HMACSHA512`Typ nur unterstützt.

2. Mit `KeyDerivation.Pbkdf2` der-Methode wird das aktuelle Betriebssystem erkannt, und es wird versucht, die am besten optimierte Implementierung der Routine zu wählen, um in bestimmten Fällen eine viel bessere Leistung zu erzielen (Unter Windows 8 bietet das Angebot ungefähr Zehnfache den Durchsatz von `Rfc2898DeriveBytes`.)

3. Die `KeyDerivation.Pbkdf2` -Methode erfordert, dass der Aufrufer alle Parameter (Salt, PRF und Iterations Anzahl) angibt. Der `Rfc2898DeriveBytes` -Typ stellt Standardwerte für diese bereit.

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

Sehen Sie sich den [Quellcode](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) für IdentityASP.net Core `PasswordHasher` -Typ für einen realen Anwendungsfall an.
