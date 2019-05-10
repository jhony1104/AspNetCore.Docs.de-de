---
title: Schlüsselverwaltungserweiterbarkeit in ASP.NET Core
author: rick-anderson
description: Informationen Sie zu ASP.NET Core-Datenschutz schlüsselverwaltungserweiterbarkeit.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: 28932cbef1cc797338980f3e0de8b09caee324c0
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896907"
---
# <a name="key-management-extensibility-in-aspnet-core"></a><span data-ttu-id="74882-103">Schlüsselverwaltungserweiterbarkeit in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="74882-103">Key management extensibility in ASP.NET Core</span></span>

> [!TIP]
> <span data-ttu-id="74882-104">Lesen der [schlüsselverwaltung](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) Abschnitt vor dem Lesen der in diesem Abschnitt, wie sie einige der grundlegenden Konzepte hinter dieser APIs erläutert.</span><span class="sxs-lookup"><span data-stu-id="74882-104">Read the [key management](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) section before reading this section, as it explains some of the fundamental concepts behind these APIs.</span></span>

> [!WARNING]
> <span data-ttu-id="74882-105">Typen, die die folgenden Schnittstellen implementieren, sollten threadsicher werden für mehrere Aufrufer.</span><span class="sxs-lookup"><span data-stu-id="74882-105">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="key"></a><span data-ttu-id="74882-106">Key</span><span class="sxs-lookup"><span data-stu-id="74882-106">Key</span></span>

<span data-ttu-id="74882-107">Die `IKey` Schnittstelle ist die grundlegende Darstellung eines Schlüssels in Kryptografiesystem.</span><span class="sxs-lookup"><span data-stu-id="74882-107">The `IKey` interface is the basic representation of a key in cryptosystem.</span></span> <span data-ttu-id="74882-108">Der Begriff-Schlüssel ist hier in der abstrakten Sinne, nicht im literal Sinne "kryptografische Schlüsseldaten" verwendet.</span><span class="sxs-lookup"><span data-stu-id="74882-108">The term key is used here in the abstract sense, not in the literal sense of "cryptographic key material".</span></span> <span data-ttu-id="74882-109">Ein Schlüssel hat die folgenden Eigenschaften:</span><span class="sxs-lookup"><span data-stu-id="74882-109">A key has the following properties:</span></span>

* <span data-ttu-id="74882-110">Aktivierung, Erstellung und Ablaufdaten</span><span class="sxs-lookup"><span data-stu-id="74882-110">Activation, creation, and expiration dates</span></span>

* <span data-ttu-id="74882-111">Status der Zertifikatsperre</span><span class="sxs-lookup"><span data-stu-id="74882-111">Revocation status</span></span>

* <span data-ttu-id="74882-112">Schlüsselbezeichner (eine GUID)</span><span class="sxs-lookup"><span data-stu-id="74882-112">Key identifier (a GUID)</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="74882-113">Darüber hinaus `IKey` macht eine `CreateEncryptor` Methode, die verwendet werden können, erstellen eine [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) Instanz gebunden, der diesem Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="74882-113">Additionally, `IKey` exposes a `CreateEncryptor` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="74882-114">Darüber hinaus `IKey` macht eine `CreateEncryptorInstance` Methode, die verwendet werden können, erstellen eine [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) Instanz gebunden, der diesem Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="74882-114">Additionally, `IKey` exposes a `CreateEncryptorInstance` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="74882-115">Es gibt keine API zum Abrufen der unformatierten kryptografischen Materials aus einem `IKey` Instanz.</span><span class="sxs-lookup"><span data-stu-id="74882-115">There's no API to retrieve the raw cryptographic material from an `IKey` instance.</span></span>

## <a name="ikeymanager"></a><span data-ttu-id="74882-116">IKeyManager</span><span class="sxs-lookup"><span data-stu-id="74882-116">IKeyManager</span></span>

