---
title: Schlüsselverwaltungserweiterbarkeit in ASP.NET Core
author: rick-anderson
description: Informationen Sie zu ASP.NET Core-Datenschutz schlüsselverwaltungserweiterbarkeit.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: 28932cbef1cc797338980f3e0de8b09caee324c0
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654259"
---
# <a name="key-management-extensibility-in-aspnet-core"></a><span data-ttu-id="cf97c-103">Schlüsselverwaltungserweiterbarkeit in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cf97c-103">Key management extensibility in ASP.NET Core</span></span>

> [!TIP]
> <span data-ttu-id="cf97c-104">Lesen Sie den Abschnitt [Key Management](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) vor dem Lesen dieses Abschnitts, da hier einige der grundlegenden Konzepte hinter diesen APIs erläutert werden.</span><span class="sxs-lookup"><span data-stu-id="cf97c-104">Read the [key management](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) section before reading this section, as it explains some of the fundamental concepts behind these APIs.</span></span>

> [!WARNING]
> <span data-ttu-id="cf97c-105">Typen, die die folgenden Schnittstellen implementieren, sollten threadsicher werden für mehrere Aufrufer.</span><span class="sxs-lookup"><span data-stu-id="cf97c-105">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="key"></a><span data-ttu-id="cf97c-106">Key</span><span class="sxs-lookup"><span data-stu-id="cf97c-106">Key</span></span>

<span data-ttu-id="cf97c-107">Die `IKey`-Schnittstelle ist die grundlegende Darstellung eines Schlüssels in Cryptosystem.</span><span class="sxs-lookup"><span data-stu-id="cf97c-107">The `IKey` interface is the basic representation of a key in cryptosystem.</span></span> <span data-ttu-id="cf97c-108">Der Begriff-Schlüssel ist hier in der abstrakten Sinne, nicht im literal Sinne "kryptografische Schlüsseldaten" verwendet.</span><span class="sxs-lookup"><span data-stu-id="cf97c-108">The term key is used here in the abstract sense, not in the literal sense of "cryptographic key material".</span></span> <span data-ttu-id="cf97c-109">Ein Schlüssel hat die folgenden Eigenschaften:</span><span class="sxs-lookup"><span data-stu-id="cf97c-109">A key has the following properties:</span></span>

* <span data-ttu-id="cf97c-110">Aktivierung, Erstellung und Ablaufdaten</span><span class="sxs-lookup"><span data-stu-id="cf97c-110">Activation, creation, and expiration dates</span></span>

* <span data-ttu-id="cf97c-111">Sperrstatus</span><span class="sxs-lookup"><span data-stu-id="cf97c-111">Revocation status</span></span>

* <span data-ttu-id="cf97c-112">Schlüsselbezeichner (eine GUID)</span><span class="sxs-lookup"><span data-stu-id="cf97c-112">Key identifier (a GUID)</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="cf97c-113">Darüber hinaus stellt `IKey` eine `CreateEncryptor`-Methode zur Verfügung, die zum Erstellen einer [iauthenticatedencryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) -Instanz verwendet werden kann, die mit diesem Schlüssel verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="cf97c-113">Additionally, `IKey` exposes a `CreateEncryptor` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="cf97c-114">Darüber hinaus stellt `IKey` eine `CreateEncryptorInstance`-Methode zur Verfügung, die zum Erstellen einer [iauthenticatedencryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) -Instanz verwendet werden kann, die mit diesem Schlüssel verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="cf97c-114">Additionally, `IKey` exposes a `CreateEncryptorInstance` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="cf97c-115">Es gibt keine API, um das unformatierte kryptografiematerial aus einer `IKey` Instanz abzurufen.</span><span class="sxs-lookup"><span data-stu-id="cf97c-115">There's no API to retrieve the raw cryptographic material from an `IKey` instance.</span></span>

## <a name="ikeymanager"></a><span data-ttu-id="cf97c-116">IKeyManager</span><span class="sxs-lookup"><span data-stu-id="cf97c-116">IKeyManager</span></span>

