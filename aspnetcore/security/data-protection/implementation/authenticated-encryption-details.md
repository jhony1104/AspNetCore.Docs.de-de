---
title: Authentifizierte Verschlüsselungsdetails in ASP.NET Core
author: rick-anderson
description: Erfahren Sie Details zur Implementierung der Verschlüsselung von ASP.NET Core-Datenschutz authentifiziert.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/authenticated-encryption-details
ms.openlocfilehash: 9def03e6b27e19fc34a839e923d6152e086889db
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896627"
---
# <a name="authenticated-encryption-details-in-aspnet-core"></a><span data-ttu-id="7aa21-103">Authentifizierte Verschlüsselungsdetails in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7aa21-103">Authenticated encryption details in ASP.NET Core</span></span>

<a name="data-protection-implementation-authenticated-encryption-details"></a>

<span data-ttu-id="7aa21-104">Aufrufe von IDataProtector.Protect sind authentifizierte Verschlüsselung von Vorgängen.</span><span class="sxs-lookup"><span data-stu-id="7aa21-104">Calls to IDataProtector.Protect are authenticated encryption operations.</span></span> <span data-ttu-id="7aa21-105">Die Protect-Methode bietet sowohl Vertraulichkeit und Authentizität, und es gebunden an die Kette von Zweck, die zum Ableiten von dieser bestimmten Instanz des IDataProtector ab dessen Stammverzeichnis IDataProtectionProvider verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="7aa21-105">The Protect method offers both confidentiality and authenticity, and it's tied to the purpose chain that was used to derive this particular IDataProtector instance from its root IDataProtectionProvider.</span></span>

