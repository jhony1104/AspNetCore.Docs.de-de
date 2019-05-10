---
title: Verschlüsselung ruhender Daten in ASP.NET Core
author: rick-anderson
description: Erfahren Sie Details zur Implementierung von ASP.NET Core-Datenschutz-Key-Verschlüsselung ruhender Daten.
ms.author: riande
ms.date: 07/16/2018
uid: security/data-protection/implementation/key-encryption-at-rest
ms.openlocfilehash: 52c3137dbe467096364b42430c92aecc7c15e313
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892307"
---
# <a name="key-encryption-at-rest-in-aspnet-core"></a>Verschlüsselung ruhender Daten in ASP.NET Core

System zum Schutz von Daten [Discovery-Mechanismus in der Standardeinstellung setzt](xref:security/data-protection/configuration/default-settings) um zu bestimmen, wie kryptografische Schlüssel im Ruhezustand verschlüsselt werden soll. Der Entwickler kann außer Kraft setzen der Discovery-Mechanismus und manuell angeben, wie die Schlüssel im Ruhezustand verschlüsselt werden soll.

> [!WARNING]
> Wenn Sie einen expliziten angeben [Schlüssel persistenzspeicherort](xref:security/data-protection/implementation/key-storage-providers), System zum Schutz von Daten hebt die Registrierung der Standard-Schlüsselverschlüsselung auf Rest-Mechanismus. Daher werden die Schlüssel nicht mehr im Ruhezustand verschlüsselt. Es wird empfohlen, die Sie [Geben Sie einen Mechanismus für die explizite Schlüsselverschlüsselung](xref:security/data-protection/implementation/key-encryption-at-rest) für produktionsbereitstellungen. Die Verschlüsselung im Ruhezustand Methode-Optionen werden in diesem Thema beschrieben.

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a>Azure Key Vault

Zum Speichern von Schlüsseln in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), konfigurieren Sie das System mit [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in die `Startup` Klasse:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

Weitere Informationen finden Sie unter [Konfigurieren von ASP.NET Core-Datenschutz: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).

::: moniker-end

## <a name="windows-dpapi"></a>Windows DPAPI

**Gilt nur für Windows-Bereitstellungen.**

Wenn Windows DPAPI verwendet wird, wird mit Schlüsselmaterial verschlüsselt [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) vor, die in den Speicher beibehalten wird. DPAPI ist einem entsprechenden Verschlüsselungsmechanismus für Daten, die niemals außerhalb des aktuellen Computers gelesen werden (allerdings es ist möglich, diese Schlüssel bis zum Active Directory sichern, finden Sie unter [DPAPI und servergespeicherte Profile](https://support.microsoft.com/kb/309408/#6)). Um DPAPI-Schlüssel im Ruhezustand-Verschlüsselung zu konfigurieren, rufen Sie eine der der [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) Erweiterungsmethoden:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

Wenn `ProtectKeysWithDpapi` mit ohne Parameter aufgerufen wird, nur das aktuelle Windows-Benutzerkonto den beibehaltenen Schlüsselbund entschlüsseln kann. Sie können optional angeben, dass jedes Benutzerkonto auf dem Computer (nicht nur das aktuelle Benutzerkonto) auf den Schlüsselbund zu entschlüsseln:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // All user accounts on the machine can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi(protectToLocalMachine: true);
}
```

::: moniker range=">= aspnetcore-2.0"

## <a name="x509-certificate"></a>X.509-Zertifikat

Wenn die app über mehrere Computer verteilt ist, ist es möglicherweise sinnvoll, ein gemeinsam genutztes x. 509-Zertifikat auf dem Computer zu verteilen, und konfigurieren Sie die gehosteten apps zur Verwendung des Zertifikats für die Verschlüsselung von Schlüsseln im ruhenden Zustand:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

Aufgrund von Beschränkungen im .NET Framework werden nur Zertifikate mit privaten Schlüsseln CAPI unterstützt. Finden Sie unter den unten stehenden Inhalt mögliche problemumgehungen für diese Einschränkungen.

::: moniker-end

## <a name="windows-dpapi-ng"></a>Windows DPAPI-NG

**Dieser Mechanismus ist nur für Windows 8/Windows Server 2012 oder höher verfügbar.**

Ab Windows 8 unterstützt Windows-Betriebssystem DPAPI-NG (auch als CNG DPAPI). Weitere Informationen finden Sie unter [zu CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi).

Der Prinzipal wird als eine Regel zum Schutz von Deskriptor codiert. Im folgenden Beispiel, das Aufrufe [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping)ausschließlich in die Domäne eingebundener Benutzer mit der angegebenen SID den Schlüsselbund entschlüsselt werden kann:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

Es gibt auch eine parameterlose Überladung von `ProtectKeysWithDpapiNG`. Diese Hilfsmethode verwenden, an die Regel "SID = {CURRENT_ACCOUNT_SID}", wobei *CURRENT_ACCOUNT_SID* ist die SID des die aktuelle Windows-Benutzerkonto:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

In diesem Szenario ist die Active Directory-Domänencontroller für die Verteilung von den DPAPI-NG-Vorgängen verwendeten Verschlüsselungsschlüssel verantwortlich. Der Zielbenutzer kann die verschlüsselte Nutzlast über eine beliebige Domäne eingebundenen Computer entschlüsselt werden, (vorausgesetzt, dass der Prozess unter ihrer Identität ausgeführt wird).

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a>Einem zertifikatbasierten Verschlüsselungsverfahren mit Windows DPAPI-NG

Wenn die app unter Windows 8.1 / Windows Server 2012 R2 ausgeführt wird, oder höher, Sie Windows DPAPI-NG verwenden können, um zertifikatbasierte Verschlüsselung auszuführen. Verwenden Sie die Regel sicherheitsbeschreibungs-Zeichenfolge "Zertifikat HashId:THUMBPRINT =", wobei *FINGERABDRUCK* ist der Hexadezimal-codierten SHA1-Fingerabdruck des Zertifikats:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

Jede app, die auf dieses Repository verwiesen wird, muss unter Windows 8.1 / Windows Server 2012 R2 oder höher, auf die Schlüssel zu entschlüsseln ausgeführt werden.

## <a name="custom-key-encryption"></a>Benutzerdefinierte Schlüsselverschlüsselung

Wenn die integrierte Mechanismen nicht geeignet sind, kann der Entwickler einen eigenen Mechanismus für die Schlüsselverschlüsselung angeben, durch Bereitstellen eines benutzerdefinierten [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor).