<span data-ttu-id="cf97c-117">Die `IKeyManager`-Schnittstelle stellt ein Objekt dar, das für das Speichern, abrufen und manipulieren allgemeiner Schlüssel zuständig ist.</span><span class="sxs-lookup"><span data-stu-id="cf97c-117">The `IKeyManager` interface represents an object responsible for general key storage, retrieval, and manipulation.</span></span> <span data-ttu-id="cf97c-118">Sie macht drei allgemeine Vorgänge verfügbar:</span><span class="sxs-lookup"><span data-stu-id="cf97c-118">It exposes three high-level operations:</span></span>

* <span data-ttu-id="cf97c-119">Erstellen Sie einen neuen Schlüssel, und speichern Sie die Daten in den Speicher.</span><span class="sxs-lookup"><span data-stu-id="cf97c-119">Create a new key and persist it to storage.</span></span>

* <span data-ttu-id="cf97c-120">Alle Schlüssel aus dem Speicher abrufen.</span><span class="sxs-lookup"><span data-stu-id="cf97c-120">Get all keys from storage.</span></span>

* <span data-ttu-id="cf97c-121">Einen oder mehrere Schlüssel widerrufen, und die Sperrinformationen in den Speicher beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="cf97c-121">Revoke one or more keys and persist the revocation information to storage.</span></span>

>[!WARNING]
> <span data-ttu-id="cf97c-122">Das Schreiben einer `IKeyManager` ist eine sehr fortschrittliche Aufgabe, und die Mehrzahl der Entwickler sollten Sie nicht versuchen.</span><span class="sxs-lookup"><span data-stu-id="cf97c-122">Writing an `IKeyManager` is a very advanced task, and the majority of developers shouldn't attempt it.</span></span> <span data-ttu-id="cf97c-123">Stattdessen sollten die meisten Entwickler die Funktionen nutzen, die von der [xmlkeymanager](#xmlkeymanager) -Klasse angeboten werden.</span><span class="sxs-lookup"><span data-stu-id="cf97c-123">Instead, most developers should take advantage of the facilities offered by the [XmlKeyManager](#xmlkeymanager) class.</span></span>

## <a name="xmlkeymanager"></a><span data-ttu-id="cf97c-124">XmlKeyManager</span><span class="sxs-lookup"><span data-stu-id="cf97c-124">XmlKeyManager</span></span>