<span data-ttu-id="7aa21-106">IDataProtector.Protect nimmt einen Byte []-nur-Text-Parameter auf und erzeugt eine Byte [] geschützte Nutzlast, deren Format nachfolgend genauer beschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="7aa21-106">IDataProtector.Protect takes a byte[] plaintext parameter and produces a byte[] protected payload, whose format is described below.</span></span> <span data-ttu-id="7aa21-107">(Es gibt auch eine Überladung der Erweiterung-Methode verwendet einen Zeichenfolgenparameter für nur-Text, und gibt Sie eine geschützte Zeichenfolge-Nutzlast zurück.</span><span class="sxs-lookup"><span data-stu-id="7aa21-107">(There's also an extension method overload which takes a string plaintext parameter and returns a string protected payload.</span></span> <span data-ttu-id="7aa21-108">Wenn diese API verwendet wird das geschützte nutzlastformat haben weiterhin die unterhalb der Struktur, aber sie werden [base64url-codierte](https://tools.ietf.org/html/rfc4648#section-5).)</span><span class="sxs-lookup"><span data-stu-id="7aa21-108">If this API is used the protected payload format will still have the below structure, but it will be [base64url-encoded](https://tools.ietf.org/html/rfc4648#section-5).)</span></span>

## <a name="protected-payload-format"></a><span data-ttu-id="7aa21-109">Geschützte Nutzlast-format</span><span class="sxs-lookup"><span data-stu-id="7aa21-109">Protected payload format</span></span>

<span data-ttu-id="7aa21-110">Die geschützte Nutzlast-Format besteht aus drei Hauptkomponenten:</span><span class="sxs-lookup"><span data-stu-id="7aa21-110">The protected payload format consists of three primary components:</span></span>

* <span data-ttu-id="7aa21-111">Ein 32-Bit-Header-Magic, der die Version des System zum Schutz von Daten angibt.</span><span class="sxs-lookup"><span data-stu-id="7aa21-111">A 32-bit magic header that identifies the version of the data protection system.</span></span>

* <span data-ttu-id="7aa21-112">Eine 128-Bit-Schlüssel-Id, die den Schlüssel zum Schützen dieser bestimmten Nutzlast identifiziert.</span><span class="sxs-lookup"><span data-stu-id="7aa21-112">A 128-bit key id that identifies the key used to protect this particular payload.</span></span>

* <span data-ttu-id="7aa21-113">Der Rest der geschützte Nutzlast ist [speziell für die Verschlüsselung, die von diesem Schlüssel gekapselt](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span><span class="sxs-lookup"><span data-stu-id="7aa21-113">The remainder of the protected payload is [specific to the encryptor encapsulated by this key](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="7aa21-114">Im folgenden Beispiel wird den Schlüssel darstellt, eine AES-256-CBC + HMACSHA256-Verschlüsselung, und die Nutzlast ist weiter wie folgt unterteilt:</span><span class="sxs-lookup"><span data-stu-id="7aa21-114">In the example below, the key represents an AES-256-CBC + HMACSHA256 encryptor, and the payload is further subdivided as follows:</span></span>
  * <span data-ttu-id="7aa21-115">Ein 128-Bit-Schlüssel-Modifizierer.</span><span class="sxs-lookup"><span data-stu-id="7aa21-115">A 128-bit key modifier.</span></span>
  * <span data-ttu-id="7aa21-116">Eine 128-Bit-Initialisierungsvektor.</span><span class="sxs-lookup"><span data-stu-id="7aa21-116">A 128-bit initialization vector.</span></span>
  * <span data-ttu-id="7aa21-117">48 Bytes für den AES-256-CBC-Ausgabe.</span><span class="sxs-lookup"><span data-stu-id="7aa21-117">48 bytes of AES-256-CBC output.</span></span>
  * <span data-ttu-id="7aa21-118">Ein Tag für den HMACSHA256-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="7aa21-118">An HMACSHA256 authentication tag.</span></span>

<span data-ttu-id="7aa21-119">Eine geschützte Beispielnutzlast wird im folgenden dargestellt.</span><span class="sxs-lookup"><span data-stu-id="7aa21-119">A sample protected payload is illustrated below.</span></span>

```
09 F0 C9 F0 80 9C 81 0C 19 66 19 40 95 36 53 F8
AA FF EE 57 57 2F 40 4C 3F 7F CC 9D CC D9 32 3E
84 17 99 16 EC BA 1F 4A A1 18 45 1F 2D 13 7A 28
79 6B 86 9C F8 B7 84 F9 26 31 FC B1 86 0A F1 56
61 CF 14 58 D3 51 6F CF 36 50 85 82 08 2D 3F 73
5F B0 AD 9E 1A B2 AE 13 57 90 C8 F5 7C 95 4E 6A
8A AA 06 EF 43 CA 19 62 84 7C 11 B2 C8 71 9D AA
52 19 2E 5B 4C 1E 54 F0 55 BE 88 92 12 C1 4B 5E
52 C9 74 A0
```

<span data-ttu-id="7aa21-120">Sind nicht mit dem nutzlastformat über die ersten 32 Bits oder 4 Bytes der Magic-Header, die die Version (09 F0 C9 F0) identifiziert</span><span class="sxs-lookup"><span data-stu-id="7aa21-120">From the payload format above the first 32 bits, or 4 bytes are the magic header identifying the version (09 F0 C9 F0)</span></span>

<span data-ttu-id="7aa21-121">Die nächsten 128 Bits oder 16 Bytes ist der Schlüsselbezeichner (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)</span><span class="sxs-lookup"><span data-stu-id="7aa21-121">The next 128 bits, or 16 bytes is the key identifier (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)</span></span>

<span data-ttu-id="7aa21-122">Im weiteren Verlauf enthält die Nutzlast und bezieht sich auf das verwendete Format.</span><span class="sxs-lookup"><span data-stu-id="7aa21-122">The remainder contains the payload and is specific to the format used.</span></span>

> [!WARNING]
> <span data-ttu-id="7aa21-123">Alle Nutzlasten, die auf einen bestimmten Schlüssel geschützt werden mit den gleichen Header von 20-Byte (Magic-Wert, Schlüssel-Id) beginnen.</span><span class="sxs-lookup"><span data-stu-id="7aa21-123">All payloads protected to a given key will begin with the same 20-byte (magic value, key id) header.</span></span> <span data-ttu-id="7aa21-124">Administratoren können diese Tatsache zu Diagnosezwecken verwenden, um zu ermitteln, wenn eine Nutzlast generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="7aa21-124">Administrators can use this fact for diagnostic purposes to approximate when a payload was generated.</span></span> <span data-ttu-id="7aa21-125">Beispielsweise entspricht die Nutzlast, die oben aufgeführten Schlüssel {0c819c80-6619-4019-9536-53f8aaffee57}.</span><span class="sxs-lookup"><span data-stu-id="7aa21-125">For example, the payload above corresponds to key {0c819c80-6619-4019-9536-53f8aaffee57}.</span></span> <span data-ttu-id="7aa21-126">Wenn Sie nach der Überprüfung der wichtigsten Repository feststellen, dass dieser bestimmte Schlüssel Aktivierungsdatum 2015-01-01 wurde und das Ablaufdatum 2015-03-01 wurde, und es sinnvoll ist, Sie davon ausgehen, dass die Nutzlast (sofern Sie nicht manipuliert) innerhalb dieses Fensters, bieten generiert wurde oder nehmen eine kleine Mogelfaktor auf beiden Seiten.</span><span class="sxs-lookup"><span data-stu-id="7aa21-126">If after checking the key repository you find that this specific key's activation date was 2015-01-01 and its expiration date was 2015-03-01, then it's reasonable to assume that the payload (if not tampered with) was generated within that window, give or take a small fudge factor on either side.</span></span>