<span data-ttu-id="74882-117">Die `IKeyManager` -Schnittstelle stellt ein Objekt, das für die allgemeine Schlüsselspeicher, abrufen und Manipulation verantwortlich.</span><span class="sxs-lookup"><span data-stu-id="74882-117">The `IKeyManager` interface represents an object responsible for general key storage, retrieval, and manipulation.</span></span> <span data-ttu-id="74882-118">Sie macht drei allgemeine Vorgänge verfügbar:</span><span class="sxs-lookup"><span data-stu-id="74882-118">It exposes three high-level operations:</span></span>

* <span data-ttu-id="74882-119">Erstellen Sie einen neuen Schlüssel, und speichern Sie die Daten in den Speicher.</span><span class="sxs-lookup"><span data-stu-id="74882-119">Create a new key and persist it to storage.</span></span>

* <span data-ttu-id="74882-120">Alle Schlüssel aus dem Speicher abrufen.</span><span class="sxs-lookup"><span data-stu-id="74882-120">Get all keys from storage.</span></span>

* <span data-ttu-id="74882-121">Einen oder mehrere Schlüssel widerrufen, und die Sperrinformationen in den Speicher beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="74882-121">Revoke one or more keys and persist the revocation information to storage.</span></span>

>[!WARNING]
> <span data-ttu-id="74882-122">Schreiben einer `IKeyManager` ist eine sehr komplexe Aufgabe, und die meisten Entwickler sollten nicht versuchen, ihn.</span><span class="sxs-lookup"><span data-stu-id="74882-122">Writing an `IKeyManager` is a very advanced task, and the majority of developers shouldn't attempt it.</span></span> <span data-ttu-id="74882-123">Stattdessen die meisten Entwickler sollten profitieren Sie von den Funktionen von Angeboten die [XmlKeyManager](#xmlkeymanager) Klasse.</span><span class="sxs-lookup"><span data-stu-id="74882-123">Instead, most developers should take advantage of the facilities offered by the [XmlKeyManager](#xmlkeymanager) class.</span></span>

## <a name="xmlkeymanager"></a><span data-ttu-id="74882-124">XmlKeyManager</span><span class="sxs-lookup"><span data-stu-id="74882-124">XmlKeyManager</span></span>

