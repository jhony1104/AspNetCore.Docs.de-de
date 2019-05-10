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
# <a name="key-management-extensibility-in-aspnet-core"></a>Schlüsselverwaltungserweiterbarkeit in ASP.NET Core

> [!TIP]
> Lesen der [schlüsselverwaltung](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) Abschnitt vor dem Lesen der in diesem Abschnitt, wie sie einige der grundlegenden Konzepte hinter dieser APIs erläutert.

> [!WARNING]
> Typen, die die folgenden Schnittstellen implementieren, sollten threadsicher werden für mehrere Aufrufer.

## <a name="key"></a>Key

Die `IKey` Schnittstelle ist die grundlegende Darstellung eines Schlüssels in Kryptografiesystem. Der Begriff-Schlüssel ist hier in der abstrakten Sinne, nicht im literal Sinne "kryptografische Schlüsseldaten" verwendet. Ein Schlüssel hat die folgenden Eigenschaften:

* Aktivierung, Erstellung und Ablaufdaten

* Status der Zertifikatsperre

* Schlüsselbezeichner (eine GUID)

::: moniker range=">= aspnetcore-2.0"

Darüber hinaus `IKey` macht eine `CreateEncryptor` Methode, die verwendet werden können, erstellen eine [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) Instanz gebunden, der diesem Schlüssel.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Darüber hinaus `IKey` macht eine `CreateEncryptorInstance` Methode, die verwendet werden können, erstellen eine [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) Instanz gebunden, der diesem Schlüssel.

::: moniker-end

> [!NOTE]
> Es gibt keine API zum Abrufen der unformatierten kryptografischen Materials aus einem `IKey` Instanz.

## <a name="ikeymanager"></a>IKeyManager

Die `IKeyManager` -Schnittstelle stellt ein Objekt, das für die allgemeine Schlüsselspeicher, abrufen und Manipulation verantwortlich. Sie macht drei allgemeine Vorgänge verfügbar:

* Erstellen Sie einen neuen Schlüssel, und speichern Sie die Daten in den Speicher.

* Alle Schlüssel aus dem Speicher abrufen.

* Einen oder mehrere Schlüssel widerrufen, und die Sperrinformationen in den Speicher beizubehalten.

