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
# <a name="core-cryptography-extensibility-in-aspnet-core"></a>Kryptografieerweiterbarkeit in Core in ASP.NET Core

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> Typen, die die folgenden Schnittstellen implementieren, sollten threadsicher werden für mehrere Aufrufer.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a>IAuthenticatedEncryptor

Die **IAuthenticatedEncryptor** Schnittstelle ist der grundlegende Baustein des kryptografischen Subsystems. In der Regel eine IAuthenticatedEncryptor pro Schlüssel vorhanden ist, und die IAuthenticatedEncryptor Instanz dient als Wrapper für alle kryptografische Schlüsseldaten und algorithmische Informationen, die zum Ausführen kryptografischer Vorgänge erforderlich sind.

Wie der Name schon sagt, ist der Typ für die Bereitstellung des authentifizierten Ver- und Entschlüsselung Dienste verantwortlich. Er macht die folgenden zwei APIs verfügbar.

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

Die Encrypt-Methode gibt ein Blob, das dazu nur-Text und ein authentifizierungstag enthält. Das authentifizierungstag muss die zusätzlichen authentifizierten Daten (AAD) umfassen, obwohl die AAD selbst nicht aus der letzten Nutzlast wiederhergestellt werden muss. Die Decrypt-Methode überprüft das authentifizierungstag und die deciphered Nutzlast zurückgegeben. Alle Fehler auf (mit Ausnahme von "ArgumentNullException" und ähnlich) sollte auf CryptographicException antibiotischem sein.

> [!NOTE]
> Die IAuthenticatedEncryptor-Instanz selbst gar nicht wirklich das Schlüsselmaterial enthält. Die Implementierung könnte z. B. in ein HSM für alle Vorgänge delegieren.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a>Vorgehensweise: Erstellen einer IAuthenticatedEncryptor

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Die **IAuthenticatedEncryptorFactory** Schnittstelle darstellt, einen Typ, der weiß, wie Sie erstellen eine [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) Instanz. Die API lautet wie folgt aus:

* CreateEncryptorInstance ("iKey" Key): IAuthenticatedEncryptor

Für jede bestimmte Instanz "iKey" alle authentifizierten lehnen die CreateEncryptorInstance-Methode erstellt sollte als äquivalent betrachtet, wie in der unten stehende Codebeispiel.

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

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Die **IAuthenticatedEncryptorDescriptor** Schnittstelle darstellt, einen Typ, der weiß, wie Sie erstellen eine [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) Instanz. Die API lautet wie folgt aus:

* CreateEncryptorInstance() : IAuthenticatedEncryptor

* ExportToXml() : XmlSerializedDescriptorInfo

