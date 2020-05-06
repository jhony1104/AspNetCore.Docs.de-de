---
title: Erweiterbarkeit der Schlüsselverwaltung in ASP.net Core
author: rick-anderson
description: Erfahren Sie mehr über die Erweiterbarkeit von ASP.net Core Datenschutz Schlüsselverwaltung.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/24/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/extensibility/key-management
ms.openlocfilehash: f8af699344473510c5579c2f0e4d2920ada013f1
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775725"
---
# <a name="key-management-extensibility-in-aspnet-core"></a><span data-ttu-id="43be1-103">Erweiterbarkeit der Schlüsselverwaltung in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="43be1-103">Key management extensibility in ASP.NET Core</span></span>

> [!TIP]
> <span data-ttu-id="43be1-104">Lesen Sie den Abschnitt [Key Management](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) vor dem Lesen dieses Abschnitts, da hier einige der grundlegenden Konzepte hinter diesen APIs erläutert werden.</span><span class="sxs-lookup"><span data-stu-id="43be1-104">Read the [key management](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) section before reading this section, as it explains some of the fundamental concepts behind these APIs.</span></span>

> [!WARNING]
> <span data-ttu-id="43be1-105">Typen, die eine der folgenden Schnittstellen implementieren, sollten für mehrere Aufrufer Thread sicher sein.</span><span class="sxs-lookup"><span data-stu-id="43be1-105">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

## <a name="key"></a><span data-ttu-id="43be1-106">Schlüssel</span><span class="sxs-lookup"><span data-stu-id="43be1-106">Key</span></span>

<span data-ttu-id="43be1-107">Die `IKey` -Schnittstelle ist die grundlegende Darstellung eines Schlüssels in Cryptosystem.</span><span class="sxs-lookup"><span data-stu-id="43be1-107">The `IKey` interface is the basic representation of a key in cryptosystem.</span></span> <span data-ttu-id="43be1-108">Der Begriff Key wird hier im abstrakten Sinn verwendet, nicht im literalsinn von "kryptografieschlüsselmaterial".</span><span class="sxs-lookup"><span data-stu-id="43be1-108">The term key is used here in the abstract sense, not in the literal sense of "cryptographic key material".</span></span> <span data-ttu-id="43be1-109">Ein Schlüssel weist die folgenden Eigenschaften auf:</span><span class="sxs-lookup"><span data-stu-id="43be1-109">A key has the following properties:</span></span>

* <span data-ttu-id="43be1-110">Aktivierungs-, Erstellungs-und Ablaufdatum</span><span class="sxs-lookup"><span data-stu-id="43be1-110">Activation, creation, and expiration dates</span></span>

* <span data-ttu-id="43be1-111">Sperrstatus</span><span class="sxs-lookup"><span data-stu-id="43be1-111">Revocation status</span></span>

* <span data-ttu-id="43be1-112">Schlüssel Bezeichner (GUID)</span><span class="sxs-lookup"><span data-stu-id="43be1-112">Key identifier (a GUID)</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="43be1-113">Darüber hinaus `IKey` macht eine `CreateEncryptor` -Methode verfügbar, die zum Erstellen einer [iauthenticatedencryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) -Instanz verwendet werden kann, die mit diesem Schlüssel verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="43be1-113">Additionally, `IKey` exposes a `CreateEncryptor` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="43be1-114">Darüber hinaus `IKey` macht eine `CreateEncryptorInstance` -Methode verfügbar, die zum Erstellen einer [iauthenticatedencryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) -Instanz verwendet werden kann, die mit diesem Schlüssel verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="43be1-114">Additionally, `IKey` exposes a `CreateEncryptorInstance` method which can be used to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance tied to this key.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="43be1-115">Es gibt keine API, um das unformatierte kryptografiematerial aus einer `IKey` -Instanz abzurufen.</span><span class="sxs-lookup"><span data-stu-id="43be1-115">There's no API to retrieve the raw cryptographic material from an `IKey` instance.</span></span>

## <a name="ikeymanager"></a><span data-ttu-id="43be1-116">Ikeymanager</span><span class="sxs-lookup"><span data-stu-id="43be1-116">IKeyManager</span></span>

