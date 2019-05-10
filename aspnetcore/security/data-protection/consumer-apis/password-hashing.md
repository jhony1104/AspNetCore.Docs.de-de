---
title: Hasherstellung für Kennwörter in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Hashing von Kennwörtern mit ASP.NET Core Datenschutz-APIs.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: 70301ffffbaaf3c5ff0642b19b80e40be83aa438
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896647"
---
# <a name="hash-passwords-in-aspnet-core"></a>Hasherstellung für Kennwörter in ASP.NET Core

Die Data Protection Codebasis enthält ein Paket *Microsoft.AspNetCore.Cryptography.KeyDerivation* die kryptografischen schlüsselableitung Funktionen enthält. Dieses Paket ist eine eigenständige Komponente, und es ist völlig unabhängig von den restlichen System zum Schutz von Daten. Er kann vollständig unabhängig verwendet werden. Die Quelle, die zusammen mit der Data Protection Codebasis zur Vereinfachung vorhanden ist.

Das Paket bietet derzeit eine Methode `KeyDerivation.Pbkdf2` , sodass hashing, ein Kennwort mithilfe der [PBKDF2 Algorithmus](https://tools.ietf.org/html/rfc2898#section-5.2). Diese API ist sehr ähnlich zu vorhandenen .NET Framework [Rfc2898DeriveBytes Typ](/dotnet/api/system.security.cryptography.rfc2898derivebytes), aber es drei wichtige Unterschiede gibt:

1. Die `KeyDerivation.Pbkdf2` Methode unterstützt den Verbrauch von mehreren PRFs (derzeit `HMACSHA1`, `HMACSHA256`, und `HMACSHA512`), während die `Rfc2898DeriveBytes` nur unterstützt `HMACSHA1`.

2. Die `KeyDerivation.Pbkdf2` Methode das aktuelle Betriebssystem erkennt und versucht, die am stärksten optimierte Implementierung der Routine, wodurch viel eine bessere Leistung in bestimmten Fällen zu wählen. (Unter Windows 8 bietet ca. 10 x den Durchsatz der `Rfc2898DeriveBytes`.)

3. Die `KeyDerivation.Pbkdf2` Methode muss den Aufrufer alle Parameter angeben (das Salting PRF und die Iterationsanzahl). Die `Rfc2898DeriveBytes` Typ stellt die Standardwerte für diese bereit.

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

Finden Sie unter den [Quellcode](https://github.com/aspnet/Identity/blob/master/src/Core/PasswordHasher.cs) für ASP.NET Core Identity `PasswordHasher` Typ für einen echten Anwendungsfall.
