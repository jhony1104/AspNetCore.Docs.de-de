---
title: Kryptografieerweiterbarkeit in Core in ASP.NET Core
author: rick-anderson
description: Informationen Sie zu IAuthenticatedEncryptor, IAuthenticatedEncryptorDescriptor, IAuthenticatedEncryptorDescriptorDeserializer und der obersten Ebene Factory.
ms.author: riande
ms.date: 8/11/2017
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: cf4a142992efe5b00a75285ef9ad9735fe7be411
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896677"
---
# <a name="core-cryptography-extensibility-in-aspnet-core"></a><span data-ttu-id="a20ce-103">Kryptografieerweiterbarkeit in Core in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a20ce-103">Core cryptography extensibility in ASP.NET Core</span></span>

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> <span data-ttu-id="a20ce-104">Typen, die die folgenden Schnittstellen implementieren, sollten threadsicher werden für mehrere Aufrufer.</span><span class="sxs-lookup"><span data-stu-id="a20ce-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a><span data-ttu-id="a20ce-105">IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="a20ce-105">IAuthenticatedEncryptor</span></span>

<span data-ttu-id="a20ce-106">Die **IAuthenticatedEncryptor** Schnittstelle ist der grundlegende Baustein des kryptografischen Subsystems.</span><span class="sxs-lookup"><span data-stu-id="a20ce-106">The **IAuthenticatedEncryptor** interface is the basic building block of the cryptographic subsystem.</span></span> <span data-ttu-id="a20ce-107">In der Regel eine IAuthenticatedEncryptor pro Schlüssel vorhanden ist, und die IAuthenticatedEncryptor Instanz dient als Wrapper für alle kryptografische Schlüsseldaten und algorithmische Informationen, die zum Ausführen kryptografischer Vorgänge erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="a20ce-107">There's generally one IAuthenticatedEncryptor per key, and the IAuthenticatedEncryptor instance wraps all cryptographic key material and algorithmic information necessary to perform cryptographic operations.</span></span>

<span data-ttu-id="a20ce-108">Wie der Name schon sagt, ist der Typ für die Bereitstellung des authentifizierten Ver- und Entschlüsselung Dienste verantwortlich.</span><span class="sxs-lookup"><span data-stu-id="a20ce-108">As its name suggests, the type is responsible for providing authenticated encryption and decryption services.</span></span> <span data-ttu-id="a20ce-109">Er macht die folgenden zwei APIs verfügbar.</span><span class="sxs-lookup"><span data-stu-id="a20ce-109">It exposes the following two APIs.</span></span>

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

<span data-ttu-id="a20ce-110">Die Encrypt-Methode gibt ein Blob, das dazu nur-Text und ein authentifizierungstag enthält.</span><span class="sxs-lookup"><span data-stu-id="a20ce-110">The Encrypt method returns a blob that includes the enciphered plaintext and an authentication tag.</span></span> <span data-ttu-id="a20ce-111">Das authentifizierungstag muss die zusätzlichen authentifizierten Daten (AAD) umfassen, obwohl die AAD selbst nicht aus der letzten Nutzlast wiederhergestellt werden muss.</span><span class="sxs-lookup"><span data-stu-id="a20ce-111">The authentication tag must encompass the additional authenticated data (AAD), though the AAD itself need not be recoverable from the final payload.</span></span> <span data-ttu-id="a20ce-112">Die Decrypt-Methode überprüft das authentifizierungstag und die deciphered Nutzlast zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="a20ce-112">The Decrypt method validates the authentication tag and returns the deciphered payload.</span></span> <span data-ttu-id="a20ce-113">Alle Fehler auf (mit Ausnahme von "ArgumentNullException" und ähnlich) sollte auf CryptographicException antibiotischem sein.</span><span class="sxs-lookup"><span data-stu-id="a20ce-113">All failures (except ArgumentNullException and similar) should be homogenized to CryptographicException.</span></span>