<span data-ttu-id="43be1-117">Die `IKeyManager` -Schnittstelle stellt ein Objekt dar, das für die Speicherung, den Abruf und die Bearbeitung allgemeiner Schlüssel zuständig ist</span><span class="sxs-lookup"><span data-stu-id="43be1-117">The `IKeyManager` interface represents an object responsible for general key storage, retrieval, and manipulation.</span></span> <span data-ttu-id="43be1-118">Es macht drei Vorgänge auf hoher Ebene verfügbar:</span><span class="sxs-lookup"><span data-stu-id="43be1-118">It exposes three high-level operations:</span></span>

* <span data-ttu-id="43be1-119">Erstellen Sie einen neuen Schlüssel, und speichern Sie ihn im Speicher.</span><span class="sxs-lookup"><span data-stu-id="43be1-119">Create a new key and persist it to storage.</span></span>

* <span data-ttu-id="43be1-120">Alle Schlüssel aus dem Speicher erhalten.</span><span class="sxs-lookup"><span data-stu-id="43be1-120">Get all keys from storage.</span></span>

* <span data-ttu-id="43be1-121">Widerrufen Sie mindestens einen Schlüssel, und speichern Sie die Sperrinformationen im Speicher.</span><span class="sxs-lookup"><span data-stu-id="43be1-121">Revoke one or more keys and persist the revocation information to storage.</span></span>

>[!WARNING]
> <span data-ttu-id="43be1-122">Das Schreiben `IKeyManager` eines ist eine sehr fortschrittliche Aufgabe, und die Mehrzahl der Entwickler sollten es nicht versuchen.</span><span class="sxs-lookup"><span data-stu-id="43be1-122">Writing an `IKeyManager` is a very advanced task, and the majority of developers shouldn't attempt it.</span></span> <span data-ttu-id="43be1-123">Stattdessen sollten die meisten Entwickler die Funktionen nutzen, die von der [xmlkeymanager](#xmlkeymanager) -Klasse angeboten werden.</span><span class="sxs-lookup"><span data-stu-id="43be1-123">Instead, most developers should take advantage of the facilities offered by the [XmlKeyManager](#xmlkeymanager) class.</span></span>

## <a name="xmlkeymanager"></a><span data-ttu-id="43be1-124">Xmlkeymanager</span><span class="sxs-lookup"><span data-stu-id="43be1-124">XmlKeyManager</span></span>

