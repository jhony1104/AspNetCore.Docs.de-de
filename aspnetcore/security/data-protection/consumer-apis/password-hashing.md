---
title: Hash Kennwörter in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie Kenn Wörter mithilfe der ASP.net Core-Datenschutz-APIs Kenn Wörtern.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: 52532f9f4d7f9037609c8273deb8b6964d81c714
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651433"
---
# <a name="hash-passwords-in-aspnet-core"></a>Hash Kennwörter in ASP.net Core

Die Codebasis für den Datenschutz umfasst das Paket *Microsoft. aspnetcore. Cryptography. keyderivations* , das die Funktionen der kryptografieschlüsselableitung enthält. Bei diesem Paket handelt es sich um eine eigenständige Komponente, die keine Abhängigkeiten vom Rest des Datenschutzsystems aufweist. Sie kann vollständig unabhängig verwendet werden. Die Quelle ist neben der Datenschutz-Codebasis vorhanden.

Das Paket bietet derzeit eine-Methode `KeyDerivation.Pbkdf2` die das Hashen eines Kennworts mithilfe des [PBKDF2-Algorithmus](https://tools.ietf.org/html/rfc2898#section-5.2)zulässt. Diese API ähnelt dem vorhandenen [Rfc2898DeriveBytes-Typ](/dotnet/api/system.security.cryptography.rfc2898derivebytes)der .NET Framework, aber es gibt drei wichtige Unterschiede:

1. Die `KeyDerivation.Pbkdf2`-Methode unterstützt die Verwendung mehrerer prfs (derzeit `HMACSHA1`, `HMACSHA256`und `HMACSHA512`), während der `Rfc2898DeriveBytes` Typ nur `HMACSHA1`unterstützt.

2. Mit der `KeyDerivation.Pbkdf2`-Methode wird das aktuelle Betriebssystem erkannt, und es wird versucht, die am besten optimierte Implementierung der Routine zu wählen, um in bestimmten Fällen eine deutlich bessere Leistung zu erzielen. (Unter Windows 8 bietet das Angebot ungefähr Zehnfache den Durchsatz von `Rfc2898DeriveBytes`.)

3. Die `KeyDerivation.Pbkdf2`-Methode erfordert, dass der Aufrufer alle Parameter (Salt, PRF und Iterations Anzahl) angibt. Der `Rfc2898DeriveBytes` Typ stellt Standardwerte für diese bereit.

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

Sehen Sie sich den [Quellcode](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) für den `PasswordHasher` Typ der ASP.net Core Identität für einen realen Anwendungsfall an.
