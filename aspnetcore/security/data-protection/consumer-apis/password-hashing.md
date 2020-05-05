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
# <a name="hash-passwords-in-aspnet-core"></a><span data-ttu-id="94252-103">Hash Kennwörter in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="94252-103">Hash passwords in ASP.NET Core</span></span>

<span data-ttu-id="94252-104">Die Codebasis für den Datenschutz umfasst das Paket *Microsoft. aspnetcore. Cryptography. keyderivations* , das die Funktionen der kryptografieschlüsselableitung enthält.</span><span class="sxs-lookup"><span data-stu-id="94252-104">The data protection code base includes a package *Microsoft.AspNetCore.Cryptography.KeyDerivation* which contains cryptographic key derivation functions.</span></span> <span data-ttu-id="94252-105">Bei diesem Paket handelt es sich um eine eigenständige Komponente, die keine Abhängigkeiten vom Rest des Datenschutzsystems aufweist.</span><span class="sxs-lookup"><span data-stu-id="94252-105">This package is a standalone component and has no dependencies on the rest of the data protection system.</span></span> <span data-ttu-id="94252-106">Sie kann vollständig unabhängig verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="94252-106">It can be used completely independently.</span></span> <span data-ttu-id="94252-107">Die Quelle ist neben der Datenschutz-Codebasis vorhanden.</span><span class="sxs-lookup"><span data-stu-id="94252-107">The source exists alongside the data protection code base as a convenience.</span></span>

<span data-ttu-id="94252-108">Das Paket bietet derzeit eine Methode `KeyDerivation.Pbkdf2` , die das Hashen eines Kennworts mithilfe des [PBKDF2-Algorithmus](https://tools.ietf.org/html/rfc2898#section-5.2)zulässt.</span><span class="sxs-lookup"><span data-stu-id="94252-108">The package currently offers a method `KeyDerivation.Pbkdf2` which allows hashing a password using the [PBKDF2 algorithm](https://tools.ietf.org/html/rfc2898#section-5.2).</span></span> <span data-ttu-id="94252-109">Diese API ähnelt dem vorhandenen [Rfc2898DeriveBytes-Typ](/dotnet/api/system.security.cryptography.rfc2898derivebytes)der .NET Framework, aber es gibt drei wichtige Unterschiede:</span><span class="sxs-lookup"><span data-stu-id="94252-109">This API is very similar to the .NET Framework's existing [Rfc2898DeriveBytes type](/dotnet/api/system.security.cryptography.rfc2898derivebytes), but there are three important distinctions:</span></span>

1. <span data-ttu-id="94252-110">Die `KeyDerivation.Pbkdf2` -Methode unterstützt `HMACSHA1`die Verwendung mehrerer prfs `HMACSHA256`(zurzeit, und) `Rfc2898DeriveBytes` , während der `HMACSHA1`- `HMACSHA512`Typ nur unterstützt.</span><span class="sxs-lookup"><span data-stu-id="94252-110">The `KeyDerivation.Pbkdf2` method supports consuming multiple PRFs (currently `HMACSHA1`, `HMACSHA256`, and `HMACSHA512`), whereas the `Rfc2898DeriveBytes` type only supports `HMACSHA1`.</span></span>

2. <span data-ttu-id="94252-111">Mit `KeyDerivation.Pbkdf2` der-Methode wird das aktuelle Betriebssystem erkannt, und es wird versucht, die am besten optimierte Implementierung der Routine zu wählen, um in bestimmten Fällen eine viel bessere Leistung zu erzielen</span><span class="sxs-lookup"><span data-stu-id="94252-111">The `KeyDerivation.Pbkdf2` method detects the current operating system and attempts to choose the most optimized implementation of the routine, providing much better performance in certain cases.</span></span> <span data-ttu-id="94252-112">(Unter Windows 8 bietet das Angebot ungefähr Zehnfache den Durchsatz von `Rfc2898DeriveBytes`.)</span><span class="sxs-lookup"><span data-stu-id="94252-112">(On Windows 8, it offers around 10x the throughput of `Rfc2898DeriveBytes`.)</span></span>

3. <span data-ttu-id="94252-113">Die `KeyDerivation.Pbkdf2` -Methode erfordert, dass der Aufrufer alle Parameter (Salt, PRF und Iterations Anzahl) angibt.</span><span class="sxs-lookup"><span data-stu-id="94252-113">The `KeyDerivation.Pbkdf2` method requires the caller to specify all parameters (salt, PRF, and iteration count).</span></span> <span data-ttu-id="94252-114">Der `Rfc2898DeriveBytes` -Typ stellt Standardwerte für diese bereit.</span><span class="sxs-lookup"><span data-stu-id="94252-114">The `Rfc2898DeriveBytes` type provides default values for these.</span></span>

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

<span data-ttu-id="94252-115">Sehen Sie sich den [Quellcode](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) für IdentityASP.net Core `PasswordHasher` -Typ für einen realen Anwendungsfall an.</span><span class="sxs-lookup"><span data-stu-id="94252-115">See the [source code](https://github.com/dotnet/AspNetCore/blob/master/src/Identity/Extensions.Core/src/PasswordHasher.cs) for ASP.NET Core Identity's `PasswordHasher` type for a real-world use case.</span></span>