<span data-ttu-id="43be1-125">Der `XmlKeyManager` -Typ ist die in-Box konkrete Implementierung `IKeyManager`von.</span><span class="sxs-lookup"><span data-stu-id="43be1-125">The `XmlKeyManager` type is the in-box concrete implementation of `IKeyManager`.</span></span> <span data-ttu-id="43be1-126">Es bietet mehrere nützliche Funktionen, einschließlich Schlüssel Hinterlegung und Verschlüsselung von Schlüsseln im Ruhezustand.</span><span class="sxs-lookup"><span data-stu-id="43be1-126">It provides several useful facilities, including key escrow and encryption of keys at rest.</span></span> <span data-ttu-id="43be1-127">Schlüssel in diesem System werden als XML-Elemente (insbesondere [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)) dargestellt.</span><span class="sxs-lookup"><span data-stu-id="43be1-127">Keys in this system are represented as XML elements (specifically, [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).</span></span>

<span data-ttu-id="43be1-128">`XmlKeyManager`hängt von verschiedenen anderen Komponenten im Rahmen der Erfüllung der Aufgaben ab:</span><span class="sxs-lookup"><span data-stu-id="43be1-128">`XmlKeyManager` depends on several other components in the course of fulfilling its tasks:</span></span>

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="43be1-129">`AlgorithmConfiguration`, mit dem die von neuen Schlüsseln verwendeten Algorithmen bestimmt werden.</span><span class="sxs-lookup"><span data-stu-id="43be1-129">`AlgorithmConfiguration`, which dictates the algorithms used by new keys.</span></span>

* <span data-ttu-id="43be1-130">`IXmlRepository`, der steuert, wo Schlüssel im Speicher gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="43be1-130">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="43be1-131">`IXmlEncryptor`[optional], mit dem Schlüssel im Ruhezustand verschlüsselt werden können.</span><span class="sxs-lookup"><span data-stu-id="43be1-131">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="43be1-132">`IKeyEscrowSink`[optional], die Schlüssel Hinterlegungs Dienste bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="43be1-132">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="43be1-133">`IXmlRepository`, der steuert, wo Schlüssel im Speicher gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="43be1-133">`IXmlRepository`, which controls where keys are persisted in storage.</span></span>

* <span data-ttu-id="43be1-134">`IXmlEncryptor`[optional], mit dem Schlüssel im Ruhezustand verschlüsselt werden können.</span><span class="sxs-lookup"><span data-stu-id="43be1-134">`IXmlEncryptor` [optional], which allows encrypting keys at rest.</span></span>

* <span data-ttu-id="43be1-135">`IKeyEscrowSink`[optional], die Schlüssel Hinterlegungs Dienste bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="43be1-135">`IKeyEscrowSink` [optional], which provides key escrow services.</span></span>

::: moniker-end

<span data-ttu-id="43be1-136">Im folgenden finden Sie Diagramme auf hoher Ebene, die angeben, wie diese Komponenten `XmlKeyManager`in miteinander verbunden sind.</span><span class="sxs-lookup"><span data-stu-id="43be1-136">Below are high-level diagrams which indicate how these components are wired together within `XmlKeyManager`.</span></span>

::: moniker range=">= aspnetcore-2.0"

![Schlüssel Erstellung](key-management/_static/keycreation2.png)

<span data-ttu-id="43be1-138">*Key Creation/kreatenewkey*</span><span class="sxs-lookup"><span data-stu-id="43be1-138">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="43be1-139">In der Implementierung von `CreateNewKey`wird die `AlgorithmConfiguration` -Komponente verwendet, um einen eindeutigen `IAuthenticatedEncryptorDescriptor`zu erstellen, der dann als XML serialisiert wird.</span><span class="sxs-lookup"><span data-stu-id="43be1-139">In the implementation of `CreateNewKey`, the `AlgorithmConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="43be1-140">Wenn eine Schlüssel Hinterlegungs Senke vorhanden ist, wird die unverschlüsselte (unverschlüsselte) XML-Daten für die langfristige Speicherung an die Senke bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="43be1-140">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="43be1-141">Der unverschlüsselte XML-Code wird dann `IXmlEncryptor` (falls erforderlich) ausgeführt, um das verschlüsselte XML-Dokument zu generieren.</span><span class="sxs-lookup"><span data-stu-id="43be1-141">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="43be1-142">Dieses verschlüsselte Dokument wird in der langfristigen Speicherung über den `IXmlRepository`persistent gespeichert.</span><span class="sxs-lookup"><span data-stu-id="43be1-142">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="43be1-143">(Wenn kein `IXmlEncryptor` konfiguriert ist, wird das unverschlüsselte Dokument in gespeichert `IXmlRepository`.)</span><span class="sxs-lookup"><span data-stu-id="43be1-143">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![Schlüssel Abruf](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![Schlüssel Erstellung](key-management/_static/keycreation1.png)

<span data-ttu-id="43be1-146">*Key Creation/kreatenewkey*</span><span class="sxs-lookup"><span data-stu-id="43be1-146">*Key Creation / CreateNewKey*</span></span>

<span data-ttu-id="43be1-147">In der Implementierung von `CreateNewKey`wird die `IAuthenticatedEncryptorConfiguration` -Komponente verwendet, um einen eindeutigen `IAuthenticatedEncryptorDescriptor`zu erstellen, der dann als XML serialisiert wird.</span><span class="sxs-lookup"><span data-stu-id="43be1-147">In the implementation of `CreateNewKey`, the `IAuthenticatedEncryptorConfiguration` component is used to create a unique `IAuthenticatedEncryptorDescriptor`, which is then serialized as XML.</span></span> <span data-ttu-id="43be1-148">Wenn eine Schlüssel Hinterlegungs Senke vorhanden ist, wird die unverschlüsselte (unverschlüsselte) XML-Daten für die langfristige Speicherung an die Senke bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="43be1-148">If a key escrow sink is present, the raw (unencrypted) XML is provided to the sink for long-term storage.</span></span> <span data-ttu-id="43be1-149">Der unverschlüsselte XML-Code wird dann `IXmlEncryptor` (falls erforderlich) ausgeführt, um das verschlüsselte XML-Dokument zu generieren.</span><span class="sxs-lookup"><span data-stu-id="43be1-149">The unencrypted XML is then run through an `IXmlEncryptor` (if required) to generate the encrypted XML document.</span></span> <span data-ttu-id="43be1-150">Dieses verschlüsselte Dokument wird in der langfristigen Speicherung über den `IXmlRepository`persistent gespeichert.</span><span class="sxs-lookup"><span data-stu-id="43be1-150">This encrypted document is persisted to long-term storage via the `IXmlRepository`.</span></span> <span data-ttu-id="43be1-151">(Wenn kein `IXmlEncryptor` konfiguriert ist, wird das unverschlüsselte Dokument in gespeichert `IXmlRepository`.)</span><span class="sxs-lookup"><span data-stu-id="43be1-151">(If no `IXmlEncryptor` is configured, the unencrypted document is persisted in the `IXmlRepository`.)</span></span>

![Schlüssel Abruf](key-management/_static/keyretrieval1.png)

::: moniker-end

<span data-ttu-id="43be1-153">*Schlüssel Abruf/getallkeys*</span><span class="sxs-lookup"><span data-stu-id="43be1-153">*Key Retrieval / GetAllKeys*</span></span>

<span data-ttu-id="43be1-154">In der Implementierung von `GetAllKeys`werden die XML-Dokumente, die Schlüssel und widerrufen darstellen, aus `IXmlRepository`der zugrunde liegenden gelesen.</span><span class="sxs-lookup"><span data-stu-id="43be1-154">In the implementation of `GetAllKeys`, the XML documents representing keys and revocations are read from the underlying `IXmlRepository`.</span></span> <span data-ttu-id="43be1-155">Wenn diese Dokumente verschlüsselt sind, werden Sie vom System automatisch entschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="43be1-155">If these documents are encrypted, the system will automatically decrypt them.</span></span> <span data-ttu-id="43be1-156">`XmlKeyManager`erstellt die entsprechenden `IAuthenticatedEncryptorDescriptorDeserializer` -Instanzen, um die Dokumente wieder in Instanzen `IAuthenticatedEncryptorDescriptor` zu deserialisieren, die anschließend in `IKey` einzelne Instanzen umschließt werden.</span><span class="sxs-lookup"><span data-stu-id="43be1-156">`XmlKeyManager` creates the appropriate `IAuthenticatedEncryptorDescriptorDeserializer` instances to deserialize the documents back into `IAuthenticatedEncryptorDescriptor` instances, which are then wrapped in individual `IKey` instances.</span></span> <span data-ttu-id="43be1-157">Diese Auflistung von `IKey` -Instanzen wird an den Aufrufer zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="43be1-157">This collection of `IKey` instances is returned to the caller.</span></span>

<span data-ttu-id="43be1-158">Weitere Informationen zu den einzelnen XML-Elementen finden Sie im [Dokument Schlüsselspeicher Format](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span><span class="sxs-lookup"><span data-stu-id="43be1-158">Further information on the particular XML elements can be found in the [key storage format document](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).</span></span>

## <a name="ixmlrepository"></a><span data-ttu-id="43be1-159">Ixmlrepository</span><span class="sxs-lookup"><span data-stu-id="43be1-159">IXmlRepository</span></span>

<span data-ttu-id="43be1-160">Die `IXmlRepository` -Schnittstelle stellt einen Typ dar, der XML in einem Sicherungs Speicher beibehalten und XML abrufen kann.</span><span class="sxs-lookup"><span data-stu-id="43be1-160">The `IXmlRepository` interface represents a type that can persist XML to and retrieve XML from a backing store.</span></span> <span data-ttu-id="43be1-161">Es macht zwei APIs verfügbar:</span><span class="sxs-lookup"><span data-stu-id="43be1-161">It exposes two APIs:</span></span>

* <span data-ttu-id="43be1-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span><span class="sxs-lookup"><span data-stu-id="43be1-162">`GetAllElements` :`IReadOnlyCollection<XElement>`</span></span>

* `StoreElement(XElement element, string friendlyName)`

<span data-ttu-id="43be1-163">Implementierungen von `IXmlRepository` müssen den XML-Code nicht analysieren.</span><span class="sxs-lookup"><span data-stu-id="43be1-163">Implementations of `IXmlRepository` don't need to parse the XML passing through them.</span></span> <span data-ttu-id="43be1-164">Sie sollten die XML-Dokumente als nicht transparent behandeln und größere Ebenen beim Erstellen und Verarbeiten der Dokumente berücksichtigen.</span><span class="sxs-lookup"><span data-stu-id="43be1-164">They should treat the XML documents as opaque and let higher layers worry about generating and parsing the documents.</span></span>

<span data-ttu-id="43be1-165">Es gibt vier integrierte, konkrete Typen, die implementieren `IXmlRepository`:</span><span class="sxs-lookup"><span data-stu-id="43be1-165">There are four built-in concrete types which implement `IXmlRepository`:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="43be1-166">Filesystemxmlrepository</span><span class="sxs-lookup"><span data-stu-id="43be1-166">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="43be1-167">Registryxmlrepository</span><span class="sxs-lookup"><span data-stu-id="43be1-167">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="43be1-168">Azurestorage. azureblobxmlrepository</span><span class="sxs-lookup"><span data-stu-id="43be1-168">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="43be1-169">Redisxmlrepository</span><span class="sxs-lookup"><span data-stu-id="43be1-169">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="43be1-170">Filesystemxmlrepository</span><span class="sxs-lookup"><span data-stu-id="43be1-170">FileSystemXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [<span data-ttu-id="43be1-171">Registryxmlrepository</span><span class="sxs-lookup"><span data-stu-id="43be1-171">RegistryXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [<span data-ttu-id="43be1-172">Azurestorage. azureblobxmlrepository</span><span class="sxs-lookup"><span data-stu-id="43be1-172">AzureStorage.AzureBlobXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [<span data-ttu-id="43be1-173">Redisxmlrepository</span><span class="sxs-lookup"><span data-stu-id="43be1-173">RedisXmlRepository</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

<span data-ttu-id="43be1-174">Weitere Informationen finden Sie im [Dokument Schlüsselspeicher Anbieter](xref:security/data-protection/implementation/key-storage-providers) .</span><span class="sxs-lookup"><span data-stu-id="43be1-174">See the [key storage providers document](xref:security/data-protection/implementation/key-storage-providers) for more information.</span></span>

<span data-ttu-id="43be1-175">Die Registrierung eines `IXmlRepository` benutzerdefinierten ist sinnvoll, wenn ein anderer Sicherungs Speicher verwendet wird (z. b. Azure Table Storage).</span><span class="sxs-lookup"><span data-stu-id="43be1-175">Registering a custom `IXmlRepository` is appropriate when using a different backing store (for example, Azure Table Storage).</span></span>

<span data-ttu-id="43be1-176">Registrieren Sie eine benutzerdefinierte `IXmlRepository` -Instanz, um das standardrepository Anwendungs weit zu ändern:</span><span class="sxs-lookup"><span data-stu-id="43be1-176">To change the default repository application-wide, register a custom `IXmlRepository` instance:</span></span>

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

## <a name="ixmlencryptor"></a><span data-ttu-id="43be1-177">Ixmlencryptor</span><span class="sxs-lookup"><span data-stu-id="43be1-177">IXmlEncryptor</span></span>

<span data-ttu-id="43be1-178">Die `IXmlEncryptor` -Schnittstelle stellt einen Typ dar, der ein Klartext-XML-Element verschlüsseln kann.</span><span class="sxs-lookup"><span data-stu-id="43be1-178">The `IXmlEncryptor` interface represents a type that can encrypt a plaintext XML element.</span></span> <span data-ttu-id="43be1-179">Es macht eine einzelne API verfügbar:</span><span class="sxs-lookup"><span data-stu-id="43be1-179">It exposes a single API:</span></span>

* <span data-ttu-id="43be1-180">Verschlüsseln (XElement Klartext): verschlüsseltedxmlinfo</span><span class="sxs-lookup"><span data-stu-id="43be1-180">Encrypt(XElement plaintextElement) : EncryptedXmlInfo</span></span>

<span data-ttu-id="43be1-181">Wenn eine serialisierte `IAuthenticatedEncryptorDescriptor` Elemente enthält, die als "erfordert Verschlüsselung" gekennzeichnet sind `XmlKeyManager` , werden diese Elemente von durch die `IXmlEncryptor`- `Encrypt` Methode der konfigurierten-Methode ausgeführt, und es wird das enchiffrierte-Element und nicht das `IXmlRepository`Klartext-Element in persistent gespeichert.</span><span class="sxs-lookup"><span data-stu-id="43be1-181">If a serialized `IAuthenticatedEncryptorDescriptor` contains any elements marked as "requires encryption", then `XmlKeyManager` will run those elements through the configured `IXmlEncryptor`'s `Encrypt` method, and it will persist the enciphered element rather than the plaintext element to the `IXmlRepository`.</span></span> <span data-ttu-id="43be1-182">Bei der Ausgabe der `Encrypt` -Methode handelt `EncryptedXmlInfo` es sich um ein-Objekt.</span><span class="sxs-lookup"><span data-stu-id="43be1-182">The output of the `Encrypt` method is an `EncryptedXmlInfo` object.</span></span> <span data-ttu-id="43be1-183">Bei diesem Objekt handelt es sich um einen Wrapper, der sowohl das resultierende `XElement` chiffrierte Element als auch `IXmlDecryptor` den Typ enthält, der einen darstellt, mit dem das entsprechende Element entschlüsselt werden kann.</span><span class="sxs-lookup"><span data-stu-id="43be1-183">This object is a wrapper which contains both the resultant enciphered `XElement` and the Type which represents an `IXmlDecryptor` which can be used to decipher the corresponding element.</span></span>

<span data-ttu-id="43be1-184">Es gibt vier integrierte, konkrete Typen, die implementieren `IXmlEncryptor`:</span><span class="sxs-lookup"><span data-stu-id="43be1-184">There are four built-in concrete types which implement `IXmlEncryptor`:</span></span>

* [<span data-ttu-id="43be1-185">Certifi-exmlencryptor</span><span class="sxs-lookup"><span data-stu-id="43be1-185">CertificateXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [<span data-ttu-id="43be1-186">Dpapingxmlencryptor</span><span class="sxs-lookup"><span data-stu-id="43be1-186">DpapiNGXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [<span data-ttu-id="43be1-187">Dpapixmlencryptor</span><span class="sxs-lookup"><span data-stu-id="43be1-187">DpapiXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [<span data-ttu-id="43be1-188">Nullxmlencryptor</span><span class="sxs-lookup"><span data-stu-id="43be1-188">NullXmlEncryptor</span></span>](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

<span data-ttu-id="43be1-189">Weitere Informationen finden Sie im [Dokument Key Encryption at Rest](xref:security/data-protection/implementation/key-encryption-at-rest) .</span><span class="sxs-lookup"><span data-stu-id="43be1-189">See the [key encryption at rest document](xref:security/data-protection/implementation/key-encryption-at-rest) for more information.</span></span>

<span data-ttu-id="43be1-190">Registrieren Sie eine benutzerdefinierte `IXmlEncryptor` -Instanz, um den standardmäßigen Rest-Mechanismus für die Schlüssel Verschlüsselung zu ändern:</span><span class="sxs-lookup"><span data-stu-id="43be1-190">To change the default key-encryption-at-rest mechanism application-wide, register a custom `IXmlEncryptor` instance:</span></span>

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

## <a name="ixmldecryptor"></a><span data-ttu-id="43be1-191">Ixmldecryptor</span><span class="sxs-lookup"><span data-stu-id="43be1-191">IXmlDecryptor</span></span>

<span data-ttu-id="43be1-192">Die `IXmlDecryptor` -Schnittstelle stellt einen Typ dar, der weiß, `XElement` wie ein entschlüsselt wird, das über `IXmlEncryptor`einen verschlüsselt wurde.</span><span class="sxs-lookup"><span data-stu-id="43be1-192">The `IXmlDecryptor` interface represents a type that knows how to decrypt an `XElement` that was enciphered via an `IXmlEncryptor`.</span></span> <span data-ttu-id="43be1-193">Es macht eine einzelne API verfügbar:</span><span class="sxs-lookup"><span data-stu-id="43be1-193">It exposes a single API:</span></span>

* <span data-ttu-id="43be1-194">Entschlüsseln (XElement-verschlüsseltedelement): XElement</span><span class="sxs-lookup"><span data-stu-id="43be1-194">Decrypt(XElement encryptedElement) : XElement</span></span>

<span data-ttu-id="43be1-195">Die `Decrypt` -Methode macht die von `IXmlEncryptor.Encrypt`ausgeführte Verschlüsselung unerledigt.</span><span class="sxs-lookup"><span data-stu-id="43be1-195">The `Decrypt` method undoes the encryption performed by `IXmlEncryptor.Encrypt`.</span></span> <span data-ttu-id="43be1-196">Im allgemeinen verfügt jede `IXmlEncryptor` konkrete Implementierung über eine entsprechende konkrete `IXmlDecryptor` Implementierung.</span><span class="sxs-lookup"><span data-stu-id="43be1-196">Generally, each concrete `IXmlEncryptor` implementation will have a corresponding concrete `IXmlDecryptor` implementation.</span></span>

<span data-ttu-id="43be1-197">Typen, die `IXmlDecryptor` implementieren, sollten einen der folgenden zwei öffentlichen Konstruktoren aufweisen:</span><span class="sxs-lookup"><span data-stu-id="43be1-197">Types which implement `IXmlDecryptor` should have one of the following two public constructors:</span></span>

* <span data-ttu-id="43be1-198">. ctor (IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="43be1-198">.ctor(IServiceProvider)</span></span>
* <span data-ttu-id="43be1-199">. ctor ()</span><span class="sxs-lookup"><span data-stu-id="43be1-199">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="43be1-200">Der `IServiceProvider` an den Konstruktor übergeben kann NULL sein.</span><span class="sxs-lookup"><span data-stu-id="43be1-200">The `IServiceProvider` passed to the constructor may be null.</span></span>

## <a name="ikeyescrowsink"></a><span data-ttu-id="43be1-201">Ikeyescrowsink</span><span class="sxs-lookup"><span data-stu-id="43be1-201">IKeyEscrowSink</span></span>

<span data-ttu-id="43be1-202">Die `IKeyEscrowSink` -Schnittstelle stellt einen Typ dar, der die Hinterlegung vertraulicher Informationen durchführen kann.</span><span class="sxs-lookup"><span data-stu-id="43be1-202">The `IKeyEscrowSink` interface represents a type that can perform escrow of sensitive information.</span></span> <span data-ttu-id="43be1-203">Beachten Sie, dass serialisierte Deskriptoren vertrauliche Informationen (z. b. kryptografische Materialien) enthalten können. Dies hat zur Einführung des Typs [ixmlencryptor](#ixmlencryptor) geführt.</span><span class="sxs-lookup"><span data-stu-id="43be1-203">Recall that serialized descriptors might contain sensitive information (such as cryptographic material), and this is what led to the introduction of the [IXmlEncryptor](#ixmlencryptor) type in the first place.</span></span> <span data-ttu-id="43be1-204">Es treten jedoch Fehler auf, und Schlüssel Ringe können gelöscht oder beschädigt werden.</span><span class="sxs-lookup"><span data-stu-id="43be1-204">However, accidents happen, and key rings can be deleted or become corrupted.</span></span>

<span data-ttu-id="43be1-205">Die Hinterlegungs Schnittstelle stellt eine Notfall-escapeschraffur dar und ermöglicht den Zugriff auf die unformatierte serialisierte XML, bevor Sie von einem konfigurierten [ixmlencryptor](#ixmlencryptor)transformiert wird.</span><span class="sxs-lookup"><span data-stu-id="43be1-205">The escrow interface provides an emergency escape hatch, allowing access to the raw serialized XML before it's transformed by any configured [IXmlEncryptor](#ixmlencryptor).</span></span> <span data-ttu-id="43be1-206">Die-Schnittstelle macht eine einzelne API verfügbar:</span><span class="sxs-lookup"><span data-stu-id="43be1-206">The interface exposes a single API:</span></span>

* <span data-ttu-id="43be1-207">Store (GUID-Schlüssel-ID, XElement-Element)</span><span class="sxs-lookup"><span data-stu-id="43be1-207">Store(Guid keyId, XElement element)</span></span>

<span data-ttu-id="43be1-208">Es liegt an der `IKeyEscrowSink` Implementierung, das bereitgestellte Element auf sichere Weise in Übereinstimmung mit der Geschäfts Richtlinie zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="43be1-208">It's up to the `IKeyEscrowSink` implementation to handle the provided element in a secure manner consistent with business policy.</span></span> <span data-ttu-id="43be1-209">Eine mögliche Implementierung könnte sein, dass die hinterlegte Senke das XML-Element mit einem bekannten Unternehmens-X. 509-Zertifikat verschlüsselt, bei dem der private Schlüssel des Zertifikats verwendet wurde. der `CertificateXmlEncryptor` Typ kann dies unterstützen.</span><span class="sxs-lookup"><span data-stu-id="43be1-209">One possible implementation could be for the escrow sink to encrypt the XML element using a known corporate X.509 certificate where the certificate's private key has been escrowed; the `CertificateXmlEncryptor` type can assist with this.</span></span> <span data-ttu-id="43be1-210">Die `IKeyEscrowSink` -Implementierung ist auch dafür verantwortlich, das bereitgestellte-Element entsprechend beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="43be1-210">The `IKeyEscrowSink` implementation is also responsible for persisting the provided element appropriately.</span></span>

<span data-ttu-id="43be1-211">Standardmäßig ist kein Hinterlegungs Mechanismus aktiviert, aber Server Administratoren können [Dies Global konfigurieren](xref:security/data-protection/configuration/machine-wide-policy).</span><span class="sxs-lookup"><span data-stu-id="43be1-211">By default no escrow mechanism is enabled, though server administrators can [configure this globally](xref:security/data-protection/configuration/machine-wide-policy).</span></span> <span data-ttu-id="43be1-212">Sie kann auch Programm gesteuert über die `IDataProtectionBuilder.AddKeyEscrowSink` -Methode konfiguriert werden, wie im folgenden Beispiel gezeigt.</span><span class="sxs-lookup"><span data-stu-id="43be1-212">It can also be configured programmatically via the `IDataProtectionBuilder.AddKeyEscrowSink` method as shown in the sample below.</span></span> <span data-ttu-id="43be1-213">Die `AddKeyEscrowSink` -Methoden Überladungen spiegeln `IServiceCollection.AddSingleton` die `IServiceCollection.AddInstance` -und-über `IKeyEscrowSink` Ladungen wider, da-Instanzen als Singletons gedacht sind.</span><span class="sxs-lookup"><span data-stu-id="43be1-213">The `AddKeyEscrowSink` method overloads mirror the `IServiceCollection.AddSingleton` and `IServiceCollection.AddInstance` overloads, as `IKeyEscrowSink` instances are intended to be singletons.</span></span> <span data-ttu-id="43be1-214">Wenn mehrere `IKeyEscrowSink` Instanzen registriert sind, werden diese während der Schlüsselgenerierung aufgerufen, sodass Schlüssel gleichzeitig an mehrere Mechanismen zusammengestellt werden können.</span><span class="sxs-lookup"><span data-stu-id="43be1-214">If multiple `IKeyEscrowSink` instances are registered, each one will be called during key generation, so keys can be escrowed to multiple mechanisms simultaneously.</span></span>

<span data-ttu-id="43be1-215">Es gibt keine API zum Lesen von Material aus `IKeyEscrowSink` einer Instanz.</span><span class="sxs-lookup"><span data-stu-id="43be1-215">There's no API to read material from an `IKeyEscrowSink` instance.</span></span> <span data-ttu-id="43be1-216">Dies entspricht der Entwurfs Theorie des Hinterlegungs Mechanismus: Sie soll das Schlüsselmaterial für eine vertrauenswürdige Zertifizierungsstelle zugänglich machen, und da die Anwendung selbst keine vertrauenswürdige Autorität ist, sollte Sie keinen Zugriff auf Ihr eigenes Material haben.</span><span class="sxs-lookup"><span data-stu-id="43be1-216">This is consistent with the design theory of the escrow mechanism: it's intended to make the key material accessible to a trusted authority, and since the application is itself not a trusted authority, it shouldn't have access to its own escrowed material.</span></span>

<span data-ttu-id="43be1-217">Der folgende Beispielcode veranschaulicht das Erstellen und registrieren `IKeyEscrowSink` eines-Schlüssels, bei dem Schlüssel gespeichert werden, sodass nur Mitglieder von "condesodomain Admins" diese wiederherstellen können.</span><span class="sxs-lookup"><span data-stu-id="43be1-217">The following sample code demonstrates creating and registering an `IKeyEscrowSink` where keys are escrowed such that only members of "CONTOSODomain Admins" can recover them.</span></span>

> [!NOTE]
> <span data-ttu-id="43be1-218">Zum Ausführen dieses Beispiels müssen Sie sich auf einem in die Domäne eingebundenen Windows 8/Windows Server 2012-Computer befinden, und der Domänen Controller muss Windows Server 2012 oder höher sein.</span><span class="sxs-lookup"><span data-stu-id="43be1-218">To run this sample, you must be on a domain-joined Windows 8 / Windows Server 2012 machine, and the domain controller must be Windows Server 2012 or later.</span></span>

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
