---
title: Schlüsselspeicherformat in ASP.NET Core
author: rick-anderson
description: Erfahren Sie mehr über die Implementierungsdetails des ASP.NET Core Data Protection-Schlüsselspeicherformat.
ms.author: riande
ms.date: 04/08/2020
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 3072c673791b589027a910b80eaba52052eb9311
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976936"
---
# <a name="key-storage-format-in-aspnet-core"></a><span data-ttu-id="e55f2-103">Schlüsselspeicherformat in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e55f2-103">Key storage format in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-format"></a>

<span data-ttu-id="e55f2-104">Objekte werden im Ruhezustand in der XML-Darstellung gespeichert.</span><span class="sxs-lookup"><span data-stu-id="e55f2-104">Objects are stored at rest in XML representation.</span></span> <span data-ttu-id="e55f2-105">Das Standardverzeichnis für die Schlüsselspeicherung lautet:</span><span class="sxs-lookup"><span data-stu-id="e55f2-105">The default directory for key storage is:</span></span>

* <span data-ttu-id="e55f2-106">Windows: \*%LOCALAPPDATA%-ASP.NET-DataProtection-Keys\*</span><span class="sxs-lookup"><span data-stu-id="e55f2-106">Windows: \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span></span>
* <span data-ttu-id="e55f2-107">macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*</span><span class="sxs-lookup"><span data-stu-id="e55f2-107">macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*</span></span>

## <a name="the-key-element"></a><span data-ttu-id="e55f2-108">Das \<schlüsselelemente>-Element</span><span class="sxs-lookup"><span data-stu-id="e55f2-108">The \<key> element</span></span>

<span data-ttu-id="e55f2-109">Schlüssel sind als Objekte der obersten Ebene im Schlüsselrepository vorhanden.</span><span class="sxs-lookup"><span data-stu-id="e55f2-109">Keys exist as top-level objects in the key repository.</span></span> <span data-ttu-id="e55f2-110">Nach Konventionsschlüsseln haben sie den Dateinamen **key-'guid'.xml**, wobei 'guid' die ID des Schlüssels ist.</span><span class="sxs-lookup"><span data-stu-id="e55f2-110">By convention keys have the filename **key-{guid}.xml**, where {guid} is the id of the key.</span></span> <span data-ttu-id="e55f2-111">Jede dieser Dateien enthält einen einzelnen Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="e55f2-111">Each such file contains a single key.</span></span> <span data-ttu-id="e55f2-112">Das Format der Datei ist wie folgt.</span><span class="sxs-lookup"><span data-stu-id="e55f2-112">The format of the file is as follows.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<key id="80732141-ec8f-4b80-af9c-c4d2d1ff8901" version="1">
  <creationDate>2015-03-19T23:32:02.3949887Z</creationDate>
  <activationDate>2015-03-19T23:32:02.3839429Z</activationDate>
  <expirationDate>2015-06-17T23:32:02.3839429Z</expirationDate>
  <descriptor deserializerType="{deserializerType}">
    <descriptor>
      <encryption algorithm="AES_256_CBC" />
      <validation algorithm="HMACSHA256" />
      <enc:encryptedSecret decryptorType="{decryptorType}" xmlns:enc="...">
        <encryptedKey>
          <!-- This key is encrypted with Windows DPAPI. -->
          <value>AQAAANCM...8/zeP8lcwAg==</value>
        </encryptedKey>
      </enc:encryptedSecret>
    </descriptor>
  </descriptor>
