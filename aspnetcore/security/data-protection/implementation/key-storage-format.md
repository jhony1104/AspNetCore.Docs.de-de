---
title: Schlüsselspeicherformat in ASP.NET Core
author: rick-anderson
description: Erfahren Sie Details zur Implementierung des Schutzes von Daten ASP.NET Core Schlüsselspeicher Formats aus.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 81df124f3dd0cadf8fd895ab55f66eec6415705f
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208017"
---
# <a name="key-storage-format-in-aspnet-core"></a><span data-ttu-id="7be83-103">Schlüsselspeicherformat in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7be83-103">Key storage format in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-format"></a>

<span data-ttu-id="7be83-104">Objekte werden im Ruhezustand in XML-Darstellung gespeichert.</span><span class="sxs-lookup"><span data-stu-id="7be83-104">Objects are stored at rest in XML representation.</span></span> <span data-ttu-id="7be83-105">Das Standardverzeichnis für das Speichern von Schlüsseln ist % LOCALAPPDATA%\ASP.NET\DataProtection-Keys\.</span><span class="sxs-lookup"><span data-stu-id="7be83-105">The default directory for key storage is %LOCALAPPDATA%\ASP.NET\DataProtection-Keys\.</span></span>

## <a name="the-key-element"></a><span data-ttu-id="7be83-106">Die \<Key >-Element</span><span class="sxs-lookup"><span data-stu-id="7be83-106">The \<key> element</span></span>

<span data-ttu-id="7be83-107">Schlüssel, die als Objekte der obersten Ebene im Key-Repository vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="7be83-107">Keys exist as top-level objects in the key repository.</span></span> <span data-ttu-id="7be83-108">Gemäß der Konvention über Schlüssel verfügen, den Dateinamen **Schlüssel: {Guid} .xml**, wobei {Guid} die Id des Schlüssels ist.</span><span class="sxs-lookup"><span data-stu-id="7be83-108">By convention keys have the filename **key-{guid}.xml**, where {guid} is the id of the key.</span></span> <span data-ttu-id="7be83-109">Jede dieser Dateien enthält einen einzelnen Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="7be83-109">Each such file contains a single key.</span></span> <span data-ttu-id="7be83-110">Das Format der Datei lautet wie folgt aus.</span><span class="sxs-lookup"><span data-stu-id="7be83-110">The format of the file is as follows.</span></span>

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

<span data-ttu-id="7be83-111">Die \<Key >-Element enthält die folgenden Attribute und untergeordnete Elemente:</span><span class="sxs-lookup"><span data-stu-id="7be83-111">The \<key> element contains the following attributes and child elements:</span></span>

* <span data-ttu-id="7be83-112">Die Schlüssel-ID. Dieser Wert wird als autorisierend behandelt. der Dateiname ist einfach eine Besonderheit für Menschen lesbar.</span><span class="sxs-lookup"><span data-stu-id="7be83-112">The key id. This value is treated as authoritative; the filename is simply a nicety for human readability.</span></span>

* <span data-ttu-id="7be83-113">Die Version der \<Schlüssel > Element, das zurzeit auf 1 fest.</span><span class="sxs-lookup"><span data-stu-id="7be83-113">The version of the \<key> element, currently fixed at 1.</span></span>

* <span data-ttu-id="7be83-114">Datumsangaben, des Schlüssels erstellen, Aktivierung und Ablauf.</span><span class="sxs-lookup"><span data-stu-id="7be83-114">The key's creation, activation, and expiration dates.</span></span>

* <span data-ttu-id="7be83-115">Ein \<Deskriptor >-Element, das Objektberechtigungsinformationen für die authentifizierte Verschlüsselung-Implementierung, die innerhalb dieser Schlüssel enthält.</span><span class="sxs-lookup"><span data-stu-id="7be83-115">A \<descriptor> element, which contains information on the authenticated encryption implementation contained within this key.</span></span>