Wie IAuthenticatedEncryptor wird angenommen, dass eine Instanz von IAuthenticatedEncryptorDescriptor um einen bestimmten Schlüssel zu umschließen. Dies bedeutet, dass für jede bestimmte Instanz IAuthenticatedEncryptorDescriptor alle authentifizierten lehnen die CreateEncryptorInstance-Methode erstellt wie im entsprechenden berücksichtigt werden sollte dem folgenden Codebeispiel.

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

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a>IAuthenticatedEncryptorDescriptor ((ASP.NET Core 2.x nur)

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Die **IAuthenticatedEncryptorDescriptor** Schnittstelle darstellt, einen Typ, der weiß, wie Sie selbst nach XML exportieren. Die API lautet wie folgt aus:

* ExportToXml() : XmlSerializedDescriptorInfo

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a>XML-Serialisierung

Der Hauptunterschied zwischen IAuthenticatedEncryptor und IAuthenticatedEncryptorDescriptor ist, dass der Deskriptor weiß, wie die Verschlüsselung zu erstellen, und klicken Sie mit gültigen Argumente bereitstellen. Erwägen Sie eine IAuthenticatedEncryptor SymmetricAlgorithm und KeyedHashAlgorithm, deren Implementierung abhängig. Die Verschlüsselungsmethode für die Aufgabe besteht darin, diese Typen nutzen, aber er weiß nicht unbedingt, in denen diese Typen stammen, damit sie wirklich keiner entsprechenden Beschreibung zum selbst neu zu erstellen schreiben kann, wenn die Anwendung neu gestartet wird. Der Deskriptor fungiert als eine höhere Sicherheitsstufe als Ergänzung. Da der Deskriptor weiß, wie die Verschlüsselung-Instanz zu erstellen (z. B. er weiß, wie die erforderlichen Algorithmen zu erstellen), können sie dieses Wissen im XML-Format serialisieren, damit die Verschlüsselung-Instanz wiederhergestellt werden kann, nachdem eine Anwendung zurückgesetzt.

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

Der Deskriptor kann über die Routine ExportToXml serialisiert werden. Diese Routine gibt ein XmlSerializedDescriptorInfo enthält zwei Eigenschaften: die "XElement"-Darstellung des Deskriptors und den Typ steht für ein [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) kann verwendet, um diesen Deskriptor, erhält der entsprechende "XElement" erwecken.

Der serialisierte Deskriptor enthalten möglicherweise vertrauliche Informationen wie z. B. kryptografische Schlüsseldaten. Das System zum Schutz von Daten verfügt über integrierte Unterstützung für das Verschlüsseln von Informationen, bevor es in den Speicher dauerhaft gespeichert wird. Um diesen Vorteil nutzen zu können, sollte der Deskriptor markieren Sie das Element enthält sensiblen Informationen mit dem Attribut namens "" RequiresEncryption"" (Xmlns "<http://schemas.asp.net/2015/03/dataProtection>"), Wert "true".

>[!TIP]
> Für das Festlegen dieses Attributs ist ein Hilfs-API verfügbar. Rufen Sie die Erweiterungsmethode XElement.MarkAsRequiresEncryption() im Namespace Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel befindet.

Es kann auch Fälle, in dem der serialisierte Deskriptor vertraulichen Informationen enthalten, nicht, vorhanden sein. Betrachten Sie erneut den Fall eines kryptografischen Schlüssels, der in einem HSM gespeichert. Der Deskriptor kann nicht beim selbst serialisieren, da das HSM für das Material in unverschlüsselter Form verfügbar machen wird nicht das Schlüsselmaterial geschrieben. Stattdessen kann der Deskriptor schreiben, die Schlüssel Wrapper-Version des Schlüssels (wenn das HSM Export auf diese Weise kann) oder den HSMs-Bezeichner für den Schlüssel.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a>IAuthenticatedEncryptorDescriptorDeserializer

Die **IAuthenticatedEncryptorDescriptorDeserializer** Schnittstelle darstellt, einen Typ, der weiß, wie Sie eine Instanz eines "XElement" IAuthenticatedEncryptorDescriptor zu deserialisieren. Sie macht eine einzelne Methode verfügbar:

* ImportFromXml ("XElement"-Element): IAuthenticatedEncryptorDescriptor

Die ImportFromXml-Methode akzeptiert die "XElement", die von zurückgegeben wurde [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) und eine entsprechende von der ursprünglichen IAuthenticatedEncryptorDescriptor erstellt.

Die IAuthenticatedEncryptorDescriptorDeserializer implementieren sollte eine der folgenden zwei öffentliche Konstruktoren aufweisen:

* .ctor(IServiceProvider)

* .ctor()

> [!NOTE]
> Der an den Konstruktor übergebene IServiceProvider kann null sein.

## <a name="the-top-level-factory"></a>Die Factory der obersten Ebene

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Die **AlgorithmConfiguration** Klasse stellt einen Typ, der weiß, wie Sie erstellen [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) Instanzen. Er macht eine einzelne API verfügbar.

* CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor

Stellen Sie sich AlgorithmConfiguration als Factory der obersten Ebene. Die Konfiguration dient als Vorlage. Umschlossene algorithmische Informationen (z. B. diese Konfiguration erzeugt Deskriptoren mit einem Hauptschlüssel AES-128-GCM), aber es wurde noch nicht mit einem bestimmten Schlüssel verknüpft.

Wenn CreateNewDescriptor aufgerufen wird, neues des Schlüsselmaterials ausschließlich für diesen Aufruf erstellt wird und eine neue IAuthenticatedEncryptorDescriptor erzeugt wird umschließt die diesem Schlüsselmaterial sowie die algorithmische Informationen erforderlich, um das Material zu verwenden. Das Schlüsselmaterial konnte in der Software erstellt (und im Arbeitsspeicher gespeichert werden), es erstellt und in einem HSM usw. gespeichert werden konnte. Der entscheidende Punkt ist, dass zwei Aufrufe CreateNewDescriptor nie entsprechende IAuthenticatedEncryptorDescriptor-Instanzen erstellen soll.

Welche AlgorithmConfiguration dient als Einstiegspunkt für die schlüsselerstellung Routinen wie z. B. [automatische schlüsselrollover](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling). Um die Implementierung für alle zukünftigen Schlüssel zu ändern, legen Sie die AuthenticatedEncryptorConfiguration-Eigenschaft in KeyManagementOptions aus.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Die **IAuthenticatedEncryptorConfiguration** Schnittstelle darstellt, einen Typ, der weiß, wie Sie erstellen [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) Instanzen. Er macht eine einzelne API verfügbar.

* CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor

Stellen Sie sich IAuthenticatedEncryptorConfiguration als Factory der obersten Ebene. Die Konfiguration dient als Vorlage. Umschlossene algorithmische Informationen (z. B. diese Konfiguration erzeugt Deskriptoren mit einem Hauptschlüssel AES-128-GCM), aber es wurde noch nicht mit einem bestimmten Schlüssel verknüpft.

Wenn CreateNewDescriptor aufgerufen wird, neues des Schlüsselmaterials ausschließlich für diesen Aufruf erstellt wird und eine neue IAuthenticatedEncryptorDescriptor erzeugt wird umschließt die diesem Schlüsselmaterial sowie die algorithmische Informationen erforderlich, um das Material zu verwenden. Das Schlüsselmaterial konnte in der Software erstellt (und im Arbeitsspeicher gespeichert werden), es erstellt und in einem HSM usw. gespeichert werden konnte. Der entscheidende Punkt ist, dass zwei Aufrufe CreateNewDescriptor nie entsprechende IAuthenticatedEncryptorDescriptor-Instanzen erstellen soll.

Welche IAuthenticatedEncryptorConfiguration dient als Einstiegspunkt für die schlüsselerstellung Routinen wie z. B. [automatische schlüsselrollover](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling). Um die Implementierung für alle zukünftigen Schlüssel zu ändern, registrieren Sie einen Singleton IAuthenticatedEncryptorConfiguration im Dienstcontainer.

---
