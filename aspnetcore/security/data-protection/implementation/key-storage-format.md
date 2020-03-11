---
title: Schlüsselspeicher Format in ASP.net Core
author: rick-anderson
description: Erfahren Sie mehr über die Implementierungsdetails des Speicher Formats für die ASP.net Core Datenschutz Schlüssel.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 81df124f3dd0cadf8fd895ab55f66eec6415705f
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654997"
---
# <a name="key-storage-format-in-aspnet-core"></a><span data-ttu-id="67ba7-103">Schlüsselspeicher Format in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="67ba7-103">Key storage format in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-format"></a>

<span data-ttu-id="67ba7-104">Objekte werden im Ruhezustand in der XML-Darstellung gespeichert.</span><span class="sxs-lookup"><span data-stu-id="67ba7-104">Objects are stored at rest in XML representation.</span></span> <span data-ttu-id="67ba7-105">Das Standardverzeichnis für den Schlüsselspeicher ist%LocalAppData%\ASP.net\dataschutz-keys\.</span><span class="sxs-lookup"><span data-stu-id="67ba7-105">The default directory for key storage is %LOCALAPPDATA%\ASP.NET\DataProtection-Keys\.</span></span>

## <a name="the-key-element"></a><span data-ttu-id="67ba7-106">Das \<Key >-Element</span><span class="sxs-lookup"><span data-stu-id="67ba7-106">The \<key> element</span></span>

<span data-ttu-id="67ba7-107">Schlüssel sind im schlüsselrepository als Objekte der obersten Ebene vorhanden.</span><span class="sxs-lookup"><span data-stu-id="67ba7-107">Keys exist as top-level objects in the key repository.</span></span> <span data-ttu-id="67ba7-108">Gemäß den kontokonventionenschlüsseln haben Sie den Dateinamen **Key-{GUID}. XML**, wobei {GUID} die ID des Schlüssels ist.</span><span class="sxs-lookup"><span data-stu-id="67ba7-108">By convention keys have the filename **key-{guid}.xml**, where {guid} is the id of the key.</span></span> <span data-ttu-id="67ba7-109">Jede dieser Dateien enthält einen einzelnen Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="67ba7-109">Each such file contains a single key.</span></span> <span data-ttu-id="67ba7-110">Das Format der Datei lautet wie folgt.</span><span class="sxs-lookup"><span data-stu-id="67ba7-110">The format of the file is as follows.</span></span>

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

<span data-ttu-id="67ba7-111">Das \<Key >-Element enthält die folgenden Attribute und untergeordneten Elemente:</span><span class="sxs-lookup"><span data-stu-id="67ba7-111">The \<key> element contains the following attributes and child elements:</span></span>

* <span data-ttu-id="67ba7-112">Die Schlüssel-ID. Dieser Wert wird als autorisierend behandelt. der Dateiname ist einfach ein nicseinwert für die Lesbarkeit von Menschen.</span><span class="sxs-lookup"><span data-stu-id="67ba7-112">The key id. This value is treated as authoritative; the filename is simply a nicety for human readability.</span></span>

* <span data-ttu-id="67ba7-113">Die Version des \<Schlüssel > Elements, das derzeit bei 1 korrigiert ist.</span><span class="sxs-lookup"><span data-stu-id="67ba7-113">The version of the \<key> element, currently fixed at 1.</span></span>

* <span data-ttu-id="67ba7-114">Die Erstellungs-, Aktivierungs-und Ablaufdatums Angaben des Schlüssels.</span><span class="sxs-lookup"><span data-stu-id="67ba7-114">The key's creation, activation, and expiration dates.</span></span>

* <span data-ttu-id="67ba7-115">Ein \<Deskriptor >-Element, das Informationen zur Implementierung der authentifizierten Verschlüsselung enthält, die in diesem Schlüssel enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="67ba7-115">A \<descriptor> element, which contains information on the authenticated encryption implementation contained within this key.</span></span>

