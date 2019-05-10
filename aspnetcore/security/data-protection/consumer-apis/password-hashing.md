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
# <a name="hash-passwords-in-aspnet-core"></a><span data-ttu-id="64bc8-103">Hasherstellung für Kennwörter in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="64bc8-103">Hash passwords in ASP.NET Core</span></span>

<span data-ttu-id="64bc8-104">Die Data Protection Codebasis enthält ein Paket *Microsoft.AspNetCore.Cryptography.KeyDerivation* die kryptografischen schlüsselableitung Funktionen enthält.</span><span class="sxs-lookup"><span data-stu-id="64bc8-104">The data protection code base includes a package *Microsoft.AspNetCore.Cryptography.KeyDerivation* which contains cryptographic key derivation functions.</span></span> <span data-ttu-id="64bc8-105">Dieses Paket ist eine eigenständige Komponente, und es ist völlig unabhängig von den restlichen System zum Schutz von Daten.</span><span class="sxs-lookup"><span data-stu-id="64bc8-105">This package is a standalone component and has no dependencies on the rest of the data protection system.</span></span> <span data-ttu-id="64bc8-106">Er kann vollständig unabhängig verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="64bc8-106">It can be used completely independently.</span></span> <span data-ttu-id="64bc8-107">Die Quelle, die zusammen mit der Data Protection Codebasis zur Vereinfachung vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="64bc8-107">The source exists alongside the data protection code base as a convenience.</span></span>

<span data-ttu-id="64bc8-108">Das Paket bietet derzeit eine Methode `KeyDerivation.Pbkdf2` , sodass hashing, ein Kennwort mithilfe der [PBKDF2 Algorithmus](https://tools.ietf.org/html/rfc2898#section-5.2).</span><span class="sxs-lookup"><span data-stu-id="64bc8-108">The package currently offers a method `KeyDerivation.Pbkdf2` which allows hashing a password using the [PBKDF2 algorithm](https://tools.ietf.org/html/rfc2898#section-5.2).</span></span> <span data-ttu-id="64bc8-109">Diese API ist sehr ähnlich zu vorhandenen .NET Framework [Rfc2898DeriveBytes Typ](/dotnet/api/system.security.cryptography.rfc2898derivebytes), aber es drei wichtige Unterschiede gibt:</span><span class="sxs-lookup"><span data-stu-id="64bc8-109">This API is very similar to the .NET Framework's existing [Rfc2898DeriveBytes type](/dotnet/api/system.security.cryptography.rfc2898derivebytes), but there are three important distinctions:</span></span>

1. <span data-ttu-id="64bc8-110">Die `KeyDerivation.Pbkdf2` Methode unterstützt den Verbrauch von mehreren PRFs (derzeit `HMACSHA1`, `HMACSHA256`, und `HMACSHA512`), während die `Rfc2898DeriveBytes` nur unterstützt `HMACSHA1`.</span><span class="sxs-lookup"><span data-stu-id="64bc8-110">The `KeyDerivation.Pbkdf2` method supports consuming multiple PRFs (currently `HMACSHA1`, `HMACSHA256`, and `HMACSHA512`), whereas the `Rfc2898DeriveBytes` type only supports `HMACSHA1`.</span></span>

2. <span data-ttu-id="64bc8-111">Die `KeyDerivation.Pbkdf2` Methode das aktuelle Betriebssystem erkennt und versucht, die am stärksten optimierte Implementierung der Routine, wodurch viel eine bessere Leistung in bestimmten Fällen zu wählen.</span><span class="sxs-lookup"><span data-stu-id="64bc8-111">The `KeyDerivation.Pbkdf2` method detects the current operating system and attempts to choose the most optimized implementation of the routine, providing much better performance in certain cases.</span></span> <span data-ttu-id="64bc8-112">(Unter Windows 8 bietet ca. 10 x den Durchsatz der `Rfc2898DeriveBytes`.)</span><span class="sxs-lookup"><span data-stu-id="64bc8-112">(On Windows 8, it offers around 10x the throughput of `Rfc2898DeriveBytes`.)</span></span>

3. <span data-ttu-id="64bc8-113">Die `KeyDerivation.Pbkdf2` Methode muss den Aufrufer alle Parameter angeben (das Salting PRF und die Iterationsanzahl).</span><span class="sxs-lookup"><span data-stu-id="64bc8-113">The `KeyDerivation.Pbkdf2` method requires the caller to specify all parameters (salt, PRF, and iteration count).</span></span> <span data-ttu-id="64bc8-114">Die `Rfc2898DeriveBytes` Typ stellt die Standardwerte für diese bereit.</span><span class="sxs-lookup"><span data-stu-id="64bc8-114">The `Rfc2898DeriveBytes` type provides default values for these.</span></span>

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

<span data-ttu-id="64bc8-115">Finden Sie unter den [Quellcode](https://github.com/aspnet/Identity/blob/master/src/Core/PasswordHasher.cs) für ASP.NET Core Identity `PasswordHasher` Typ für einen echten Anwendungsfall.</span><span class="sxs-lookup"><span data-stu-id="64bc8-115">See the [source code](https://github.com/aspnet/Identity/blob/master/src/Core/PasswordHasher.cs) for ASP.NET Core Identity's `PasswordHasher` type for a real-world use case.</span></span>
