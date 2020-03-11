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
# <a name="key-management-extensibility-in-aspnet-core"></a>Schlüsselverwaltungserweiterbarkeit in ASP.NET Core

> [!TIP]
> Lesen Sie den Abschnitt [Key Management](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) vor dem Lesen dieses Abschnitts, da hier einige der grundlegenden Konzepte hinter diesen APIs erläutert werden.

> [!WARNING]
> Typen, die die folgenden Schnittstellen implementieren, sollten threadsicher werden für mehrere Aufrufer.

## <a name="key"></a>Key

Die `IKey`-Schnittstelle ist die grundlegende Darstellung eines Schlüssels in Cryptosystem. Der Begriff-Schlüssel ist hier in der abstrakten Sinne, nicht im literal Sinne "kryptografische Schlüsseldaten" verwendet. Ein Schlüssel hat die folgenden Eigenschaften:

* Aktivierung, Erstellung und Ablaufdaten

* Sperrstatus

* Schlüsselbezeichner (eine GUID)

::: moniker range=">= aspnetcore-2.0"

Darüber hinaus stellt `IKey` eine `CreateEncryptor`-Methode zur Verfügung, die zum Erstellen einer [iauthenticatedencryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) -Instanz verwendet werden kann, die mit diesem Schlüssel verknüpft ist.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Darüber hinaus stellt `IKey` eine `CreateEncryptorInstance`-Methode zur Verfügung, die zum Erstellen einer [iauthenticatedencryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) -Instanz verwendet werden kann, die mit diesem Schlüssel verknüpft ist.

::: moniker-end

> [!NOTE]
> Es gibt keine API, um das unformatierte kryptografiematerial aus einer `IKey` Instanz abzurufen.

## <a name="ikeymanager"></a>IKeyManager

Die `IKeyManager`-Schnittstelle stellt ein Objekt dar, das für das Speichern, abrufen und manipulieren allgemeiner Schlüssel zuständig ist. Sie macht drei allgemeine Vorgänge verfügbar:

* Erstellen Sie einen neuen Schlüssel, und speichern Sie die Daten in den Speicher.

* Alle Schlüssel aus dem Speicher abrufen.

* Einen oder mehrere Schlüssel widerrufen, und die Sperrinformationen in den Speicher beizubehalten.