<span data-ttu-id="cf97c-125">Der `XmlKeyManager` Typ ist die in-Box konkrete Implementierung von `IKeyManager`.</span><span class="sxs-lookup"><span data-stu-id="cf97c-125">The `XmlKeyManager` type is the in-box concrete implementation of `IKeyManager`.</span></span> <span data-ttu-id="cf97c-126">Es bietet mehrere nützliche Funktionen, einschließlich schlüsselhinterlegung und Verschlüsselung von Schlüsseln im ruhenden Zustand.</span><span class="sxs-lookup"><span data-stu-id="cf97c-126">It provides several useful facilities, including key escrow and encryption of keys at rest.</span></span> <span data-ttu-id="cf97c-127">Schlüssel in diesem System werden als XML-Elemente (insbesondere [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)) dargestellt.</span><span class="sxs-lookup"><span data-stu-id="cf97c-127">Keys in this system are represented as XML elements (specifically, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span></span>

<span data-ttu-id="cf97c-128">`XmlKeyManager` hängt von mehreren anderen Komponenten im Rahmen der Erfüllung der Aufgaben ab:</span><span class="sxs-lookup"><span data-stu-id="cf97c-128">`XmlKeyManager` depends on several other components in the course of fulfilling its tasks:</span></span>

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="cf97c-129">`AlgorithmConfiguration`, das die von neuen Schlüsseln verwendeten Algorithmen bestimmt.</span><span class="sxs-lookup"><span data-stu-id="cf97c-129">`AlgorithmConfiguration`, which dictates the algorithms used by new keys.</span></span>

* <span data-ttu-id="cf97c-130">`IXmlRepository`, der steuert, wo Schlüssel im Speicher gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="cf97c-130">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="cf97c-131">`IXmlEncryptor` [optional], das das Verschlüsseln von Schlüsseln im Ruhezustand ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="cf97c-131">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="cf97c-132">`IKeyEscrowSink` [optional], das Schlüssel Hinterlegungs Dienste bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="cf97c-132">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="cf97c-133">`IXmlRepository`, der steuert, wo Schlüssel im Speicher gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="cf97c-133">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="cf97c-134">`IXmlEncryptor` [optional], das das Verschlüsseln von Schlüsseln im Ruhezustand ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="cf97c-134">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="cf97c-135">`IKeyEscrowSink` [optional], das Schlüssel Hinterlegungs Dienste bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="cf97c-135">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

<span data-ttu-id="cf97c-136">Im folgenden finden Sie allgemeine Diagramme, die angeben, wie diese Komponenten innerhalb `XmlKeyManager`miteinander verbunden werden.</span><span class="sxs-lookup"><span data-stu-id="cf97c-136">Below are high-level diagrams which indicate how these components are wired together within `XmlKeyManager`.</span></span>

::: moniker range=">= aspnetcore-2.0"

![Schlüssel erstellen](key-management/_static/keycreation2.png)

<span data-ttu-id="cf97c-138">*Key Creation/kreatenewkey*</span><span class="sxs-lookup"><span data-stu-id="cf97c-138">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="cf97c-139">In der Implementierung von `CreateNewKey`wird die `AlgorithmConfiguration` Komponente verwendet, um eine eindeutige `IAuthenticatedEncryptorDescriptor`zu erstellen, die dann als XML serialisiert wird.</span><span class="sxs-lookup"><span data-stu-id="cf97c-139">In the implementation of `CreateNewKey`, the `AlgorithmConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="cf97c-140">Wenn eine Senke schlüsselhinterlegung vorhanden ist, wird der XML-Rohdaten (unverschlüsselt) für die langfristige Speicherung an die Senke bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="cf97c-140">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="cf97c-141">Der unverschlüsselte XML-Code wird dann durch eine `IXmlEncryptor` (falls erforderlich) ausgeführt, um das verschlüsselte XML-Dokument zu generieren.</span><span class="sxs-lookup"><span data-stu-id="cf97c-141">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="cf97c-142">Dieses verschlüsselte Dokument wird in der langfristigen Speicherung über die `IXmlRepository`persistent gespeichert.</span><span class="sxs-lookup"><span data-stu-id="cf97c-142">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="cf97c-143">(Wenn keine `IXmlEncryptor` konfiguriert ist, wird das unverschlüsselte Dokument dauerhaft in der `IXmlRepository`gespeichert.)</span><span class="sxs-lookup"><span data-stu-id="cf97c-143">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![Abrufen eines Schlüssels](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![Schlüssel erstellen](key-management/_static/keycreation1.png)

<span data-ttu-id="cf97c-146">*Key Creation/kreatenewkey*</span><span class="sxs-lookup"><span data-stu-id="cf97c-146">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="cf97c-147">In der Implementierung von `CreateNewKey`wird die `IAuthenticatedEncryptorConfiguration` Komponente verwendet, um eine eindeutige `IAuthenticatedEncryptorDescriptor`zu erstellen, die dann als XML serialisiert wird.</span><span class="sxs-lookup"><span data-stu-id="cf97c-147">In the implementation of `CreateNewKey`, the `IAuthenticatedEncryptorConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="cf97c-148">Wenn eine Senke schlüsselhinterlegung vorhanden ist, wird der XML-Rohdaten (unverschlüsselt) für die langfristige Speicherung an die Senke bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="cf97c-148">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="cf97c-149">Der unverschlüsselte XML-Code wird dann durch eine `IXmlEncryptor` (falls erforderlich) ausgeführt, um das verschlüsselte XML-Dokument zu generieren.</span><span class="sxs-lookup"><span data-stu-id="cf97c-149">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="cf97c-150">Dieses verschlüsselte Dokument wird in der langfristigen Speicherung über die `IXmlRepository`persistent gespeichert.</span><span class="sxs-lookup"><span data-stu-id="cf97c-150">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="cf97c-151">(Wenn keine `IXmlEncryptor` konfiguriert ist, wird das unverschlüsselte Dokument dauerhaft in der `IXmlRepository`gespeichert.)</span><span class="sxs-lookup"><span data-stu-id="cf97c-151">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![Abrufen eines Schlüssels](key-management/_static/keyretrieval1.png)

::: moniker-end

<span data-ttu-id="cf97c-153">*Schlüssel Abruf/getallkeys*</span><span class="sxs-lookup"><span data-stu-id="cf97c-153">*Key Retrieval / GetAllKeys*</span></span>

<span data-ttu-id="cf97c-154">Bei der Implementierung von `GetAllKeys`werden die XML-Dokumente, die Schlüssel und widerrufen darstellen, aus der zugrunde liegenden `IXmlRepository`gelesen.</span><span class="sxs-lookup"><span data-stu-id="cf97c-154">In the implementation of `GetAllKeys`, the XML documents representing keys and revocations are read from the underlying `IXmlRepository`.</span></span> <span data-ttu-id="cf97c-155">Wenn diese Dokumente verschlüsselt sind, wird das System diese automatisch entschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="cf97c-155">If these documents are encrypted, the system will automatically decrypt them.</span></span> <span data-ttu-id="cf97c-156">`XmlKeyManager` erstellt die entsprechenden `IAuthenticatedEncryptorDescriptorDeserializer` Instanzen, um die Dokumente wieder in `IAuthenticatedEncryptorDescriptor` Instanzen zu deserialisieren, die anschließend in einzelne `IKey` Instanzen umschließt werden.</span><span class="sxs-lookup"><span data-stu-id="cf97c-156">`XmlKeyManager` creates the appropriate `IAuthenticatedEncryptorDescriptorDeserializer` instances to deserialize the documents back into `IAuthenticatedEncryptorDescriptor` instances, which are then wrapped in individual `IKey` instances.</span></span> <span data-ttu-id="cf97c-157">Diese Auflistung von `IKey` Instanzen wird an den Aufrufer zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="cf97c-157">This collection of `IKey` instances is returned to the caller.</span></span>

<span data-ttu-id="cf97c-158">Weitere Informationen zu den einzelnen XML-Elementen finden Sie im [Dokument Schlüsselspeicher Format](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span><span class="sxs-lookup"><span data-stu-id="cf97c-158">Further information on the particular XML elements can be found in the [key storage format document](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span></span>

## <a name="ixmlrepository"></a><span data-ttu-id="cf97c-159">IXmlRepository</span><span class="sxs-lookup"><span data-stu-id="cf97c-159">IXmlRepository</span></span>

<span data-ttu-id="cf97c-160">Die `IXmlRepository`-Schnittstelle stellt einen Typ dar, der XML-Daten dauerhaft speichern und aus einem Sicherungs Speicher abrufen kann.</span><span class="sxs-lookup"><span data-stu-id="cf97c-160">The `IXmlRepository` interface represents a type that can persist XML to and retrieve XML from a backing store.</span></span> <span data-ttu-id="cf97c-161">Es macht zwei APIs verfügbar:</span><span class="sxs-lookup"><span data-stu-id="cf97c-161">It exposes two APIs:</span></span>

* <span data-ttu-id="cf97c-162">`GetAllElements`:`IReadOnlyCollection<XElement>`</span><span class="sxs-lookup"><span data-stu-id="cf97c-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span></span>

* `StoreElement(XElement element, string friendlyName)`

<span data-ttu-id="cf97c-163">Implementierungen von `IXmlRepository` müssen den XML-Code nicht analysieren.</span><span class="sxs-lookup"><span data-stu-id="cf97c-163">Implementations of `IXmlRepository` don't need to parse the XML passing through them.</span></span> <span data-ttu-id="cf97c-164">Sie sollten die XML-Dokumente als nicht transparent behandeln, die höhere Schichten kümmern, generieren und die Dokumente verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="cf97c-164">They should treat the XML documents as opaque and let higher layers worry about generating and parsing the documents.</span></span>

<span data-ttu-id="cf97c-165">Es gibt vier integrierte, konkrete Typen, die `IXmlRepository`implementieren:</span><span class="sxs-lookup"><span data-stu-id="cf97c-165">There are four built-in concrete types which implement `IXmlRepository`:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="cf97c-166">Filesystemxmlrepository</span><span class="sxs-lookup"><span data-stu-id="cf97c-166">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="cf97c-167">Registryxmlrepository</span><span class="sxs-lookup"><span data-stu-id="cf97c-167">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="cf97c-168">Azurestorage. azureblobxmlrepository</span><span class="sxs-lookup"><span data-stu-id="cf97c-168">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="cf97c-169">Redisxmlrepository</span><span class="sxs-lookup"><span data-stu-id="cf97c-169">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="cf97c-170">Filesystemxmlrepository</span><span class="sxs-lookup"><span data-stu-id="cf97c-170">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="cf97c-171">Registryxmlrepository</span><span class="sxs-lookup"><span data-stu-id="cf97c-171">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="cf97c-172">Azurestorage. azureblobxmlrepository</span><span class="sxs-lookup"><span data-stu-id="cf97c-172">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="cf97c-173">Redisxmlrepository</span><span class="sxs-lookup"><span data-stu-id="cf97c-173">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

<span data-ttu-id="cf97c-174">Weitere Informationen finden Sie im [Dokument Schlüsselspeicher Anbieter](xref:security/data-protection/implementation/key-storage-providers) .</span><span class="sxs-lookup"><span data-stu-id="cf97c-174">See the [key storage providers document](xref:security/data-protection/implementation/key-storage-providers) for more information.</span></span>

<span data-ttu-id="cf97c-175">Die Registrierung eines benutzerdefinierten `IXmlRepository` ist geeignet, wenn ein anderer Sicherungs Speicher (z. b. Azure Table Storage) verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="cf97c-175">Registering a custom `IXmlRepository` is appropriate when using a different backing store (for example, Azure Table Storage).</span></span>

<span data-ttu-id="cf97c-176">Wenn Sie das standardrepository Anwendungs weit ändern möchten, registrieren Sie eine benutzerdefinierte `IXmlRepository` Instanz:</span><span class="sxs-lookup"><span data-stu-id="cf97c-176">To change the default repository application-wide, register a custom `IXmlRepository` instance:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlRepository = new MyCustomXmlRepository());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlRepository>(new MyCustomXmlRepository());
```

::: moniker-end

## <a name="ixmlencryptor"></a><span data-ttu-id="cf97c-177">IXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="cf97c-177">IXmlEncryptor</span></span>

<span data-ttu-id="cf97c-178">Die `IXmlEncryptor`-Schnittstelle stellt einen Typ dar, der ein Klartext-XML-Element verschlüsseln kann.</span><span class="sxs-lookup"><span data-stu-id="cf97c-178">The `IXmlEncryptor` interface represents a type that can encrypt a plaintext XML element.</span></span> <span data-ttu-id="cf97c-179">Sie macht eine einzelne API verfügbar:</span><span class="sxs-lookup"><span data-stu-id="cf97c-179">It exposes a single API:</span></span>

* <span data-ttu-id="cf97c-180">("XElement" PlaintextElement) zu verschlüsseln: EncryptedXmlInfo</span><span class="sxs-lookup"><span data-stu-id="cf97c-180">Encrypt(XElement plaintextElement) : EncryptedXmlInfo</span></span>

<span data-ttu-id="cf97c-181">Wenn ein serialisiertes `IAuthenticatedEncryptorDescriptor` Elemente enthält, die als "erfordert Verschlüsselung" gekennzeichnet sind, werden diese Elemente von `XmlKeyManager` über die `Encrypt`-Methode des konfigurierten `IXmlEncryptor`ausgeführt, und es wird das enchiffrierte Element anstelle des Klartext-Elements in den `IXmlRepository`persistent gespeichert.</span><span class="sxs-lookup"><span data-stu-id="cf97c-181">If a serialized `IAuthenticatedEncryptorDescriptor` contains any elements marked as "requires encryption", then `XmlKeyManager` will run those elements through the configured `IXmlEncryptor`'s `Encrypt` method, and it will persist the enciphered element rather than the plaintext element to the `IXmlRepository`.</span></span> <span data-ttu-id="cf97c-182">Die Ausgabe der `Encrypt`-Methode ist ein `EncryptedXmlInfo` Objekt.</span><span class="sxs-lookup"><span data-stu-id="cf97c-182">The output of the `Encrypt` method is an `EncryptedXmlInfo` object.</span></span> <span data-ttu-id="cf97c-183">Bei diesem Objekt handelt es sich um einen Wrapper, der sowohl das resultierende `XElement` als auch den Typ enthält, der ein `IXmlDecryptor` darstellt, mit dem das entsprechende Element entschlüsselt werden kann.</span><span class="sxs-lookup"><span data-stu-id="cf97c-183">This object is a wrapper which contains both the resultant enciphered `XElement` and the Type which represents an `IXmlDecryptor` which can be used to decipher the corresponding element.</span></span>

<span data-ttu-id="cf97c-184">Es gibt vier integrierte, konkrete Typen, die `IXmlEncryptor`implementieren:</span><span class="sxs-lookup"><span data-stu-id="cf97c-184">There are four built-in concrete types which implement `IXmlEncryptor`:</span></span>

* [<span data-ttu-id="cf97c-185">Certifi-exmlencryptor</span><span class="sxs-lookup"><span data-stu-id="cf97c-185">CertificateXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [<span data-ttu-id="cf97c-186">Dpapingxmlencryptor</span><span class="sxs-lookup"><span data-stu-id="cf97c-186">DpapiNGXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [<span data-ttu-id="cf97c-187">Dpapixmlencryptor</span><span class="sxs-lookup"><span data-stu-id="cf97c-187">DpapiXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [<span data-ttu-id="cf97c-188">Nullxmlencryptor</span><span class="sxs-lookup"><span data-stu-id="cf97c-188">NullXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

<span data-ttu-id="cf97c-189">Weitere Informationen finden Sie im [Dokument Key Encryption at Rest](xref:security/data-protection/implementation/key-encryption-at-rest) .</span><span class="sxs-lookup"><span data-stu-id="cf97c-189">See the [key encryption at rest document](xref:security/data-protection/implementation/key-encryption-at-rest) for more information.</span></span>

<span data-ttu-id="cf97c-190">Registrieren Sie eine benutzerdefinierte `IXmlEncryptor` Instanz, um den standardmäßigen Rest-Mechanismus für die Schlüssel Verschlüsselung zu ändern:</span><span class="sxs-lookup"><span data-stu-id="cf97c-190">To change the default key-encryption-at-rest mechanism application-wide, register a custom `IXmlEncryptor` instance:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.Configure<KeyManagementOptions>(options => options.XmlEncryptor = new MyCustomXmlEncryptor());
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddSingleton<IXmlEncryptor>(new MyCustomXmlEncryptor());
```

::: moniker-end

## <a name="ixmldecryptor"></a><span data-ttu-id="cf97c-191">IXmlDecryptor</span><span class="sxs-lookup"><span data-stu-id="cf97c-191">IXmlDecryptor</span></span>

<span data-ttu-id="cf97c-192">Die `IXmlDecryptor`-Schnittstelle stellt einen Typ dar, der weiß, wie ein `XElement` entschlüsselt wird, das über einen `IXmlEncryptor`verschlüsselt wurde.</span><span class="sxs-lookup"><span data-stu-id="cf97c-192">The `IXmlDecryptor` interface represents a type that knows how to decrypt an `XElement` that was enciphered via an `IXmlEncryptor`.</span></span> <span data-ttu-id="cf97c-193">Sie macht eine einzelne API verfügbar:</span><span class="sxs-lookup"><span data-stu-id="cf97c-193">It exposes a single API:</span></span>

* <span data-ttu-id="cf97c-194">Entschlüsseln ("XElement" EncryptedElement): "XElement"</span><span class="sxs-lookup"><span data-stu-id="cf97c-194">Decrypt(XElement encryptedElement) : XElement</span></span>

<span data-ttu-id="cf97c-195">Die `Decrypt`-Methode macht die von `IXmlEncryptor.Encrypt`ausgeführte Verschlüsselung nicht mehr.</span><span class="sxs-lookup"><span data-stu-id="cf97c-195">The `Decrypt` method undoes the encryption performed by `IXmlEncryptor.Encrypt`.</span></span> <span data-ttu-id="cf97c-196">Im allgemeinen verfügt jede konkrete `IXmlEncryptor` Implementierung über eine entsprechende konkrete `IXmlDecryptor` Implementierung.</span><span class="sxs-lookup"><span data-stu-id="cf97c-196">Generally, each concrete `IXmlEncryptor` implementation will have a corresponding concrete `IXmlDecryptor` implementation.</span></span>

<span data-ttu-id="cf97c-197">Typen, die `IXmlDecryptor` implementieren, sollten einen der folgenden zwei öffentlichen Konstruktoren aufweisen:</span><span class="sxs-lookup"><span data-stu-id="cf97c-197">Types which implement `IXmlDecryptor` should have one of the following two public constructors:</span></span>

* <span data-ttu-id="cf97c-198">.ctor(IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="cf97c-198">.ctor(IServiceProvider)</span></span>
* <span data-ttu-id="cf97c-199">.ctor()</span><span class="sxs-lookup"><span data-stu-id="cf97c-199">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="cf97c-200">Die an den Konstruktor übergeben `IServiceProvider` kann NULL sein.</span><span class="sxs-lookup"><span data-stu-id="cf97c-200">The `IServiceProvider` passed to the constructor may be null.</span></span>

## <a name="ikeyescrowsink"></a><span data-ttu-id="cf97c-201">IKeyEscrowSink</span><span class="sxs-lookup"><span data-stu-id="cf97c-201">IKeyEscrowSink</span></span>

<span data-ttu-id="cf97c-202">Die `IKeyEscrowSink`-Schnittstelle stellt einen Typ dar, der die Hinterlegung vertraulicher Informationen durchführen kann.</span><span class="sxs-lookup"><span data-stu-id="cf97c-202">The `IKeyEscrowSink` interface represents a type that can perform escrow of sensitive information.</span></span> <span data-ttu-id="cf97c-203">Beachten Sie, dass serialisierte Deskriptoren vertrauliche Informationen (z. b. kryptografische Materialien) enthalten können. Dies hat zur Einführung des Typs [ixmlencryptor](#ixmlencryptor) geführt.</span><span class="sxs-lookup"><span data-stu-id="cf97c-203">Recall that serialized descriptors might contain sensitive information (such as cryptographic material), and this is what led to the introduction of the [IXmlEncryptor](#ixmlencryptor) type in the first place.</span></span> <span data-ttu-id="cf97c-204">Allerdings Unfälle passieren, und Schlüssel Ringe können gelöscht werden, oder beschädigt.</span><span class="sxs-lookup"><span data-stu-id="cf97c-204">However, accidents happen, and key rings can be deleted or become corrupted.</span></span>

<span data-ttu-id="cf97c-205">Die Hinterlegungs Schnittstelle stellt eine Notfall-escapeschraffur dar und ermöglicht den Zugriff auf die unformatierte serialisierte XML, bevor Sie von einem konfigurierten [ixmlencryptor](#ixmlencryptor)transformiert wird.</span><span class="sxs-lookup"><span data-stu-id="cf97c-205">The escrow interface provides an emergency escape hatch, allowing access to the raw serialized XML before it's transformed by any configured [IXmlEncryptor](#ixmlencryptor).</span></span> <span data-ttu-id="cf97c-206">Die Schnittstelle macht eine einzelne API verfügbar:</span><span class="sxs-lookup"><span data-stu-id="cf97c-206">The interface exposes a single API:</span></span>

* <span data-ttu-id="cf97c-207">Store (Guid-Schlüssel-ID, "XElement"-Element)</span><span class="sxs-lookup"><span data-stu-id="cf97c-207">Store(Guid keyId, XElement element)</span></span>

<span data-ttu-id="cf97c-208">Es liegt an der `IKeyEscrowSink` Implementierung, das bereitgestellte Element auf sichere Weise in Übereinstimmung mit der Geschäfts Richtlinie zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="cf97c-208">It's up to the `IKeyEscrowSink` implementation to handle the provided element in a secure manner consistent with business policy.</span></span> <span data-ttu-id="cf97c-209">Eine mögliche Implementierung könnte sein, dass die hinterlegte Senke das XML-Element mit einem bekannten Unternehmens-X. 509-Zertifikat verschlüsselt, bei dem der private Schlüssel des Zertifikats verwendet wurde. der `CertificateXmlEncryptor`-Typ kann dies unterstützen.</span><span class="sxs-lookup"><span data-stu-id="cf97c-209">One possible implementation could be for the escrow sink to encrypt the XML element using a known corporate X.509 certificate where the certificate's private key has been escrowed; the `CertificateXmlEncryptor` type can assist with this.</span></span> <span data-ttu-id="cf97c-210">Die `IKeyEscrowSink`-Implementierung ist auch dafür verantwortlich, das bereitgestellte Element entsprechend beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="cf97c-210">The `IKeyEscrowSink` implementation is also responsible for persisting the provided element appropriately.</span></span>

<span data-ttu-id="cf97c-211">Standardmäßig ist kein Hinterlegungs Mechanismus aktiviert, aber Server Administratoren können [Dies Global konfigurieren](xref:security/data-protection/configuration/machine-wide-policy).</span><span class="sxs-lookup"><span data-stu-id="cf97c-211">By default no escrow mechanism is enabled, though server administrators can [configure this globally](xref:security/data-protection/configuration/machine-wide-policy).</span></span> <span data-ttu-id="cf97c-212">Sie kann auch Programm gesteuert über die `IDataProtectionBuilder.AddKeyEscrowSink`-Methode konfiguriert werden, wie im folgenden Beispiel gezeigt.</span><span class="sxs-lookup"><span data-stu-id="cf97c-212">It can also be configured programmatically via the `IDataProtectionBuilder.AddKeyEscrowSink` method as shown in the sample below.</span></span> <span data-ttu-id="cf97c-213">Die `AddKeyEscrowSink`-Methoden Überladungen spiegeln die `IServiceCollection.AddSingleton` und `IServiceCollection.AddInstance` Überladungen wider, da `IKeyEscrowSink` Instanzen als Singletons gedacht sind.</span><span class="sxs-lookup"><span data-stu-id="cf97c-213">The `AddKeyEscrowSink` method overloads mirror the `IServiceCollection.AddSingleton` and `IServiceCollection.AddInstance` overloads, as `IKeyEscrowSink` instances are intended to be singletons.</span></span> <span data-ttu-id="cf97c-214">Wenn mehrere `IKeyEscrowSink` Instanzen registriert sind, werden diese während der Schlüsselgenerierung aufgerufen, sodass Schlüssel gleichzeitig an mehrere Mechanismen über gestellt werden können.</span><span class="sxs-lookup"><span data-stu-id="cf97c-214">If multiple `IKeyEscrowSink` instances are registered, each one will be called during key generation, so keys can be escrowed to multiple mechanisms simultaneously.</span></span>

<span data-ttu-id="cf97c-215">Es gibt keine API zum Lesen von Material aus einer `IKeyEscrowSink`-Instanz.</span><span class="sxs-lookup"><span data-stu-id="cf97c-215">There's no API to read material from an `IKeyEscrowSink` instance.</span></span> <span data-ttu-id="cf97c-216">Dies ist konsistent mit der Theorie zum Entwurf des Mechanismus hinterlegter: Dieser Vorgang soll das Schlüsselmaterial zu einer vertrauenswürdigen Stelle zugänglich zu machen, und da die Anwendung selbst keiner vertrauenswürdigen Zertifizierungsstelle ist, er keinen Zugriff haben sollten eigene hinterlegte Material.</span><span class="sxs-lookup"><span data-stu-id="cf97c-216">This is consistent with the design theory of the escrow mechanism: it's intended to make the key material accessible to a trusted authority, and since the application is itself not a trusted authority, it shouldn't have access to its own escrowed material.</span></span>

<span data-ttu-id="cf97c-217">Der folgende Beispielcode veranschaulicht das Erstellen und Registrieren einer `IKeyEscrowSink`, bei der Schlüssel eingebesdert werden, sodass nur Mitglieder von "condesodomain Admins" diese wiederherstellen können.</span><span class="sxs-lookup"><span data-stu-id="cf97c-217">The following sample code demonstrates creating and registering an `IKeyEscrowSink` where keys are escrowed such that only members of "CONTOSODomain Admins" can recover them.</span></span>

> [!NOTE]
> <span data-ttu-id="cf97c-218">Um dieses Beispiel ausführen zu können, muss auf eine Domäne eingebundenen Windows 8 / Windows Server 2012-Computer und dem Domänencontroller muss WindowsServer 2012 oder höher sein.</span><span class="sxs-lookup"><span data-stu-id="cf97c-218">To run this sample, you must be on a domain-joined Windows 8 / Windows Server 2012 machine, and the domain controller must be Windows Server 2012 or later.</span></span>

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