<span data-ttu-id="67ba7-116">Im obigen Beispiel lautet die ID des Schlüssels {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, wurde am 19. März 2015 erstellt und aktiviert und verfügt über eine Gültigkeitsdauer von 90 Tagen.</span><span class="sxs-lookup"><span data-stu-id="67ba7-116">In the above example, the key's id is {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, it was created and activated on March 19, 2015, and it has a lifetime of 90 days.</span></span> <span data-ttu-id="67ba7-117">(Gelegentlich ist das Aktivierungsdatum möglicherweise etwas vor dem Erstellungsdatum, wie in diesem Beispiel.</span><span class="sxs-lookup"><span data-stu-id="67ba7-117">(Occasionally the activation date might be slightly before the creation date as in this example.</span></span> <span data-ttu-id="67ba7-118">Der Grund hierfür ist die Funktionsweise der APIs und ist in der Praxis harmlos.)</span><span class="sxs-lookup"><span data-stu-id="67ba7-118">This is due to a nit in how the APIs work and is harmless in practice.)</span></span>

## <a name="the-descriptor-element"></a><span data-ttu-id="67ba7-119">Das \<Deskriptor > Element</span><span class="sxs-lookup"><span data-stu-id="67ba7-119">The \<descriptor> element</span></span>

<span data-ttu-id="67ba7-120">Das äußere \<Deskriptor >-Element enthält ein deserializertype-Attribut, bei dem es sich um den durch die Assembly qualifizierten Namen eines Typs handelt, der iauthenticatedencryptordescriptordeserializer implementiert.</span><span class="sxs-lookup"><span data-stu-id="67ba7-120">The outer \<descriptor> element contains an attribute deserializerType, which is the assembly-qualified name of a type which implements IAuthenticatedEncryptorDescriptorDeserializer.</span></span> <span data-ttu-id="67ba7-121">Dieser Typ ist für das Lesen des inneren \<Deskriptors >-Elements und für das Auswerten der in enthaltenen Informationen verantwortlich.</span><span class="sxs-lookup"><span data-stu-id="67ba7-121">This type is responsible for reading the inner \<descriptor> element and for parsing the information contained within.</span></span>

<span data-ttu-id="67ba7-122">Das besondere Format des \<Deskriptors >-Elements hängt von der durch den Schlüssel gekapselten authentifizierten Verschlüsselungs Implementierung ab, und jeder deserialisierungstyp erwartet ein etwas anderes Format für dieses.</span><span class="sxs-lookup"><span data-stu-id="67ba7-122">The particular format of the \<descriptor> element depends on the authenticated encryptor implementation encapsulated by the key, and each deserializer type expects a slightly different format for this.</span></span> <span data-ttu-id="67ba7-123">Im Allgemeinen enthält dieses Element jedoch algorithmische Informationen (Namen, Typen, OIDs oder ähnliches) und Geheimnis Schlüsselmaterial.</span><span class="sxs-lookup"><span data-stu-id="67ba7-123">In general, though, this element will contain algorithmic information (names, types, OIDs, or similar) and secret key material.</span></span> <span data-ttu-id="67ba7-124">Im obigen Beispiel gibt der Deskriptor an, dass dieser Schlüssel die AES-256-CBC-Verschlüsselung + HMACSHA256-Überprüfung umschließt.</span><span class="sxs-lookup"><span data-stu-id="67ba7-124">In the above example, the descriptor specifies that this key wraps AES-256-CBC encryption + HMACSHA256 validation.</span></span>

## <a name="the-encryptedsecret-element"></a><span data-ttu-id="67ba7-125">Das \<"verschlüsseltedsecret"-> Element</span><span class="sxs-lookup"><span data-stu-id="67ba7-125">The \<encryptedSecret> element</span></span>

<span data-ttu-id="67ba7-126">Ein **&lt;verschlüsseltedsecret-&gt;** Element, das die verschlüsselte Form des geheimen Schlüssel Materials enthält, kann vorhanden sein, wenn die [Verschlüsselung von geheimen Schlüsseln aktiviert ist](xref:security/data-protection/implementation/key-encryption-at-rest).</span><span class="sxs-lookup"><span data-stu-id="67ba7-126">An **&lt;encryptedSecret&gt;** element which contains the encrypted form of the secret key material may be present if [encryption of secrets at rest is enabled](xref:security/data-protection/implementation/key-encryption-at-rest).</span></span> <span data-ttu-id="67ba7-127">Das Attribut `decryptorType` ist der durch die Assembly qualifizierte Name eines Typs, der [ixmldecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor)implementiert.</span><span class="sxs-lookup"><span data-stu-id="67ba7-127">The attribute `decryptorType` is the assembly-qualified name of a type which implements [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span></span> <span data-ttu-id="67ba7-128">Dieser Typ ist für das Lesen der inneren **&lt;verschlüsseltedkey-&gt;** Elements und das Entschlüsseln der Methode zum Wiederherstellen des ursprünglichen Klartext verantwortlich.</span><span class="sxs-lookup"><span data-stu-id="67ba7-128">This type is responsible for reading the inner **&lt;encryptedKey&gt;** element and decrypting it to recover the original plaintext.</span></span>

<span data-ttu-id="67ba7-129">Wie bei `<descriptor>`hängt das jeweilige Format des `<encryptedSecret>` Elements vom verwendeten ruhenden Verschlüsselungsmechanismus ab.</span><span class="sxs-lookup"><span data-stu-id="67ba7-129">As with `<descriptor>`, the particular format of the `<encryptedSecret>` element depends on the at-rest encryption mechanism in use.</span></span> <span data-ttu-id="67ba7-130">Im obigen Beispiel wird der Hauptschlüssel mit Windows DPAPI gemäß dem Kommentar verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="67ba7-130">In the above example, the master key is encrypted using Windows DPAPI per the comment.</span></span>

## <a name="the-revocation-element"></a><span data-ttu-id="67ba7-131">Das \<-Sperr > Element</span><span class="sxs-lookup"><span data-stu-id="67ba7-131">The \<revocation> element</span></span>

<span data-ttu-id="67ba7-132">Widerrufen sind im schlüsselrepository als Objekte der obersten Ebene vorhanden.</span><span class="sxs-lookup"><span data-stu-id="67ba7-132">Revocations exist as top-level objects in the key repository.</span></span> <span data-ttu-id="67ba7-133">Gemäß der Konvention verfügen Widerrufungen über den Dateinamen " **Widerruf-{Timestamp}. XML** " (zum Aufheben aller Schlüssel vor einem bestimmten Datum) oder " **Widerruf-{GUID}. XML** " (für das Aufheben eines bestimmten Schlüssels).</span><span class="sxs-lookup"><span data-stu-id="67ba7-133">By convention revocations have the filename **revocation-{timestamp}.xml** (for revoking all keys before a specific date) or **revocation-{guid}.xml** (for revoking a specific key).</span></span> <span data-ttu-id="67ba7-134">Jede Datei enthält ein einzelnes \<> Element.</span><span class="sxs-lookup"><span data-stu-id="67ba7-134">Each file contains a single \<revocation> element.</span></span>

<span data-ttu-id="67ba7-135">Für das Aufheben der einzelnen Schlüssel wird der Inhalt der Datei wie unten beschrieben.</span><span class="sxs-lookup"><span data-stu-id="67ba7-135">For revocations of individual keys, the file contents will be as below.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="67ba7-136">In diesem Fall wird nur der angegebene Schlüssel widerrufen.</span><span class="sxs-lookup"><span data-stu-id="67ba7-136">In this case, only the specified key is revoked.</span></span> <span data-ttu-id="67ba7-137">Wenn die Schlüssel-ID jedoch "\*" lautet, wie im folgenden Beispiel, werden alle Schlüssel, deren Erstellungsdatum vor dem angegebenen Sperr Datum liegt, widerrufen.</span><span class="sxs-lookup"><span data-stu-id="67ba7-137">If the key id is "\*", however, as in the below example, all keys whose creation date is prior to the specified revocation date are revoked.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="67ba7-138">Der \<Grund, warum > Element nie vom System gelesen wird.</span><span class="sxs-lookup"><span data-stu-id="67ba7-138">The \<reason> element is never read by the system.</span></span> <span data-ttu-id="67ba7-139">Es ist einfach ein guter Ort, um einen lesbaren Grund für den Widerruf zu speichern.</span><span class="sxs-lookup"><span data-stu-id="67ba7-139">It's simply a convenient place to store a human-readable reason for revocation.</span></span>