> [!NOTE]
> <span data-ttu-id="a20ce-114">Die IAuthenticatedEncryptor-Instanz selbst gar nicht wirklich das Schlüsselmaterial enthält.</span><span class="sxs-lookup"><span data-stu-id="a20ce-114">The IAuthenticatedEncryptor instance itself doesn't actually need to contain the key material.</span></span> <span data-ttu-id="a20ce-115">Die Implementierung könnte z. B. in ein HSM für alle Vorgänge delegieren.</span><span class="sxs-lookup"><span data-stu-id="a20ce-115">For example, the implementation could delegate to an HSM for all operations.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a><span data-ttu-id="a20ce-116">Vorgehensweise: Erstellen einer IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="a20ce-116">How to create an IAuthenticatedEncryptor</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="a20ce-117">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="a20ce-117">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="a20ce-118">Die **IAuthenticatedEncryptorFactory** Schnittstelle darstellt, einen Typ, der weiß, wie Sie erstellen eine [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) Instanz.</span><span class="sxs-lookup"><span data-stu-id="a20ce-118">The **IAuthenticatedEncryptorFactory** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="a20ce-119">Die API lautet wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="a20ce-119">Its API is as follows.</span></span>

* <span data-ttu-id="a20ce-120">CreateEncryptorInstance ("iKey" Key): IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="a20ce-120">CreateEncryptorInstance(IKey key) : IAuthenticatedEncryptor</span></span>

<span data-ttu-id="a20ce-121">Für jede bestimmte Instanz "iKey" alle authentifizierten lehnen die CreateEncryptorInstance-Methode erstellt sollte als äquivalent betrachtet, wie in der unten stehende Codebeispiel.</span><span class="sxs-lookup"><span data-stu-id="a20ce-121">For any given IKey instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorFactory instance and an IKey instance
IAuthenticatedEncryptorFactory factory = ...;
IKey key = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = factory.CreateEncryptorInstance(key);
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = factory.CreateEncryptorInstance(key);
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="a20ce-122">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="a20ce-122">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="a20ce-123">Die **IAuthenticatedEncryptorDescriptor** Schnittstelle darstellt, einen Typ, der weiß, wie Sie erstellen eine [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) Instanz.</span><span class="sxs-lookup"><span data-stu-id="a20ce-123">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="a20ce-124">Die API lautet wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="a20ce-124">Its API is as follows.</span></span>

* <span data-ttu-id="a20ce-125">CreateEncryptorInstance() : IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="a20ce-125">CreateEncryptorInstance() : IAuthenticatedEncryptor</span></span>

* <span data-ttu-id="a20ce-126">ExportToXml() : XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="a20ce-126">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

<span data-ttu-id="a20ce-127">Wie IAuthenticatedEncryptor wird angenommen, dass eine Instanz von IAuthenticatedEncryptorDescriptor um einen bestimmten Schlüssel zu umschließen.</span><span class="sxs-lookup"><span data-stu-id="a20ce-127">Like IAuthenticatedEncryptor, an instance of IAuthenticatedEncryptorDescriptor is assumed to wrap one specific key.</span></span> <span data-ttu-id="a20ce-128">Dies bedeutet, dass für jede bestimmte Instanz IAuthenticatedEncryptorDescriptor alle authentifizierten lehnen die CreateEncryptorInstance-Methode erstellt wie im entsprechenden berücksichtigt werden sollte dem folgenden Codebeispiel.</span><span class="sxs-lookup"><span data-stu-id="a20ce-128">This means that for any given IAuthenticatedEncryptorDescriptor instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorDescriptor instance
IAuthenticatedEncryptorDescriptor descriptor = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = descriptor.CreateEncryptorInstance();
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = descriptor.CreateEncryptorInstance();
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