<span data-ttu-id="74882-125">Die `XmlKeyManager` Typ ist die integrierte konkrete Implementierung der `IKeyManager`.</span><span class="sxs-lookup"><span data-stu-id="74882-125">The `XmlKeyManager` type is the in-box concrete implementation of `IKeyManager`.</span></span> <span data-ttu-id="74882-126">Es bietet mehrere nützliche Funktionen, einschließlich schlüsselhinterlegung und Verschlüsselung von Schlüsseln im ruhenden Zustand.</span><span class="sxs-lookup"><span data-stu-id="74882-126">It provides several useful facilities, including key escrow and encryption of keys at rest.</span></span> <span data-ttu-id="74882-127">Schlüssel in diesem System als XML-Elemente dargestellt werden (insbesondere ["XElement"](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span><span class="sxs-lookup"><span data-stu-id="74882-127">Keys in this system are represented as XML elements (specifically, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span></span>

<span data-ttu-id="74882-128">`XmlKeyManager` hängt davon ab, auf mehrere andere Komponenten, die im Verlauf seiner Aufgaben ausführen:</span><span class="sxs-lookup"><span data-stu-id="74882-128">`XmlKeyManager` depends on several other components in the course of fulfilling its tasks:</span></span>

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="74882-129">`AlgorithmConfiguration`, die festlegen, dass der Algorithmen, die durch neue Schlüssel verwendet.</span><span class="sxs-lookup"><span data-stu-id="74882-129">`AlgorithmConfiguration`, which dictates the algorithms used by new keys.</span></span>

* <span data-ttu-id="74882-130">`IXmlRepository`, welche Steuerelemente, wo der Schlüssel im Speicher gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="74882-130">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="74882-131">`IXmlEncryptor` [optional], wodurch Schlüsseln im ruhenden Zustand verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="74882-131">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="74882-132">`IKeyEscrowSink` [optional] die schlüsselhinterlegung Dienste bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="74882-132">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="74882-133">`IXmlRepository`, welche Steuerelemente, wo der Schlüssel im Speicher gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="74882-133">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="74882-134">`IXmlEncryptor` [optional], wodurch Schlüsseln im ruhenden Zustand verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="74882-134">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="74882-135">`IKeyEscrowSink` [optional] die schlüsselhinterlegung Dienste bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="74882-135">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

<span data-ttu-id="74882-136">Im folgenden finden Sie übersichtliche Diagramme, die angeben, wie diese Komponenten innerhalb von miteinander verknüpft sind `XmlKeyManager`.</span><span class="sxs-lookup"><span data-stu-id="74882-136">Below are high-level diagrams which indicate how these components are wired together within `XmlKeyManager`.</span></span>

::: moniker range=">= aspnetcore-2.0"

![Schlüssel erstellen](key-management/_static/keycreation2.png)

<span data-ttu-id="74882-138">*Schlüssel erstellen / CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="74882-138">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="74882-139">In der Implementierung von `CreateNewKey`, `AlgorithmConfiguration` Komponente dient zum Erstellen Sie einen eindeutigen `IAuthenticatedEncryptorDescriptor`, die dann als XML serialisiert wird.</span><span class="sxs-lookup"><span data-stu-id="74882-139">In the implementation of `CreateNewKey`, the `AlgorithmConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="74882-140">Wenn eine Senke schlüsselhinterlegung vorhanden ist, wird der XML-Rohdaten (unverschlüsselt) für die langfristige Speicherung an die Senke bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="74882-140">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="74882-141">Der nicht verschlüsselte XML-Code wird ausgeführt, bis ein `IXmlEncryptor` (falls erforderlich) das verschlüsselte XML-Dokument zu generieren.</span><span class="sxs-lookup"><span data-stu-id="74882-141">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="74882-142">Dieses verschlüsselte Dokument werden beibehalten, in den langfristigen Speicher über die `IXmlRepository`.</span><span class="sxs-lookup"><span data-stu-id="74882-142">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="74882-143">(Wenn kein `IXmlEncryptor` wird konfiguriert, wird das Dokument nicht verschlüsselte in beibehalten der `IXmlRepository`.)</span><span class="sxs-lookup"><span data-stu-id="74882-143">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![Abrufen eines Schlüssels](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![Schlüssel erstellen](key-management/_static/keycreation1.png)

<span data-ttu-id="74882-146">*Schlüssel erstellen / CreateNewKey*</span><span class="sxs-lookup"><span data-stu-id="74882-146">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="74882-147">In der Implementierung von `CreateNewKey`, `IAuthenticatedEncryptorConfiguration` Komponente dient zum Erstellen Sie einen eindeutigen `IAuthenticatedEncryptorDescriptor`, die dann als XML serialisiert wird.</span><span class="sxs-lookup"><span data-stu-id="74882-147">In the implementation of `CreateNewKey`, the `IAuthenticatedEncryptorConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="74882-148">Wenn eine Senke schlüsselhinterlegung vorhanden ist, wird der XML-Rohdaten (unverschlüsselt) für die langfristige Speicherung an die Senke bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="74882-148">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="74882-149">Der nicht verschlüsselte XML-Code wird ausgeführt, bis ein `IXmlEncryptor` (falls erforderlich) das verschlüsselte XML-Dokument zu generieren.</span><span class="sxs-lookup"><span data-stu-id="74882-149">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="74882-150">Dieses verschlüsselte Dokument werden beibehalten, in den langfristigen Speicher über die `IXmlRepository`.</span><span class="sxs-lookup"><span data-stu-id="74882-150">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="74882-151">(Wenn kein `IXmlEncryptor` wird konfiguriert, wird das Dokument nicht verschlüsselte in beibehalten der `IXmlRepository`.)</span><span class="sxs-lookup"><span data-stu-id="74882-151">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![Abrufen eines Schlüssels](key-management/_static/keyretrieval1.png)

::: moniker-end

<span data-ttu-id="74882-153">*Abrufen von Schlüssel / GetAllKeys*</span><span class="sxs-lookup"><span data-stu-id="74882-153">*Key Retrieval / GetAllKeys*</span></span>

<span data-ttu-id="74882-154">In der Implementierung von `GetAllKeys`, die XML-Dokumente darstellen, Schlüssel und Widerrufe werden gelesen, aus der zugrunde liegenden `IXmlRepository`.</span><span class="sxs-lookup"><span data-stu-id="74882-154">In the implementation of `GetAllKeys`, the XML documents representing keys and revocations are read from the underlying `IXmlRepository`.</span></span> <span data-ttu-id="74882-155">Wenn diese Dokumente verschlüsselt sind, wird das System diese automatisch entschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="74882-155">If these documents are encrypted, the system will automatically decrypt them.</span></span> <span data-ttu-id="74882-156">`XmlKeyManager` erstellt den entsprechenden `IAuthenticatedEncryptorDescriptorDeserializer` Instanzen, die Dokumente zu deserialisieren, wieder in das `IAuthenticatedEncryptorDescriptor` -Instanzen, die dann in einzelnen umschlossen werden `IKey` Instanzen.</span><span class="sxs-lookup"><span data-stu-id="74882-156">`XmlKeyManager` creates the appropriate `IAuthenticatedEncryptorDescriptorDeserializer` instances to deserialize the documents back into `IAuthenticatedEncryptorDescriptor` instances, which are then wrapped in individual `IKey` instances.</span></span> <span data-ttu-id="74882-157">Diese Sammlung von `IKey` Instanzen an den Aufrufer zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="74882-157">This collection of `IKey` instances is returned to the caller.</span></span>

<span data-ttu-id="74882-158">Weitere Informationen zu bestimmten XML-Elemente befinden sich die [Schlüsselspeicher Format Dokument](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span><span class="sxs-lookup"><span data-stu-id="74882-158">Further information on the particular XML elements can be found in the [key storage format document](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span></span>

## <a name="ixmlrepository"></a><span data-ttu-id="74882-159">IXmlRepository</span><span class="sxs-lookup"><span data-stu-id="74882-159">IXmlRepository</span></span>

<span data-ttu-id="74882-160">Die `IXmlRepository` Schnittstelle darstellt, einen Typ, der XML-Code beibehalten und Abrufen von XML aus einem Sicherungsspeicher kann.</span><span class="sxs-lookup"><span data-stu-id="74882-160">The `IXmlRepository` interface represents a type that can persist XML to and retrieve XML from a backing store.</span></span> <span data-ttu-id="74882-161">Es macht zwei APIs verfügbar:</span><span class="sxs-lookup"><span data-stu-id="74882-161">It exposes two APIs:</span></span>

* <span data-ttu-id="74882-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span><span class="sxs-lookup"><span data-stu-id="74882-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span></span>

* `StoreElement(XElement element, string friendlyName)`

<span data-ttu-id="74882-163">Implementierungen von `IXmlRepository` keine zum Analysieren des XML-Code übergeben werden müssen.</span><span class="sxs-lookup"><span data-stu-id="74882-163">Implementations of `IXmlRepository` don't need to parse the XML passing through them.</span></span> <span data-ttu-id="74882-164">Sie sollten die XML-Dokumente als nicht transparent behandeln, die höhere Schichten kümmern, generieren und die Dokumente verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="74882-164">They should treat the XML documents as opaque and let higher layers worry about generating and parsing the documents.</span></span>

<span data-ttu-id="74882-165">Es gibt vier integrierte konkrete Typen die implementieren `IXmlRepository`:</span><span class="sxs-lookup"><span data-stu-id="74882-165">There are four built-in concrete types which implement `IXmlRepository`:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="74882-166">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="74882-166">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="74882-167">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="74882-167">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="74882-168">AzureStorage.AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="74882-168">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="74882-169">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="74882-169">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="74882-170">FileSystemXmlRepository</span><span class="sxs-lookup"><span data-stu-id="74882-170">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="74882-171">RegistryXmlRepository</span><span class="sxs-lookup"><span data-stu-id="74882-171">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="74882-172">AzureStorage.AzureBlobXmlRepository</span><span class="sxs-lookup"><span data-stu-id="74882-172">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="74882-173">RedisXmlRepository</span><span class="sxs-lookup"><span data-stu-id="74882-173">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

<span data-ttu-id="74882-174">Finden Sie unter den [softwareschlüsselspeicher-Anbieter Dokument](xref:security/data-protection/implementation/key-storage-providers) für Weitere Informationen.</span><span class="sxs-lookup"><span data-stu-id="74882-174">See the [key storage providers document](xref:security/data-protection/implementation/key-storage-providers) for more information.</span></span>

<span data-ttu-id="74882-175">Registrieren ein benutzerdefinierten `IXmlRepository` ist geeignet, wenn Sie einen einzelne Sicherungsspeicher (z. B. Azure Table Storage) verwenden.</span><span class="sxs-lookup"><span data-stu-id="74882-175">Registering a custom `IXmlRepository` is appropriate when using a different backing store (for example, Azure Table Storage).</span></span>

<span data-ttu-id="74882-176">Um das standardrepository anwendungsweite ändern möchten, registrieren Sie eine benutzerdefinierte `IXmlRepository` Instanz:</span><span class="sxs-lookup"><span data-stu-id="74882-176">To change the default repository application-wide, register a custom `IXmlRepository` instance:</span></span>

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

## <a name="ixmlencryptor"></a><span data-ttu-id="74882-177">IXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="74882-177">IXmlEncryptor</span></span>

<span data-ttu-id="74882-178">Die `IXmlEncryptor` Schnittstelle darstellt, einen Typ, der einem nur-Text-XML-Element verschlüsseln kann.</span><span class="sxs-lookup"><span data-stu-id="74882-178">The `IXmlEncryptor` interface represents a type that can encrypt a plaintext XML element.</span></span> <span data-ttu-id="74882-179">Sie macht eine einzelne API verfügbar:</span><span class="sxs-lookup"><span data-stu-id="74882-179">It exposes a single API:</span></span>

* <span data-ttu-id="74882-180">Encrypt(XElement plaintextElement): EncryptedXmlInfo</span><span class="sxs-lookup"><span data-stu-id="74882-180">Encrypt(XElement plaintextElement) : EncryptedXmlInfo</span></span>

<span data-ttu-id="74882-181">Wenn eine serialisierte `IAuthenticatedEncryptorDescriptor` enthält alle Elemente, die als "erfordert eine Verschlüsselung", klicken Sie dann `XmlKeyManager` führt diese Elemente über dem konfigurierten `IXmlEncryptor`des `Encrypt` -Methode, und es bleiben dazu Elements anstelle der Nur-Text-Element, um die `IXmlRepository`.</span><span class="sxs-lookup"><span data-stu-id="74882-181">If a serialized `IAuthenticatedEncryptorDescriptor` contains any elements marked as "requires encryption", then `XmlKeyManager` will run those elements through the configured `IXmlEncryptor`'s `Encrypt` method, and it will persist the enciphered element rather than the plaintext element to the `IXmlRepository`.</span></span> <span data-ttu-id="74882-182">Die Ausgabe der `Encrypt` Methode ist ein `EncryptedXmlInfo` Objekt.</span><span class="sxs-lookup"><span data-stu-id="74882-182">The output of the `Encrypt` method is an `EncryptedXmlInfo` object.</span></span> <span data-ttu-id="74882-183">Dieses Objekt ist ein Wrapper enthält sowohl die resultierenden dazu `XElement` und den Typ steht für ein `IXmlDecryptor` die verwendet werden können, um das entsprechende Element zu entschlüsseln.</span><span class="sxs-lookup"><span data-stu-id="74882-183">This object is a wrapper which contains both the resultant enciphered `XElement` and the Type which represents an `IXmlDecryptor` which can be used to decipher the corresponding element.</span></span>

<span data-ttu-id="74882-184">Es gibt vier integrierte konkrete Typen die implementieren `IXmlEncryptor`:</span><span class="sxs-lookup"><span data-stu-id="74882-184">There are four built-in concrete types which implement `IXmlEncryptor`:</span></span>

* [<span data-ttu-id="74882-185">CertificateXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="74882-185">CertificateXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [<span data-ttu-id="74882-186">DpapiNGXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="74882-186">DpapiNGXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [<span data-ttu-id="74882-187">DpapiXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="74882-187">DpapiXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [<span data-ttu-id="74882-188">NullXmlEncryptor</span><span class="sxs-lookup"><span data-stu-id="74882-188">NullXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

<span data-ttu-id="74882-189">Finden Sie unter den [Schlüsselverschlüsselung auf Rest-Dokument](xref:security/data-protection/implementation/key-encryption-at-rest) für Weitere Informationen.</span><span class="sxs-lookup"><span data-stu-id="74882-189">See the [key encryption at rest document](xref:security/data-protection/implementation/key-encryption-at-rest) for more information.</span></span>

<span data-ttu-id="74882-190">Um den Key-Verschlüsselung im Ruhezustand Standardmechanismus anwendungsweite ändern möchten, registrieren Sie eine benutzerdefinierte `IXmlEncryptor` Instanz:</span><span class="sxs-lookup"><span data-stu-id="74882-190">To change the default key-encryption-at-rest mechanism application-wide, register a custom `IXmlEncryptor` instance:</span></span>

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

## <a name="ixmldecryptor"></a><span data-ttu-id="74882-191">IXmlDecryptor</span><span class="sxs-lookup"><span data-stu-id="74882-191">IXmlDecryptor</span></span>

<span data-ttu-id="74882-192">Die `IXmlDecryptor` Schnittstelle darstellt, einen Typ, der weiß, wie zum Entschlüsseln einer `XElement` , wurde dazu über einen `IXmlEncryptor`.</span><span class="sxs-lookup"><span data-stu-id="74882-192">The `IXmlDecryptor` interface represents a type that knows how to decrypt an `XElement` that was enciphered via an `IXmlEncryptor`.</span></span> <span data-ttu-id="74882-193">Sie macht eine einzelne API verfügbar:</span><span class="sxs-lookup"><span data-stu-id="74882-193">It exposes a single API:</span></span>

* <span data-ttu-id="74882-194">Entschlüsseln Sie ("XElement" EncryptedElement): "XElement"</span><span class="sxs-lookup"><span data-stu-id="74882-194">Decrypt(XElement encryptedElement) : XElement</span></span>

<span data-ttu-id="74882-195">Die `Decrypt` Methode macht die Verschlüsselung von `IXmlEncryptor.Encrypt`.</span><span class="sxs-lookup"><span data-stu-id="74882-195">The `Decrypt` method undoes the encryption performed by `IXmlEncryptor.Encrypt`.</span></span> <span data-ttu-id="74882-196">Im Allgemeinen jede konkrete `IXmlEncryptor` Implementierung weist einen entsprechenden konkreten `IXmlDecryptor` Implementierung.</span><span class="sxs-lookup"><span data-stu-id="74882-196">Generally, each concrete `IXmlEncryptor` implementation will have a corresponding concrete `IXmlDecryptor` implementation.</span></span>

<span data-ttu-id="74882-197">Typen der implementieren `IXmlDecryptor` sollte einen der folgenden zwei öffentliche Konstruktoren aufweisen:</span><span class="sxs-lookup"><span data-stu-id="74882-197">Types which implement `IXmlDecryptor` should have one of the following two public constructors:</span></span>

* <span data-ttu-id="74882-198">.ctor(IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="74882-198">.ctor(IServiceProvider)</span></span>
* <span data-ttu-id="74882-199">.ctor()</span><span class="sxs-lookup"><span data-stu-id="74882-199">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="74882-200">Die `IServiceProvider` , die an der Konstruktor kann null sein.</span><span class="sxs-lookup"><span data-stu-id="74882-200">The `IServiceProvider` passed to the constructor may be null.</span></span>

## <a name="ikeyescrowsink"></a><span data-ttu-id="74882-201">IKeyEscrowSink</span><span class="sxs-lookup"><span data-stu-id="74882-201">IKeyEscrowSink</span></span>

<span data-ttu-id="74882-202">Die `IKeyEscrowSink` Schnittstelle darstellt, einen Typ, der hinterlegter vertraulicher Informationen führen kann.</span><span class="sxs-lookup"><span data-stu-id="74882-202">The `IKeyEscrowSink` interface represents a type that can perform escrow of sensitive information.</span></span> <span data-ttu-id="74882-203">Denken Sie daran, dass serialisierte Deskriptoren möglicherweise vertrauliche Informationen (z. B. kryptografischem Material enthalten), und dies ist wie der Einführung von geführt hat die [IXmlEncryptor](#ixmlencryptor) im vornherein geben.</span><span class="sxs-lookup"><span data-stu-id="74882-203">Recall that serialized descriptors might contain sensitive information (such as cryptographic material), and this is what led to the introduction of the [IXmlEncryptor](#ixmlencryptor) type in the first place.</span></span> <span data-ttu-id="74882-204">Allerdings Unfälle passieren, und Schlüssel Ringe können gelöscht werden, oder beschädigt.</span><span class="sxs-lookup"><span data-stu-id="74882-204">However, accidents happen, and key rings can be deleted or become corrupted.</span></span>

<span data-ttu-id="74882-205">Die hinterlegter-Schnittstelle bietet ein Notfall escapehatch, für die Gewährung des Zugriffs auf die serialisierte XML-Rohdaten, bevor sie durch alle konfigurierten transformiert wird [IXmlEncryptor](#ixmlencryptor).</span><span class="sxs-lookup"><span data-stu-id="74882-205">The escrow interface provides an emergency escape hatch, allowing access to the raw serialized XML before it's transformed by any configured [IXmlEncryptor](#ixmlencryptor).</span></span> <span data-ttu-id="74882-206">Die Schnittstelle macht eine einzelne API verfügbar:</span><span class="sxs-lookup"><span data-stu-id="74882-206">The interface exposes a single API:</span></span>

* <span data-ttu-id="74882-207">Store (Guid-Schlüssel-ID, "XElement"-Element)</span><span class="sxs-lookup"><span data-stu-id="74882-207">Store(Guid keyId, XElement element)</span></span>

<span data-ttu-id="74882-208">Es liegt die `IKeyEscrowSink` Implementierung, die das angegebene Element auf sichere Weise konsistent mit der Business-Richtlinie zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="74882-208">It's up to the `IKeyEscrowSink` implementation to handle the provided element in a secure manner consistent with business policy.</span></span> <span data-ttu-id="74882-209">Eine mögliche Implementierung könnte für die Senke hinterlegter zum Verschlüsseln von XML-Elements mit einem bekannten Unternehmen x. 509-Zertifikat sein, in dem der private Zertifikatschlüssel hinterlegt ist. die `CertificateXmlEncryptor` Typ dabei helfen kann.</span><span class="sxs-lookup"><span data-stu-id="74882-209">One possible implementation could be for the escrow sink to encrypt the XML element using a known corporate X.509 certificate where the certificate's private key has been escrowed; the `CertificateXmlEncryptor` type can assist with this.</span></span> <span data-ttu-id="74882-210">Die `IKeyEscrowSink` Implementierung ist auch zuständig für das bereitgestellte Element entsprechend beibehalten.</span><span class="sxs-lookup"><span data-stu-id="74882-210">The `IKeyEscrowSink` implementation is also responsible for persisting the provided element appropriately.</span></span>

<span data-ttu-id="74882-211">Keinen hinterlegter-Mechanismus ist standardmäßig aktiviert, wenn der Server-Administratoren können [Global konfigurieren](xref:security/data-protection/configuration/machine-wide-policy).</span><span class="sxs-lookup"><span data-stu-id="74882-211">By default no escrow mechanism is enabled, though server administrators can [configure this globally](xref:security/data-protection/configuration/machine-wide-policy).</span></span> <span data-ttu-id="74882-212">Sie können auch programmgesteuert programmiert werden über die `IDataProtectionBuilder.AddKeyEscrowSink` -Methode wie im folgenden Beispiel gezeigt.</span><span class="sxs-lookup"><span data-stu-id="74882-212">It can also be configured programmatically via the `IDataProtectionBuilder.AddKeyEscrowSink` method as shown in the sample below.</span></span> <span data-ttu-id="74882-213">Die `AddKeyEscrowSink` Methode Überladungen Spiegel der `IServiceCollection.AddSingleton` und `IServiceCollection.AddInstance` Überladungen als `IKeyEscrowSink` Instanzen Singletons werden sollen.</span><span class="sxs-lookup"><span data-stu-id="74882-213">The `AddKeyEscrowSink` method overloads mirror the `IServiceCollection.AddSingleton` and `IServiceCollection.AddInstance` overloads, as `IKeyEscrowSink` instances are intended to be singletons.</span></span> <span data-ttu-id="74882-214">Wenn mehrere `IKeyEscrowSink` Instanzen registriert sind, jeweils wird während der Generierung von Schlüsseln, aufgerufen werden, damit der Schlüssel gleichzeitig an mehrere Mechanismen hinterlegt werden können.</span><span class="sxs-lookup"><span data-stu-id="74882-214">If multiple `IKeyEscrowSink` instances are registered, each one will be called during key generation, so keys can be escrowed to multiple mechanisms simultaneously.</span></span>

<span data-ttu-id="74882-215">Es gibt keine API zum Lesen von Material aus einem `IKeyEscrowSink` Instanz.</span><span class="sxs-lookup"><span data-stu-id="74882-215">There's no API to read material from an `IKeyEscrowSink` instance.</span></span> <span data-ttu-id="74882-216">Dies ist konsistent mit der Theorie zum Entwurf des Mechanismus hinterlegter: Dieser Vorgang soll das Schlüsselmaterial zu einer vertrauenswürdigen Stelle zugänglich zu machen, und da die Anwendung selbst keiner vertrauenswürdigen Zertifizierungsstelle ist, er keinen Zugriff haben sollten eigene hinterlegte Material.</span><span class="sxs-lookup"><span data-stu-id="74882-216">This is consistent with the design theory of the escrow mechanism: it's intended to make the key material accessible to a trusted authority, and since the application is itself not a trusted authority, it shouldn't have access to its own escrowed material.</span></span>

<span data-ttu-id="74882-217">Der folgende Beispielcode veranschaulicht das Erstellen und Registrieren einer `IKeyEscrowSink` , in denen Schlüssel hinterlegt werden, dass nur Mitglieder der "CONTOSODomain-Admins" wiederhergestellt werden können.</span><span class="sxs-lookup"><span data-stu-id="74882-217">The following sample code demonstrates creating and registering an `IKeyEscrowSink` where keys are escrowed such that only members of "CONTOSODomain Admins" can recover them.</span></span>

> [!NOTE]
> <span data-ttu-id="74882-218">Um dieses Beispiel ausführen zu können, muss auf eine Domäne eingebundenen Windows 8 / Windows Server 2012-Computer und dem Domänencontroller muss WindowsServer 2012 oder höher sein.</span><span class="sxs-lookup"><span data-stu-id="74882-218">To run this sample, you must be on a domain-joined Windows 8 / Windows Server 2012 machine, and the domain controller must be Windows Server 2012 or later.</span></span>

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