</key>
```

<span data-ttu-id="e55f2-113">Der \<Schlüssel>-Elements enthält die folgenden Attribute und untergeordneten Elemente:</span><span class="sxs-lookup"><span data-stu-id="e55f2-113">The \<key> element contains the following attributes and child elements:</span></span>

* <span data-ttu-id="e55f2-114">Die Schlüssel-ID. Dieser Wert wird als autorisierend behandelt. der Dateiname ist einfach eine Nettigkeit für die menschliche Lesbarkeit.</span><span class="sxs-lookup"><span data-stu-id="e55f2-114">The key id. This value is treated as authoritative; the filename is simply a nicety for human readability.</span></span>

* <span data-ttu-id="e55f2-115">Die Version \<des Schlüsselelements> Element, das derzeit auf 1 festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="e55f2-115">The version of the \<key> element, currently fixed at 1.</span></span>

* <span data-ttu-id="e55f2-116">Das Erstellungs-, Aktivierungs- und Ablaufdatum des Schlüssels.</span><span class="sxs-lookup"><span data-stu-id="e55f2-116">The key's creation, activation, and expiration dates.</span></span>

* <span data-ttu-id="e55f2-117">Ein \<Deskriptor>-Element, das Informationen zur authentifizierten Verschlüsselungsimplementierung enthält, die in diesem Schlüssel enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="e55f2-117">A \<descriptor> element, which contains information on the authenticated encryption implementation contained within this key.</span></span>

<span data-ttu-id="e55f2-118">Im obigen Beispiel ist die ID des Schlüssels 80732141-ec8f-4b80-af9c-c4d2d1ff8901, er wurde am 19. März 2015 erstellt und aktiviert und hat eine Lebensdauer von 90 Tagen.</span><span class="sxs-lookup"><span data-stu-id="e55f2-118">In the above example, the key's id is {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, it was created and activated on March 19, 2015, and it has a lifetime of 90 days.</span></span> <span data-ttu-id="e55f2-119">(Gelegentlich liegt das Aktivierungsdatum möglicherweise etwas vor dem Erstellungsdatum wie in diesem Beispiel.</span><span class="sxs-lookup"><span data-stu-id="e55f2-119">(Occasionally the activation date might be slightly before the creation date as in this example.</span></span> <span data-ttu-id="e55f2-120">Dies ist auf eine Nit in wie die APIs arbeiten und ist harmlos in der Praxis.)</span><span class="sxs-lookup"><span data-stu-id="e55f2-120">This is due to a nit in how the APIs work and is harmless in practice.)</span></span>

## <a name="the-descriptor-element"></a><span data-ttu-id="e55f2-121">Der \<Deskriptor>-Element</span><span class="sxs-lookup"><span data-stu-id="e55f2-121">The \<descriptor> element</span></span>

<span data-ttu-id="e55f2-122">Der \<äußere Deskriptor>-Element enthält ein Attribut deserializerType, der Assembly-qualifizierte Name eines Typs ist, der IAuthenticatedEncryptorDescriptorDeserializer implementiert.</span><span class="sxs-lookup"><span data-stu-id="e55f2-122">The outer \<descriptor> element contains an attribute deserializerType, which is the assembly-qualified name of a type which implements IAuthenticatedEncryptorDescriptorDeserializer.</span></span> <span data-ttu-id="e55f2-123">Dieser Typ ist verantwortlich \<für das Lesen des inneren Deskriptors> Elements und für das Analysieren der darin enthaltenen Informationen.</span><span class="sxs-lookup"><span data-stu-id="e55f2-123">This type is responsible for reading the inner \<descriptor> element and for parsing the information contained within.</span></span>

<span data-ttu-id="e55f2-124">Das jeweilige Format \<des Deskriptors>-Elements hängt von der authentifizierten Verschlüsselungsimplementierung ab, die vom Schlüssel gekapselt wird, und jeder Deserialisierungstyp erwartet dafür ein etwas anderes Format.</span><span class="sxs-lookup"><span data-stu-id="e55f2-124">The particular format of the \<descriptor> element depends on the authenticated encryptor implementation encapsulated by the key, and each deserializer type expects a slightly different format for this.</span></span> <span data-ttu-id="e55f2-125">Im Allgemeinen enthält dieses Element jedoch algorithmische Informationen (Namen, Typen, OIDs oder ähnliches) und geheimes Schlüsselmaterial.</span><span class="sxs-lookup"><span data-stu-id="e55f2-125">In general, though, this element will contain algorithmic information (names, types, OIDs, or similar) and secret key material.</span></span> <span data-ttu-id="e55f2-126">Im obigen Beispiel gibt der Deskriptor an, dass dieser Schlüssel die AES-256-CBC-Verschlüsselung + HMACSHA256-Validierung umschließt.</span><span class="sxs-lookup"><span data-stu-id="e55f2-126">In the above example, the descriptor specifies that this key wraps AES-256-CBC encryption + HMACSHA256 validation.</span></span>

## <a name="the-encryptedsecret-element"></a><span data-ttu-id="e55f2-127">Das \<verschlüsselte Secret->-Element</span><span class="sxs-lookup"><span data-stu-id="e55f2-127">The \<encryptedSecret> element</span></span>

<span data-ttu-id="e55f2-128">Ein \*\* &lt;verschlüsseltes&gt; Secret-Element,\*\* das die verschlüsselte Form des geheimen Schlüsselmaterials enthält, kann vorhanden sein, wenn [die Verschlüsselung von Geheimnissen im Ruhezustand aktiviert ist.](xref:security/data-protection/implementation/key-encryption-at-rest)</span><span class="sxs-lookup"><span data-stu-id="e55f2-128">An **&lt;encryptedSecret&gt;** element which contains the encrypted form of the secret key material may be present if [encryption of secrets at rest is enabled](xref:security/data-protection/implementation/key-encryption-at-rest).</span></span> <span data-ttu-id="e55f2-129">Das `decryptorType` Attribut ist der Assembly-qualifizierte Name eines Typs, der [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor)implementiert.</span><span class="sxs-lookup"><span data-stu-id="e55f2-129">The attribute `decryptorType` is the assembly-qualified name of a type which implements [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span></span> <span data-ttu-id="e55f2-130">Dieser Typ ist verantwortlich für das Lesen des inneren \*\* &lt;verschlüsseltenSchlüsselelements&gt; \*\* und das Entschlüsseln, um den ursprünglichen Klartext wiederherzustellen.</span><span class="sxs-lookup"><span data-stu-id="e55f2-130">This type is responsible for reading the inner **&lt;encryptedKey&gt;** element and decrypting it to recover the original plaintext.</span></span>

<span data-ttu-id="e55f2-131">Wie `<descriptor>`bei hängt das `<encryptedSecret>` jeweilige Format des Elements vom verwendungsgemäßen Verschlüsselungsmechanismus ab.</span><span class="sxs-lookup"><span data-stu-id="e55f2-131">As with `<descriptor>`, the particular format of the `<encryptedSecret>` element depends on the at-rest encryption mechanism in use.</span></span> <span data-ttu-id="e55f2-132">Im obigen Beispiel wird der Hauptschlüssel mit Windows DPAPI pro Kommentar verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="e55f2-132">In the above example, the master key is encrypted using Windows DPAPI per the comment.</span></span>

## <a name="the-revocation-element"></a><span data-ttu-id="e55f2-133">Der \<Widerruf>-Element</span><span class="sxs-lookup"><span data-stu-id="e55f2-133">The \<revocation> element</span></span>

<span data-ttu-id="e55f2-134">Sperrungen sind als Objekte der obersten Ebene im Schlüsselrepository vorhanden.</span><span class="sxs-lookup"><span data-stu-id="e55f2-134">Revocations exist as top-level objects in the key repository.</span></span> <span data-ttu-id="e55f2-135">Nach Konventionswiderrufen wird der Dateiname **""timestamp".xml".xml** (zum Widerruf aller Schlüssel vor einem bestimmten Datum) oder **"Widerruf" (zum** Widerruf eines bestimmten Schlüssels) widerrufen.</span><span class="sxs-lookup"><span data-stu-id="e55f2-135">By convention revocations have the filename **revocation-{timestamp}.xml** (for revoking all keys before a specific date) or **revocation-{guid}.xml** (for revoking a specific key).</span></span> <span data-ttu-id="e55f2-136">Jede Datei enthält \<einen einzelnen Widerruf>-Element.</span><span class="sxs-lookup"><span data-stu-id="e55f2-136">Each file contains a single \<revocation> element.</span></span>

<span data-ttu-id="e55f2-137">Bei Widerrufen einzelner Schlüssel wird der Dateiinhalt wie folgt sein.</span><span class="sxs-lookup"><span data-stu-id="e55f2-137">For revocations of individual keys, the file contents will be as below.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="e55f2-138">In diesem Fall wird nur der angegebene Schlüssel widerrufen.</span><span class="sxs-lookup"><span data-stu-id="e55f2-138">In this case, only the specified key is revoked.</span></span> <span data-ttu-id="e55f2-139">Wenn die Schlüssel-ID "\*" lautet, werden jedoch, wie im folgenden Beispiel, alle Schlüssel, deren Erstellungsdatum vor dem angegebenen Widerrufsdatum liegt, widerrufen.</span><span class="sxs-lookup"><span data-stu-id="e55f2-139">If the key id is "\*", however, as in the below example, all keys whose creation date is prior to the specified revocation date are revoked.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="e55f2-140">Der \<Grund> Elements wird nie vom System gelesen.</span><span class="sxs-lookup"><span data-stu-id="e55f2-140">The \<reason> element is never read by the system.</span></span> <span data-ttu-id="e55f2-141">Es ist einfach ein bequemer Ort, um einen menschenlesbaren Grund für den Widerruf zu speichern.</span><span class="sxs-lookup"><span data-stu-id="e55f2-141">It's simply a convenient place to store a human-readable reason for revocation.</span></span>