---

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a><span data-ttu-id="a20ce-129">IAuthenticatedEncryptorDescriptor ((ASP.NET Core 2.x nur)</span><span class="sxs-lookup"><span data-stu-id="a20ce-129">IAuthenticatedEncryptorDescriptor (ASP.NET Core 2.x only)</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="a20ce-130">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="a20ce-130">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="a20ce-131">Die **IAuthenticatedEncryptorDescriptor** Schnittstelle darstellt, einen Typ, der weiß, wie Sie selbst nach XML exportieren.</span><span class="sxs-lookup"><span data-stu-id="a20ce-131">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to export itself to XML.</span></span> <span data-ttu-id="a20ce-132">Die API lautet wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="a20ce-132">Its API is as follows.</span></span>

* <span data-ttu-id="a20ce-133">ExportToXml() : XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="a20ce-133">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="a20ce-134">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="a20ce-134">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a><span data-ttu-id="a20ce-135">XML-Serialisierung</span><span class="sxs-lookup"><span data-stu-id="a20ce-135">XML Serialization</span></span>

<span data-ttu-id="a20ce-136">Der Hauptunterschied zwischen IAuthenticatedEncryptor und IAuthenticatedEncryptorDescriptor ist, dass der Deskriptor weiß, wie die Verschlüsselung zu erstellen, und klicken Sie mit gültigen Argumente bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="a20ce-136">The primary difference between IAuthenticatedEncryptor and IAuthenticatedEncryptorDescriptor is that the descriptor knows how to create the encryptor and supply it with valid arguments.</span></span> <span data-ttu-id="a20ce-137">Erwägen Sie eine IAuthenticatedEncryptor SymmetricAlgorithm und KeyedHashAlgorithm, deren Implementierung abhängig.</span><span class="sxs-lookup"><span data-stu-id="a20ce-137">Consider an IAuthenticatedEncryptor whose implementation relies on SymmetricAlgorithm and KeyedHashAlgorithm.</span></span> <span data-ttu-id="a20ce-138">Die Verschlüsselungsmethode für die Aufgabe besteht darin, diese Typen nutzen, aber er weiß nicht unbedingt, in denen diese Typen stammen, damit sie wirklich keiner entsprechenden Beschreibung zum selbst neu zu erstellen schreiben kann, wenn die Anwendung neu gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="a20ce-138">The encryptor's job is to consume these types, but it doesn't necessarily know where these types came from, so it can't really write out a proper description of how to recreate itself if the application restarts.</span></span> <span data-ttu-id="a20ce-139">Der Deskriptor fungiert als eine höhere Sicherheitsstufe als Ergänzung.</span><span class="sxs-lookup"><span data-stu-id="a20ce-139">The descriptor acts as a higher level on top of this.</span></span> <span data-ttu-id="a20ce-140">Da der Deskriptor weiß, wie die Verschlüsselung-Instanz zu erstellen (z. B. er weiß, wie die erforderlichen Algorithmen zu erstellen), können sie dieses Wissen im XML-Format serialisieren, damit die Verschlüsselung-Instanz wiederhergestellt werden kann, nachdem eine Anwendung zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="a20ce-140">Since the descriptor knows how to create the encryptor instance (e.g., it knows how to create the required algorithms), it can serialize that knowledge in XML form so that the encryptor instance can be recreated after an application reset.</span></span>

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

<span data-ttu-id="a20ce-141">Der Deskriptor kann über die Routine ExportToXml serialisiert werden.</span><span class="sxs-lookup"><span data-stu-id="a20ce-141">The descriptor can be serialized via its ExportToXml routine.</span></span> <span data-ttu-id="a20ce-142">Diese Routine gibt ein XmlSerializedDescriptorInfo enthält zwei Eigenschaften: die "XElement"-Darstellung des Deskriptors und den Typ steht für ein [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) kann verwendet, um diesen Deskriptor, erhält der entsprechende "XElement" erwecken.</span><span class="sxs-lookup"><span data-stu-id="a20ce-142">This routine returns an XmlSerializedDescriptorInfo which contains two properties: the XElement representation of the descriptor and the Type which represents an [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) which can be used to resurrect this descriptor given the corresponding XElement.</span></span>

<span data-ttu-id="a20ce-143">Der serialisierte Deskriptor enthalten möglicherweise vertrauliche Informationen wie z. B. kryptografische Schlüsseldaten.</span><span class="sxs-lookup"><span data-stu-id="a20ce-143">The serialized descriptor may contain sensitive information such as cryptographic key material.</span></span> <span data-ttu-id="a20ce-144">Das System zum Schutz von Daten verfügt über integrierte Unterstützung für das Verschlüsseln von Informationen, bevor es in den Speicher dauerhaft gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="a20ce-144">The data protection system has built-in support for encrypting information before it's persisted to storage.</span></span> <span data-ttu-id="a20ce-145">Um diesen Vorteil nutzen zu können, sollte der Deskriptor markieren Sie das Element enthält sensiblen Informationen mit dem Attribut namens "" RequiresEncryption"" (Xmlns "<http://schemas.asp.net/2015/03/dataProtection>"), Wert "true".</span><span class="sxs-lookup"><span data-stu-id="a20ce-145">To take advantage of this, the descriptor should mark the element which contains sensitive information with the attribute name "requiresEncryption" (xmlns "<http://schemas.asp.net/2015/03/dataProtection>"), value "true".</span></span>

>[!TIP]
> <span data-ttu-id="a20ce-146">Für das Festlegen dieses Attributs ist ein Hilfs-API verfügbar.</span><span class="sxs-lookup"><span data-stu-id="a20ce-146">There's a helper API for setting this attribute.</span></span> <span data-ttu-id="a20ce-147">Rufen Sie die Erweiterungsmethode XElement.MarkAsRequiresEncryption() im Namespace Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel befindet.</span><span class="sxs-lookup"><span data-stu-id="a20ce-147">Call the extension method XElement.MarkAsRequiresEncryption() located in namespace Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.</span></span>

<span data-ttu-id="a20ce-148">Es kann auch Fälle, in dem der serialisierte Deskriptor vertraulichen Informationen enthalten, nicht, vorhanden sein.</span><span class="sxs-lookup"><span data-stu-id="a20ce-148">There can also be cases where the serialized descriptor doesn't contain sensitive information.</span></span> <span data-ttu-id="a20ce-149">Betrachten Sie erneut den Fall eines kryptografischen Schlüssels, der in einem HSM gespeichert.</span><span class="sxs-lookup"><span data-stu-id="a20ce-149">Consider again the case of a cryptographic key stored in an HSM.</span></span> <span data-ttu-id="a20ce-150">Der Deskriptor kann nicht beim selbst serialisieren, da das HSM für das Material in unverschlüsselter Form verfügbar machen wird nicht das Schlüsselmaterial geschrieben.</span><span class="sxs-lookup"><span data-stu-id="a20ce-150">The descriptor cannot write out the key material when serializing itself since the HSM won't expose the material in plaintext form.</span></span> <span data-ttu-id="a20ce-151">Stattdessen kann der Deskriptor schreiben, die Schlüssel Wrapper-Version des Schlüssels (wenn das HSM Export auf diese Weise kann) oder den HSMs-Bezeichner für den Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="a20ce-151">Instead, the descriptor might write out the key-wrapped version of the key (if the HSM allows export in this fashion) or the HSM's own unique identifier for the key.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a><span data-ttu-id="a20ce-152">IAuthenticatedEncryptorDescriptorDeserializer</span><span class="sxs-lookup"><span data-stu-id="a20ce-152">IAuthenticatedEncryptorDescriptorDeserializer</span></span>

<span data-ttu-id="a20ce-153">Die **IAuthenticatedEncryptorDescriptorDeserializer** Schnittstelle darstellt, einen Typ, der weiß, wie Sie eine Instanz eines "XElement" IAuthenticatedEncryptorDescriptor zu deserialisieren.</span><span class="sxs-lookup"><span data-stu-id="a20ce-153">The **IAuthenticatedEncryptorDescriptorDeserializer** interface represents a type that knows how to deserialize an IAuthenticatedEncryptorDescriptor instance from an XElement.</span></span> <span data-ttu-id="a20ce-154">Sie macht eine einzelne Methode verfügbar:</span><span class="sxs-lookup"><span data-stu-id="a20ce-154">It exposes a single method:</span></span>

* <span data-ttu-id="a20ce-155">ImportFromXml ("XElement"-Element): IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="a20ce-155">ImportFromXml(XElement element) : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="a20ce-156">Die ImportFromXml-Methode akzeptiert die "XElement", die von zurückgegeben wurde [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) und eine entsprechende von der ursprünglichen IAuthenticatedEncryptorDescriptor erstellt.</span><span class="sxs-lookup"><span data-stu-id="a20ce-156">The ImportFromXml method takes the XElement that was returned by [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) and creates an equivalent of the original IAuthenticatedEncryptorDescriptor.</span></span>

<span data-ttu-id="a20ce-157">Die IAuthenticatedEncryptorDescriptorDeserializer implementieren sollte eine der folgenden zwei öffentliche Konstruktoren aufweisen:</span><span class="sxs-lookup"><span data-stu-id="a20ce-157">Types which implement IAuthenticatedEncryptorDescriptorDeserializer should have one of the following two public constructors:</span></span>

* <span data-ttu-id="a20ce-158">.ctor(IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="a20ce-158">.ctor(IServiceProvider)</span></span>

* <span data-ttu-id="a20ce-159">.ctor()</span><span class="sxs-lookup"><span data-stu-id="a20ce-159">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="a20ce-160">Der an den Konstruktor übergebene IServiceProvider kann null sein.</span><span class="sxs-lookup"><span data-stu-id="a20ce-160">The IServiceProvider passed to the constructor may be null.</span></span>

## <a name="the-top-level-factory"></a><span data-ttu-id="a20ce-161">Die Factory der obersten Ebene</span><span class="sxs-lookup"><span data-stu-id="a20ce-161">The top-level factory</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="a20ce-162">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="a20ce-162">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="a20ce-163">Die **AlgorithmConfiguration** Klasse stellt einen Typ, der weiß, wie Sie erstellen [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) Instanzen.</span><span class="sxs-lookup"><span data-stu-id="a20ce-163">The **AlgorithmConfiguration** class represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="a20ce-164">Er macht eine einzelne API verfügbar.</span><span class="sxs-lookup"><span data-stu-id="a20ce-164">It exposes a single API.</span></span>

* <span data-ttu-id="a20ce-165">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="a20ce-165">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="a20ce-166">Stellen Sie sich AlgorithmConfiguration als Factory der obersten Ebene.</span><span class="sxs-lookup"><span data-stu-id="a20ce-166">Think of AlgorithmConfiguration as the top-level factory.</span></span> <span data-ttu-id="a20ce-167">Die Konfiguration dient als Vorlage.</span><span class="sxs-lookup"><span data-stu-id="a20ce-167">The configuration serves as a template.</span></span> <span data-ttu-id="a20ce-168">Umschlossene algorithmische Informationen (z. B. diese Konfiguration erzeugt Deskriptoren mit einem Hauptschlüssel AES-128-GCM), aber es wurde noch nicht mit einem bestimmten Schlüssel verknüpft.</span><span class="sxs-lookup"><span data-stu-id="a20ce-168">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it's not yet associated with a specific key.</span></span>

<span data-ttu-id="a20ce-169">Wenn CreateNewDescriptor aufgerufen wird, neues des Schlüsselmaterials ausschließlich für diesen Aufruf erstellt wird und eine neue IAuthenticatedEncryptorDescriptor erzeugt wird umschließt die diesem Schlüsselmaterial sowie die algorithmische Informationen erforderlich, um das Material zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="a20ce-169">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="a20ce-170">Das Schlüsselmaterial konnte in der Software erstellt (und im Arbeitsspeicher gespeichert werden), es erstellt und in einem HSM usw. gespeichert werden konnte.</span><span class="sxs-lookup"><span data-stu-id="a20ce-170">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="a20ce-171">Der entscheidende Punkt ist, dass zwei Aufrufe CreateNewDescriptor nie entsprechende IAuthenticatedEncryptorDescriptor-Instanzen erstellen soll.</span><span class="sxs-lookup"><span data-stu-id="a20ce-171">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="a20ce-172">Welche AlgorithmConfiguration dient als Einstiegspunkt für die schlüsselerstellung Routinen wie z. B. [automatische schlüsselrollover](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span><span class="sxs-lookup"><span data-stu-id="a20ce-172">The AlgorithmConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span></span> <span data-ttu-id="a20ce-173">Um die Implementierung für alle zukünftigen Schlüssel zu ändern, legen Sie die AuthenticatedEncryptorConfiguration-Eigenschaft in KeyManagementOptions aus.</span><span class="sxs-lookup"><span data-stu-id="a20ce-173">To change the implementation for all future keys, set the AuthenticatedEncryptorConfiguration property in KeyManagementOptions.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="a20ce-174">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="a20ce-174">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="a20ce-175">Die **IAuthenticatedEncryptorConfiguration** Schnittstelle darstellt, einen Typ, der weiß, wie Sie erstellen [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) Instanzen.</span><span class="sxs-lookup"><span data-stu-id="a20ce-175">The **IAuthenticatedEncryptorConfiguration** interface represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="a20ce-176">Er macht eine einzelne API verfügbar.</span><span class="sxs-lookup"><span data-stu-id="a20ce-176">It exposes a single API.</span></span>

* <span data-ttu-id="a20ce-177">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="a20ce-177">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="a20ce-178">Stellen Sie sich IAuthenticatedEncryptorConfiguration als Factory der obersten Ebene.</span><span class="sxs-lookup"><span data-stu-id="a20ce-178">Think of IAuthenticatedEncryptorConfiguration as the top-level factory.</span></span> <span data-ttu-id="a20ce-179">Die Konfiguration dient als Vorlage.</span><span class="sxs-lookup"><span data-stu-id="a20ce-179">The configuration serves as a template.</span></span> <span data-ttu-id="a20ce-180">Umschlossene algorithmische Informationen (z. B. diese Konfiguration erzeugt Deskriptoren mit einem Hauptschlüssel AES-128-GCM), aber es wurde noch nicht mit einem bestimmten Schlüssel verknüpft.</span><span class="sxs-lookup"><span data-stu-id="a20ce-180">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it's not yet associated with a specific key.</span></span>

<span data-ttu-id="a20ce-181">Wenn CreateNewDescriptor aufgerufen wird, neues des Schlüsselmaterials ausschließlich für diesen Aufruf erstellt wird und eine neue IAuthenticatedEncryptorDescriptor erzeugt wird umschließt die diesem Schlüsselmaterial sowie die algorithmische Informationen erforderlich, um das Material zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="a20ce-181">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="a20ce-182">Das Schlüsselmaterial konnte in der Software erstellt (und im Arbeitsspeicher gespeichert werden), es erstellt und in einem HSM usw. gespeichert werden konnte.</span><span class="sxs-lookup"><span data-stu-id="a20ce-182">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="a20ce-183">Der entscheidende Punkt ist, dass zwei Aufrufe CreateNewDescriptor nie entsprechende IAuthenticatedEncryptorDescriptor-Instanzen erstellen soll.</span><span class="sxs-lookup"><span data-stu-id="a20ce-183">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="a20ce-184">Welche IAuthenticatedEncryptorConfiguration dient als Einstiegspunkt für die schlüsselerstellung Routinen wie z. B. [automatische schlüsselrollover](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span><span class="sxs-lookup"><span data-stu-id="a20ce-184">The IAuthenticatedEncryptorConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span></span> <span data-ttu-id="a20ce-185">Um die Implementierung für alle zukünftigen Schlüssel zu ändern, registrieren Sie einen Singleton IAuthenticatedEncryptorConfiguration im Dienstcontainer.</span><span class="sxs-lookup"><span data-stu-id="a20ce-185">To change the implementation for all future keys, register a singleton IAuthenticatedEncryptorConfiguration in the service container.</span></span>

---