>[!WARNING]
> Schreiben einer `IKeyManager` ist eine sehr komplexe Aufgabe, und die meisten Entwickler sollten nicht versuchen, ihn. Stattdessen die meisten Entwickler sollten profitieren Sie von den Funktionen von Angeboten die [XmlKeyManager](#xmlkeymanager) Klasse.

## <a name="xmlkeymanager"></a>XmlKeyManager

Die `XmlKeyManager` Typ ist die integrierte konkrete Implementierung der `IKeyManager`. Es bietet mehrere nützliche Funktionen, einschließlich schlüsselhinterlegung und Verschlüsselung von Schlüsseln im ruhenden Zustand. Schlüssel in diesem System als XML-Elemente dargestellt werden (insbesondere ["XElement"](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)).

`XmlKeyManager` hängt davon ab, auf mehrere andere Komponenten, die im Verlauf seiner Aufgaben ausführen:

::: moniker range=">= aspnetcore-2.0"

* `AlgorithmConfiguration`, die festlegen, dass der Algorithmen, die durch neue Schlüssel verwendet.

* `IXmlRepository`, welche Steuerelemente, wo der Schlüssel im Speicher gespeichert sind.

* `IXmlEncryptor` [optional], wodurch Schlüsseln im ruhenden Zustand verschlüsselt.

* `IKeyEscrowSink` [optional] die schlüsselhinterlegung Dienste bereitstellt.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* `IXmlRepository`, welche Steuerelemente, wo der Schlüssel im Speicher gespeichert sind.

* `IXmlEncryptor` [optional], wodurch Schlüsseln im ruhenden Zustand verschlüsselt.

* `IKeyEscrowSink` [optional] die schlüsselhinterlegung Dienste bereitstellt.

::: moniker-end

Im folgenden finden Sie übersichtliche Diagramme, die angeben, wie diese Komponenten innerhalb von miteinander verknüpft sind `XmlKeyManager`.

::: moniker range=">= aspnetcore-2.0"

![Schlüssel erstellen](key-management/_static/keycreation2.png)

*Schlüssel erstellen / CreateNewKey*

In der Implementierung von `CreateNewKey`, `AlgorithmConfiguration` Komponente dient zum Erstellen Sie einen eindeutigen `IAuthenticatedEncryptorDescriptor`, die dann als XML serialisiert wird. Wenn eine Senke schlüsselhinterlegung vorhanden ist, wird der XML-Rohdaten (unverschlüsselt) für die langfristige Speicherung an die Senke bereitgestellt. Der nicht verschlüsselte XML-Code wird ausgeführt, bis ein `IXmlEncryptor` (falls erforderlich) das verschlüsselte XML-Dokument zu generieren. Dieses verschlüsselte Dokument werden beibehalten, in den langfristigen Speicher über die `IXmlRepository`. (Wenn kein `IXmlEncryptor` wird konfiguriert, wird das Dokument nicht verschlüsselte in beibehalten der `IXmlRepository`.)

![Abrufen eines Schlüssels](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![Schlüssel erstellen](key-management/_static/keycreation1.png)

*Schlüssel erstellen / CreateNewKey*

In der Implementierung von `CreateNewKey`, `IAuthenticatedEncryptorConfiguration` Komponente dient zum Erstellen Sie einen eindeutigen `IAuthenticatedEncryptorDescriptor`, die dann als XML serialisiert wird. Wenn eine Senke schlüsselhinterlegung vorhanden ist, wird der XML-Rohdaten (unverschlüsselt) für die langfristige Speicherung an die Senke bereitgestellt. Der nicht verschlüsselte XML-Code wird ausgeführt, bis ein `IXmlEncryptor` (falls erforderlich) das verschlüsselte XML-Dokument zu generieren. Dieses verschlüsselte Dokument werden beibehalten, in den langfristigen Speicher über die `IXmlRepository`. (Wenn kein `IXmlEncryptor` wird konfiguriert, wird das Dokument nicht verschlüsselte in beibehalten der `IXmlRepository`.)

![Abrufen eines Schlüssels](key-management/_static/keyretrieval1.png)

::: moniker-end

*Abrufen von Schlüssel / GetAllKeys*

In der Implementierung von `GetAllKeys`, die XML-Dokumente darstellen, Schlüssel und Widerrufe werden gelesen, aus der zugrunde liegenden `IXmlRepository`. Wenn diese Dokumente verschlüsselt sind, wird das System diese automatisch entschlüsselt. `XmlKeyManager` erstellt den entsprechenden `IAuthenticatedEncryptorDescriptorDeserializer` Instanzen, die Dokumente zu deserialisieren, wieder in das `IAuthenticatedEncryptorDescriptor` -Instanzen, die dann in einzelnen umschlossen werden `IKey` Instanzen. Diese Sammlung von `IKey` Instanzen an den Aufrufer zurückgegeben wird.

Weitere Informationen zu bestimmten XML-Elemente befinden sich die [Schlüsselspeicher Format Dokument](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).

## <a name="ixmlrepository"></a>IXmlRepository

Die `IXmlRepository` Schnittstelle darstellt, einen Typ, der XML-Code beibehalten und Abrufen von XML aus einem Sicherungsspeicher kann. Es macht zwei APIs verfügbar:

* `GetAllElements` :`IReadOnlyCollection<XElement>`

* `StoreElement(XElement element, string friendlyName)`

Implementierungen von `IXmlRepository` keine zum Analysieren des XML-Code übergeben werden müssen. Sie sollten die XML-Dokumente als nicht transparent behandeln, die höhere Schichten kümmern, generieren und die Dokumente verarbeiten.

Es gibt vier integrierte konkrete Typen die implementieren `IXmlRepository`:

::: moniker range=">= aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage.AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [FileSystemXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [RegistryXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [AzureStorage.AzureBlobXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [RedisXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

Finden Sie unter den [softwareschlüsselspeicher-Anbieter Dokument](xref:security/data-protection/implementation/key-storage-providers) für Weitere Informationen.

Registrieren ein benutzerdefinierten `IXmlRepository` ist geeignet, wenn Sie einen einzelne Sicherungsspeicher (z. B. Azure Table Storage) verwenden.

Um das standardrepository anwendungsweite ändern möchten, registrieren Sie eine benutzerdefinierte `IXmlRepository` Instanz:

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

## <a name="ixmlencryptor"></a>IXmlEncryptor

Die `IXmlEncryptor` Schnittstelle darstellt, einen Typ, der einem nur-Text-XML-Element verschlüsseln kann. Sie macht eine einzelne API verfügbar:

* Encrypt(XElement plaintextElement): EncryptedXmlInfo

Wenn eine serialisierte `IAuthenticatedEncryptorDescriptor` enthält alle Elemente, die als "erfordert eine Verschlüsselung", klicken Sie dann `XmlKeyManager` führt diese Elemente über dem konfigurierten `IXmlEncryptor`des `Encrypt` -Methode, und es bleiben dazu Elements anstelle der Nur-Text-Element, um die `IXmlRepository`. Die Ausgabe der `Encrypt` Methode ist ein `EncryptedXmlInfo` Objekt. Dieses Objekt ist ein Wrapper enthält sowohl die resultierenden dazu `XElement` und den Typ steht für ein `IXmlDecryptor` die verwendet werden können, um das entsprechende Element zu entschlüsseln.

Es gibt vier integrierte konkrete Typen die implementieren `IXmlEncryptor`:

* [CertificateXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [DpapiNGXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [DpapiXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [NullXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

Finden Sie unter den [Schlüsselverschlüsselung auf Rest-Dokument](xref:security/data-protection/implementation/key-encryption-at-rest) für Weitere Informationen.

Um den Key-Verschlüsselung im Ruhezustand Standardmechanismus anwendungsweite ändern möchten, registrieren Sie eine benutzerdefinierte `IXmlEncryptor` Instanz:

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

## <a name="ixmldecryptor"></a>IXmlDecryptor

Die `IXmlDecryptor` Schnittstelle darstellt, einen Typ, der weiß, wie zum Entschlüsseln einer `XElement` , wurde dazu über einen `IXmlEncryptor`. Sie macht eine einzelne API verfügbar:

* Entschlüsseln Sie ("XElement" EncryptedElement): "XElement"

Die `Decrypt` Methode macht die Verschlüsselung von `IXmlEncryptor.Encrypt`. Im Allgemeinen jede konkrete `IXmlEncryptor` Implementierung weist einen entsprechenden konkreten `IXmlDecryptor` Implementierung.

Typen der implementieren `IXmlDecryptor` sollte einen der folgenden zwei öffentliche Konstruktoren aufweisen:

* .ctor(IServiceProvider)
* .ctor()

> [!NOTE]
> Die `IServiceProvider` , die an der Konstruktor kann null sein.

## <a name="ikeyescrowsink"></a>IKeyEscrowSink

Die `IKeyEscrowSink` Schnittstelle darstellt, einen Typ, der hinterlegter vertraulicher Informationen führen kann. Denken Sie daran, dass serialisierte Deskriptoren möglicherweise vertrauliche Informationen (z. B. kryptografischem Material enthalten), und dies ist wie der Einführung von geführt hat die [IXmlEncryptor](#ixmlencryptor) im vornherein geben. Allerdings Unfälle passieren, und Schlüssel Ringe können gelöscht werden, oder beschädigt.

Die hinterlegter-Schnittstelle bietet ein Notfall escapehatch, für die Gewährung des Zugriffs auf die serialisierte XML-Rohdaten, bevor sie durch alle konfigurierten transformiert wird [IXmlEncryptor](#ixmlencryptor). Die Schnittstelle macht eine einzelne API verfügbar:

* Store (Guid-Schlüssel-ID, "XElement"-Element)

Es liegt die `IKeyEscrowSink` Implementierung, die das angegebene Element auf sichere Weise konsistent mit der Business-Richtlinie zu verarbeiten. Eine mögliche Implementierung könnte für die Senke hinterlegter zum Verschlüsseln von XML-Elements mit einem bekannten Unternehmen x. 509-Zertifikat sein, in dem der private Zertifikatschlüssel hinterlegt ist. die `CertificateXmlEncryptor` Typ dabei helfen kann. Die `IKeyEscrowSink` Implementierung ist auch zuständig für das bereitgestellte Element entsprechend beibehalten.

Keinen hinterlegter-Mechanismus ist standardmäßig aktiviert, wenn der Server-Administratoren können [Global konfigurieren](xref:security/data-protection/configuration/machine-wide-policy). Sie können auch programmgesteuert programmiert werden über die `IDataProtectionBuilder.AddKeyEscrowSink` -Methode wie im folgenden Beispiel gezeigt. Die `AddKeyEscrowSink` Methode Überladungen Spiegel der `IServiceCollection.AddSingleton` und `IServiceCollection.AddInstance` Überladungen als `IKeyEscrowSink` Instanzen Singletons werden sollen. Wenn mehrere `IKeyEscrowSink` Instanzen registriert sind, jeweils wird während der Generierung von Schlüsseln, aufgerufen werden, damit der Schlüssel gleichzeitig an mehrere Mechanismen hinterlegt werden können.

Es gibt keine API zum Lesen von Material aus einem `IKeyEscrowSink` Instanz. Dies ist konsistent mit der Theorie zum Entwurf des Mechanismus hinterlegter: Dieser Vorgang soll das Schlüsselmaterial zu einer vertrauenswürdigen Stelle zugänglich zu machen, und da die Anwendung selbst keiner vertrauenswürdigen Zertifizierungsstelle ist, er keinen Zugriff haben sollten eigene hinterlegte Material.

Der folgende Beispielcode veranschaulicht das Erstellen und Registrieren einer `IKeyEscrowSink` , in denen Schlüssel hinterlegt werden, dass nur Mitglieder der "CONTOSODomain-Admins" wiederhergestellt werden können.

> [!NOTE]
> Um dieses Beispiel ausführen zu können, muss auf eine Domäne eingebundenen Windows 8 / Windows Server 2012-Computer und dem Domänencontroller muss WindowsServer 2012 oder höher sein.

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