<span data-ttu-id="7be83-116">Im obigen Beispiel der mit dem Schlüssel-Id ist {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, Erstellung und der am 19. März 2015, aktiviert und wird eine Lebensdauer von 90 Tagen.</span><span class="sxs-lookup"><span data-stu-id="7be83-116">In the above example, the key's id is {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, it was created and activated on March 19, 2015, and it has a lifetime of 90 days.</span></span> <span data-ttu-id="7be83-117">(Gelegentlich kann das Datum der Aktivierung etwas vor dem Erstellungsdatum wie in diesem Beispiel sein.</span><span class="sxs-lookup"><span data-stu-id="7be83-117">(Occasionally the activation date might be slightly before the creation date as in this example.</span></span> <span data-ttu-id="7be83-118">Dies ist aufgrund einer übertreibenden wie die APIs arbeiten, und ist in der Praxis harmlos.)</span><span class="sxs-lookup"><span data-stu-id="7be83-118">This is due to a nit in how the APIs work and is harmless in practice.)</span></span>

## <a name="the-descriptor-element"></a><span data-ttu-id="7be83-119">Die \<Deskriptor >-Element</span><span class="sxs-lookup"><span data-stu-id="7be83-119">The \<descriptor> element</span></span>

<span data-ttu-id="7be83-120">Die äußere \<Deskriptor >-Element enthält ein Attribut-DeserializerType, wird die Assembly qualifizierten Namen eines Typs der IAuthenticatedEncryptorDescriptorDeserializer implementiert.</span><span class="sxs-lookup"><span data-stu-id="7be83-120">The outer \<descriptor> element contains an attribute deserializerType, which is the assembly-qualified name of a type which implements IAuthenticatedEncryptorDescriptorDeserializer.</span></span> <span data-ttu-id="7be83-121">Dieser Typ ist verantwortlich für das Lesen des inneren \<Deskriptor >-Element und für die Analyse der Informationen in.</span><span class="sxs-lookup"><span data-stu-id="7be83-121">This type is responsible for reading the inner \<descriptor> element and for parsing the information contained within.</span></span>

<span data-ttu-id="7be83-122">Das besondere Format, der die \<Deskriptor >-Elements hängt von der authentifizierte Verschlüsselung Implementierung gekapselt durch den Schlüssel, und jeder Deserialisierer-Typ erwartet, dass ein geringfügig anderes Format für diese.</span><span class="sxs-lookup"><span data-stu-id="7be83-122">The particular format of the \<descriptor> element depends on the authenticated encryptor implementation encapsulated by the key, and each deserializer type expects a slightly different format for this.</span></span> <span data-ttu-id="7be83-123">Im Allgemeinen jedoch dieses Element enthält algorithmische Informationen (Namen, Typen, OIDs, oder ähnliches) und geheimem Schlüsselmaterial.</span><span class="sxs-lookup"><span data-stu-id="7be83-123">In general, though, this element will contain algorithmic information (names, types, OIDs, or similar) and secret key material.</span></span> <span data-ttu-id="7be83-124">Im obigen Beispiel gibt an, dass dieser Schlüssel dient als Wrapper für AES-256-CBC-Verschlüsselung und HMACSHA256-Überprüfung der Deskriptor.</span><span class="sxs-lookup"><span data-stu-id="7be83-124">In the above example, the descriptor specifies that this key wraps AES-256-CBC encryption + HMACSHA256 validation.</span></span>

## <a name="the-encryptedsecret-element"></a><span data-ttu-id="7be83-125">Die \<EncryptedSecret >-Element</span><span class="sxs-lookup"><span data-stu-id="7be83-125">The \<encryptedSecret> element</span></span>

<span data-ttu-id="7be83-126">Ein **&lt;EncryptedSecret&gt;** Element enthält die verschlüsselte Form des Schlüsselmaterials geheimen darf vorhanden sein, wenn [die Verschlüsselung von Geheimnissen im Ruhezustand aktiviert](xref:security/data-protection/implementation/key-encryption-at-rest).</span><span class="sxs-lookup"><span data-stu-id="7be83-126">An **&lt;encryptedSecret&gt;** element which contains the encrypted form of the secret key material may be present if [encryption of secrets at rest is enabled](xref:security/data-protection/implementation/key-encryption-at-rest).</span></span> <span data-ttu-id="7be83-127">Das Attribut `decryptorType` wird die Assembly qualifizierten Namen eines Typs, der implementiert [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span><span class="sxs-lookup"><span data-stu-id="7be83-127">The attribute `decryptorType` is the assembly-qualified name of a type which implements [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span></span> <span data-ttu-id="7be83-128">Dieser Typ ist verantwortlich für das Lesen des inneren **&lt;EncryptedKey&gt;** Element- und entschlüsseln, um den ursprünglichen Klartext zu gewinnen.</span><span class="sxs-lookup"><span data-stu-id="7be83-128">This type is responsible for reading the inner **&lt;encryptedKey&gt;** element and decrypting it to recover the original plaintext.</span></span>

<span data-ttu-id="7be83-129">Wie bei `<descriptor>`, das besondere Format, der die `<encryptedSecret>` -Elements hängt von der im Ruhezustand Verschlüsselungsmechanismus verwendet.</span><span class="sxs-lookup"><span data-stu-id="7be83-129">As with `<descriptor>`, the particular format of the `<encryptedSecret>` element depends on the at-rest encryption mechanism in use.</span></span> <span data-ttu-id="7be83-130">Im obigen Beispiel wird der Hauptschlüssel verschlüsselt mithilfe von Windows-DPAPI pro des Kommentars.</span><span class="sxs-lookup"><span data-stu-id="7be83-130">In the above example, the master key is encrypted using Windows DPAPI per the comment.</span></span>

## <a name="the-revocation-element"></a><span data-ttu-id="7be83-131">Die \<Sperrung >-Element</span><span class="sxs-lookup"><span data-stu-id="7be83-131">The \<revocation> element</span></span>

<span data-ttu-id="7be83-132">Widerrufe sind als Objekte der obersten Ebene im Key-Repository vorhanden.</span><span class="sxs-lookup"><span data-stu-id="7be83-132">Revocations exist as top-level objects in the key repository.</span></span> <span data-ttu-id="7be83-133">Gemäß der Konvention Widerrufe Dateinamen haben **Zertifikatsperrlisten-{Zeitstempel} .xml** (für das Aufheben der alle Schlüssel vor einem bestimmten Datum) oder **Zertifikatsperrlisten-{Guid} .xml** (zum Aufheben von eines bestimmten Schlüssels).</span><span class="sxs-lookup"><span data-stu-id="7be83-133">By convention revocations have the filename **revocation-{timestamp}.xml** (for revoking all keys before a specific date) or **revocation-{guid}.xml** (for revoking a specific key).</span></span> <span data-ttu-id="7be83-134">Jede Datei enthält eine einzelne \<Sperrung > Element.</span><span class="sxs-lookup"><span data-stu-id="7be83-134">Each file contains a single \<revocation> element.</span></span>

<span data-ttu-id="7be83-135">Widerrufe der einzelnen Schlüssel, den Inhalt der Datei werden wie folgt.</span><span class="sxs-lookup"><span data-stu-id="7be83-135">For revocations of individual keys, the file contents will be as below.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="7be83-136">In diesem Fall wird nur auf der angegebene Schlüssel gesperrt.</span><span class="sxs-lookup"><span data-stu-id="7be83-136">In this case, only the specified key is revoked.</span></span> <span data-ttu-id="7be83-137">Wenn der Schlüssel-Id ist "\*", jedoch wie in der folgenden Beispiel werden alle Schlüssel, dessen Datum der Erstellung vor dem angegebenen Sperrdatum wird, widerrufen.</span><span class="sxs-lookup"><span data-stu-id="7be83-137">If the key id is "\*", however, as in the below example, all keys whose creation date is prior to the specified revocation date are revoked.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="7be83-138">Die \<Grund >-Element wird vom System nie gelesen.</span><span class="sxs-lookup"><span data-stu-id="7be83-138">The \<reason> element is never read by the system.</span></span> <span data-ttu-id="7be83-139">Es ist lediglich eine bequeme Möglichkeit, einen lesbaren Grund für die Sperrung zu speichern.</span><span class="sxs-lookup"><span data-stu-id="7be83-139">It's simply a convenient place to store a human-readable reason for revocation.</span></span>