>[!WARNING]
> Das Schreiben einer `IKeyManager` ist eine sehr fortschrittliche Aufgabe, und die Mehrzahl der Entwickler sollten Sie nicht versuchen. Stattdessen sollten die meisten Entwickler die Funktionen nutzen, die von der [xmlkeymanager](#xmlkeymanager) -Klasse angeboten werden.

## <a name="xmlkeymanager"></a>XmlKeyManager

Der `XmlKeyManager` Typ ist die in-Box konkrete Implementierung von `IKeyManager`. Es bietet mehrere nützliche Funktionen, einschließlich schlüsselhinterlegung und Verschlüsselung von Schlüsseln im ruhenden Zustand. Schlüssel in diesem System werden als XML-Elemente (insbesondere [XElement](/dotnet/csharp/programming-guide/concepts/linq/xelement-class-overview)) dargestellt.

`XmlKeyManager` hängt von mehreren anderen Komponenten im Rahmen der Erfüllung der Aufgaben ab:

::: moniker range=">= aspnetcore-2.0"

* `AlgorithmConfiguration`, das die von neuen Schlüsseln verwendeten Algorithmen bestimmt.

* `IXmlRepository`, der steuert, wo Schlüssel im Speicher gespeichert werden.

* `IXmlEncryptor` [optional], das das Verschlüsseln von Schlüsseln im Ruhezustand ermöglicht.

* `IKeyEscrowSink` [optional], das Schlüssel Hinterlegungs Dienste bereitstellt.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* `IXmlRepository`, der steuert, wo Schlüssel im Speicher gespeichert werden.

* `IXmlEncryptor` [optional], das das Verschlüsseln von Schlüsseln im Ruhezustand ermöglicht.

* `IKeyEscrowSink` [optional], das Schlüssel Hinterlegungs Dienste bereitstellt.

::: moniker-end

Im folgenden finden Sie allgemeine Diagramme, die angeben, wie diese Komponenten innerhalb `XmlKeyManager`miteinander verbunden werden.

::: moniker range=">= aspnetcore-2.0"

![Schlüssel erstellen](key-management/_static/keycreation2.png)

*Key Creation/kreatenewkey*

In der Implementierung von `CreateNewKey`wird die `AlgorithmConfiguration` Komponente verwendet, um eine eindeutige `IAuthenticatedEncryptorDescriptor`zu erstellen, die dann als XML serialisiert wird. Wenn eine Senke schlüsselhinterlegung vorhanden ist, wird der XML-Rohdaten (unverschlüsselt) für die langfristige Speicherung an die Senke bereitgestellt. Der unverschlüsselte XML-Code wird dann durch eine `IXmlEncryptor` (falls erforderlich) ausgeführt, um das verschlüsselte XML-Dokument zu generieren. Dieses verschlüsselte Dokument wird in der langfristigen Speicherung über die `IXmlRepository`persistent gespeichert. (Wenn keine `IXmlEncryptor` konfiguriert ist, wird das unverschlüsselte Dokument dauerhaft in der `IXmlRepository`gespeichert.)

![Abrufen eines Schlüssels](key-management/_static/keyretrieval2.png)

::: moniker-end

::: moniker range="< aspnetcore-2.0"

![Schlüssel erstellen](key-management/_static/keycreation1.png)

*Key Creation/kreatenewkey*

In der Implementierung von `CreateNewKey`wird die `IAuthenticatedEncryptorConfiguration` Komponente verwendet, um eine eindeutige `IAuthenticatedEncryptorDescriptor`zu erstellen, die dann als XML serialisiert wird. Wenn eine Senke schlüsselhinterlegung vorhanden ist, wird der XML-Rohdaten (unverschlüsselt) für die langfristige Speicherung an die Senke bereitgestellt. Der unverschlüsselte XML-Code wird dann durch eine `IXmlEncryptor` (falls erforderlich) ausgeführt, um das verschlüsselte XML-Dokument zu generieren. Dieses verschlüsselte Dokument wird in der langfristigen Speicherung über die `IXmlRepository`persistent gespeichert. (Wenn keine `IXmlEncryptor` konfiguriert ist, wird das unverschlüsselte Dokument dauerhaft in der `IXmlRepository`gespeichert.)

![Abrufen eines Schlüssels](key-management/_static/keyretrieval1.png)

::: moniker-end

*Schlüssel Abruf/getallkeys*

Bei der Implementierung von `GetAllKeys`werden die XML-Dokumente, die Schlüssel und widerrufen darstellen, aus der zugrunde liegenden `IXmlRepository`gelesen. Wenn diese Dokumente verschlüsselt sind, wird das System diese automatisch entschlüsselt. `XmlKeyManager` erstellt die entsprechenden `IAuthenticatedEncryptorDescriptorDeserializer` Instanzen, um die Dokumente wieder in `IAuthenticatedEncryptorDescriptor` Instanzen zu deserialisieren, die anschließend in einzelne `IKey` Instanzen umschließt werden. Diese Auflistung von `IKey` Instanzen wird an den Aufrufer zurückgegeben.

Weitere Informationen zu den einzelnen XML-Elementen finden Sie im [Dokument Schlüsselspeicher Format](xref:security/data-protection/implementation/key-storage-format#data-protection-implementation-key-storage-format).

## <a name="ixmlrepository"></a>IXmlRepository

Die `IXmlRepository`-Schnittstelle stellt einen Typ dar, der XML-Daten dauerhaft speichern und aus einem Sicherungs Speicher abrufen kann. Es macht zwei APIs verfügbar:

* `GetAllElements`:`IReadOnlyCollection<XElement>`

* `StoreElement(XElement element, string friendlyName)`

Implementierungen von `IXmlRepository` müssen den XML-Code nicht analysieren. Sie sollten die XML-Dokumente als nicht transparent behandeln, die höhere Schichten kümmern, generieren und die Dokumente verarbeiten.

Es gibt vier integrierte, konkrete Typen, die `IXmlRepository`implementieren:

::: moniker range=">= aspnetcore-2.2"

* [Filesystemxmlrepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [Registryxmlrepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [Azurestorage. azureblobxmlrepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [Redisxmlrepository](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredis.redisxmlrepository)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [Filesystemxmlrepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.filesystemxmlrepository)
* [Registryxmlrepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository)
* [Azurestorage. azureblobxmlrepository](/dotnet/api/microsoft.aspnetcore.dataprotection.azurestorage.azureblobxmlrepository)
* [Redisxmlrepository](/dotnet/api/microsoft.aspnetcore.dataprotection.redisxmlrepository)

::: moniker-end

Weitere Informationen finden Sie im [Dokument Schlüsselspeicher Anbieter](xref:security/data-protection/implementation/key-storage-providers) .

Die Registrierung eines benutzerdefinierten `IXmlRepository` ist geeignet, wenn ein anderer Sicherungs Speicher (z. b. Azure Table Storage) verwendet wird.

Wenn Sie das standardrepository Anwendungs weit ändern möchten, registrieren Sie eine benutzerdefinierte `IXmlRepository` Instanz:

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

Die `IXmlEncryptor`-Schnittstelle stellt einen Typ dar, der ein Klartext-XML-Element verschlüsseln kann. Sie macht eine einzelne API verfügbar:

* ("XElement" PlaintextElement) zu verschlüsseln: EncryptedXmlInfo

Wenn ein serialisiertes `IAuthenticatedEncryptorDescriptor` Elemente enthält, die als "erfordert Verschlüsselung" gekennzeichnet sind, werden diese Elemente von `XmlKeyManager` über die `Encrypt`-Methode des konfigurierten `IXmlEncryptor`ausgeführt, und es wird das enchiffrierte Element anstelle des Klartext-Elements in den `IXmlRepository`persistent gespeichert. Die Ausgabe der `Encrypt`-Methode ist ein `EncryptedXmlInfo` Objekt. Bei diesem Objekt handelt es sich um einen Wrapper, der sowohl das resultierende `XElement` als auch den Typ enthält, der ein `IXmlDecryptor` darstellt, mit dem das entsprechende Element entschlüsselt werden kann.

Es gibt vier integrierte, konkrete Typen, die `IXmlEncryptor`implementieren:

* [Certifi-exmlencryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.certificatexmlencryptor)
* [Dpapingxmlencryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapingxmlencryptor)
* [Dpapixmlencryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.dpapixmlencryptor)
* [Nullxmlencryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.nullxmlencryptor)

Weitere Informationen finden Sie im [Dokument Key Encryption at Rest](xref:security/data-protection/implementation/key-encryption-at-rest) .

Registrieren Sie eine benutzerdefinierte `IXmlEncryptor` Instanz, um den standardmäßigen Rest-Mechanismus für die Schlüssel Verschlüsselung zu ändern:

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

Die `IXmlDecryptor`-Schnittstelle stellt einen Typ dar, der weiß, wie ein `XElement` entschlüsselt wird, das über einen `IXmlEncryptor`verschlüsselt wurde. Sie macht eine einzelne API verfügbar:

* Entschlüsseln ("XElement" EncryptedElement): "XElement"

Die `Decrypt`-Methode macht die von `IXmlEncryptor.Encrypt`ausgeführte Verschlüsselung nicht mehr. Im allgemeinen verfügt jede konkrete `IXmlEncryptor` Implementierung über eine entsprechende konkrete `IXmlDecryptor` Implementierung.

Typen, die `IXmlDecryptor` implementieren, sollten einen der folgenden zwei öffentlichen Konstruktoren aufweisen:

* .ctor(IServiceProvider)
* .ctor()

> [!NOTE]
> Die an den Konstruktor übergeben `IServiceProvider` kann NULL sein.

## <a name="ikeyescrowsink"></a>IKeyEscrowSink

Die `IKeyEscrowSink`-Schnittstelle stellt einen Typ dar, der die Hinterlegung vertraulicher Informationen durchführen kann. Beachten Sie, dass serialisierte Deskriptoren vertrauliche Informationen (z. b. kryptografische Materialien) enthalten können. Dies hat zur Einführung des Typs [ixmlencryptor](#ixmlencryptor) geführt. Allerdings Unfälle passieren, und Schlüssel Ringe können gelöscht werden, oder beschädigt.

Die Hinterlegungs Schnittstelle stellt eine Notfall-escapeschraffur dar und ermöglicht den Zugriff auf die unformatierte serialisierte XML, bevor Sie von einem konfigurierten [ixmlencryptor](#ixmlencryptor)transformiert wird. Die Schnittstelle macht eine einzelne API verfügbar:

* Store (Guid-Schlüssel-ID, "XElement"-Element)

Es liegt an der `IKeyEscrowSink` Implementierung, das bereitgestellte Element auf sichere Weise in Übereinstimmung mit der Geschäfts Richtlinie zu verarbeiten. Eine mögliche Implementierung könnte sein, dass die hinterlegte Senke das XML-Element mit einem bekannten Unternehmens-X. 509-Zertifikat verschlüsselt, bei dem der private Schlüssel des Zertifikats verwendet wurde. der `CertificateXmlEncryptor`-Typ kann dies unterstützen. Die `IKeyEscrowSink`-Implementierung ist auch dafür verantwortlich, das bereitgestellte Element entsprechend beizubehalten.

Standardmäßig ist kein Hinterlegungs Mechanismus aktiviert, aber Server Administratoren können [Dies Global konfigurieren](xref:security/data-protection/configuration/machine-wide-policy). Sie kann auch Programm gesteuert über die `IDataProtectionBuilder.AddKeyEscrowSink`-Methode konfiguriert werden, wie im folgenden Beispiel gezeigt. Die `AddKeyEscrowSink`-Methoden Überladungen spiegeln die `IServiceCollection.AddSingleton` und `IServiceCollection.AddInstance` Überladungen wider, da `IKeyEscrowSink` Instanzen als Singletons gedacht sind. Wenn mehrere `IKeyEscrowSink` Instanzen registriert sind, werden diese während der Schlüsselgenerierung aufgerufen, sodass Schlüssel gleichzeitig an mehrere Mechanismen über gestellt werden können.

Es gibt keine API zum Lesen von Material aus einer `IKeyEscrowSink`-Instanz. Dies ist konsistent mit der Theorie zum Entwurf des Mechanismus hinterlegter: Dieser Vorgang soll das Schlüsselmaterial zu einer vertrauenswürdigen Stelle zugänglich zu machen, und da die Anwendung selbst keiner vertrauenswürdigen Zertifizierungsstelle ist, er keinen Zugriff haben sollten eigene hinterlegte Material.

Der folgende Beispielcode veranschaulicht das Erstellen und Registrieren einer `IKeyEscrowSink`, bei der Schlüssel eingebesdert werden, sodass nur Mitglieder von "condesodomain Admins" diese wiederherstellen können.

> [!NOTE]
> Um dieses Beispiel ausführen zu können, muss auf eine Domäne eingebundenen Windows 8 / Windows Server 2012-Computer und dem Domänencontroller muss WindowsServer 2012 oder höher sein.

[!code-csharp[](key-management/samples/key-management-extensibility.cs)]
