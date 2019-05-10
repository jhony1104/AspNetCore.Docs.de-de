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
# <a name="key-encryption-at-rest-in-aspnet-core"></a><span data-ttu-id="3033a-103">Verschlüsselung ruhender Daten in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3033a-103">Key encryption at rest in ASP.NET Core</span></span>

<span data-ttu-id="3033a-104">System zum Schutz von Daten [Discovery-Mechanismus in der Standardeinstellung setzt](xref:security/data-protection/configuration/default-settings) um zu bestimmen, wie kryptografische Schlüssel im Ruhezustand verschlüsselt werden soll.</span><span class="sxs-lookup"><span data-stu-id="3033a-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine how cryptographic keys should be encrypted at rest.</span></span> <span data-ttu-id="3033a-105">Der Entwickler kann außer Kraft setzen der Discovery-Mechanismus und manuell angeben, wie die Schlüssel im Ruhezustand verschlüsselt werden soll.</span><span class="sxs-lookup"><span data-stu-id="3033a-105">The developer can override the discovery mechanism and manually specify how keys should be encrypted at rest.</span></span>

> [!WARNING]
> <span data-ttu-id="3033a-106">Wenn Sie einen expliziten angeben [Schlüssel persistenzspeicherort](xref:security/data-protection/implementation/key-storage-providers), System zum Schutz von Daten hebt die Registrierung der Standard-Schlüsselverschlüsselung auf Rest-Mechanismus.</span><span class="sxs-lookup"><span data-stu-id="3033a-106">If you specify an explicit [key persistence location](xref:security/data-protection/implementation/key-storage-providers), the data protection system deregisters the default key encryption at rest mechanism.</span></span> <span data-ttu-id="3033a-107">Daher werden die Schlüssel nicht mehr im Ruhezustand verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="3033a-107">Consequently, keys are no longer encrypted at rest.</span></span> <span data-ttu-id="3033a-108">Es wird empfohlen, die Sie [Geben Sie einen Mechanismus für die explizite Schlüsselverschlüsselung](xref:security/data-protection/implementation/key-encryption-at-rest) für produktionsbereitstellungen.</span><span class="sxs-lookup"><span data-stu-id="3033a-108">We recommend that you [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span> <span data-ttu-id="3033a-109">Die Verschlüsselung im Ruhezustand Methode-Optionen werden in diesem Thema beschrieben.</span><span class="sxs-lookup"><span data-stu-id="3033a-109">The encryption-at-rest mechanism options are described in this topic.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a><span data-ttu-id="3033a-110">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="3033a-110">Azure Key Vault</span></span>

<span data-ttu-id="3033a-111">Zum Speichern von Schlüsseln in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), konfigurieren Sie das System mit [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in die `Startup` Klasse:</span><span class="sxs-lookup"><span data-stu-id="3033a-111">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

<span data-ttu-id="3033a-112">Weitere Informationen finden Sie unter [Konfigurieren von ASP.NET Core-Datenschutz: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).</span><span class="sxs-lookup"><span data-stu-id="3033a-112">For more information, see [Configure ASP.NET Core Data Protection: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).</span></span>

::: moniker-end

## <a name="windows-dpapi"></a><span data-ttu-id="3033a-113">Windows DPAPI</span><span class="sxs-lookup"><span data-stu-id="3033a-113">Windows DPAPI</span></span>

<span data-ttu-id="3033a-114">**Gilt nur für Windows-Bereitstellungen.**</span><span class="sxs-lookup"><span data-stu-id="3033a-114">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="3033a-115">Wenn Windows DPAPI verwendet wird, wird mit Schlüsselmaterial verschlüsselt [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) vor, die in den Speicher beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="3033a-115">When Windows DPAPI is used, key material is encrypted with [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) before being persisted to storage.</span></span> <span data-ttu-id="3033a-116">DPAPI ist einem entsprechenden Verschlüsselungsmechanismus für Daten, die niemals außerhalb des aktuellen Computers gelesen werden (allerdings es ist möglich, diese Schlüssel bis zum Active Directory sichern, finden Sie unter [DPAPI und servergespeicherte Profile](https://support.microsoft.com/kb/309408/#6)).</span><span class="sxs-lookup"><span data-stu-id="3033a-116">DPAPI is an appropriate encryption mechanism for data that's never read outside of the current machine (though it's possible to back these keys up to Active Directory; see [DPAPI and Roaming Profiles](https://support.microsoft.com/kb/309408/#6)).</span></span> <span data-ttu-id="3033a-117">Um DPAPI-Schlüssel im Ruhezustand-Verschlüsselung zu konfigurieren, rufen Sie eine der der [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) Erweiterungsmethoden:</span><span class="sxs-lookup"><span data-stu-id="3033a-117">To configure DPAPI key-at-rest encryption, call one of the [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) extension methods:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

<span data-ttu-id="3033a-118">Wenn `ProtectKeysWithDpapi` mit ohne Parameter aufgerufen wird, nur das aktuelle Windows-Benutzerkonto den beibehaltenen Schlüsselbund entschlüsseln kann.</span><span class="sxs-lookup"><span data-stu-id="3033a-118">If `ProtectKeysWithDpapi` is called with no parameters, only the current Windows user account can decipher the persisted key ring.</span></span> <span data-ttu-id="3033a-119">Sie können optional angeben, dass jedes Benutzerkonto auf dem Computer (nicht nur das aktuelle Benutzerkonto) auf den Schlüsselbund zu entschlüsseln:</span><span class="sxs-lookup"><span data-stu-id="3033a-119">You can optionally specify that any user account on the machine (not just the current user account) be able to decipher the key ring:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // All user accounts on the machine can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi(protectToLocalMachine: true);
}
```

::: moniker range=">= aspnetcore-2.0"

## <a name="x509-certificate"></a><span data-ttu-id="3033a-120">X.509-Zertifikat</span><span class="sxs-lookup"><span data-stu-id="3033a-120">X.509 certificate</span></span>

<span data-ttu-id="3033a-121">Wenn die app über mehrere Computer verteilt ist, ist es möglicherweise sinnvoll, ein gemeinsam genutztes x. 509-Zertifikat auf dem Computer zu verteilen, und konfigurieren Sie die gehosteten apps zur Verwendung des Zertifikats für die Verschlüsselung von Schlüsseln im ruhenden Zustand:</span><span class="sxs-lookup"><span data-stu-id="3033a-121">If the app is spread across multiple machines, it may be convenient to distribute a shared X.509 certificate across the machines and configure the hosted apps to use the certificate for encryption of keys at rest:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

<span data-ttu-id="3033a-122">Aufgrund von Beschränkungen im .NET Framework werden nur Zertifikate mit privaten Schlüsseln CAPI unterstützt.</span><span class="sxs-lookup"><span data-stu-id="3033a-122">Due to .NET Framework limitations, only certificates with CAPI private keys are supported.</span></span> <span data-ttu-id="3033a-123">Finden Sie unter den unten stehenden Inhalt mögliche problemumgehungen für diese Einschränkungen.</span><span class="sxs-lookup"><span data-stu-id="3033a-123">See the content below for possible workarounds to these limitations.</span></span>

::: moniker-end

## <a name="windows-dpapi-ng"></a><span data-ttu-id="3033a-124">Windows DPAPI-NG</span><span class="sxs-lookup"><span data-stu-id="3033a-124">Windows DPAPI-NG</span></span>

<span data-ttu-id="3033a-125">**Dieser Mechanismus ist nur für Windows 8/Windows Server 2012 oder höher verfügbar.**</span><span class="sxs-lookup"><span data-stu-id="3033a-125">**This mechanism is available only on Windows 8/Windows Server 2012 or later.**</span></span>

<span data-ttu-id="3033a-126">Ab Windows 8 unterstützt Windows-Betriebssystem DPAPI-NG (auch als CNG DPAPI).</span><span class="sxs-lookup"><span data-stu-id="3033a-126">Beginning with Windows 8, Windows OS supports DPAPI-NG (also called CNG DPAPI).</span></span> <span data-ttu-id="3033a-127">Weitere Informationen finden Sie unter [zu CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi).</span><span class="sxs-lookup"><span data-stu-id="3033a-127">For more information, see [About CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi).</span></span>

<span data-ttu-id="3033a-128">Der Prinzipal wird als eine Regel zum Schutz von Deskriptor codiert.</span><span class="sxs-lookup"><span data-stu-id="3033a-128">The principal is encoded as a protection descriptor rule.</span></span> <span data-ttu-id="3033a-129">Im folgenden Beispiel, das Aufrufe [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping)ausschließlich in die Domäne eingebundener Benutzer mit der angegebenen SID den Schlüsselbund entschlüsselt werden kann:</span><span class="sxs-lookup"><span data-stu-id="3033a-129">In the following example that calls [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping), only the domain-joined user with the specified SID can decrypt the key ring:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

<span data-ttu-id="3033a-130">Es gibt auch eine parameterlose Überladung von `ProtectKeysWithDpapiNG`.</span><span class="sxs-lookup"><span data-stu-id="3033a-130">There's also a parameterless overload of `ProtectKeysWithDpapiNG`.</span></span> <span data-ttu-id="3033a-131">Diese Hilfsmethode verwenden, an die Regel "SID = {CURRENT_ACCOUNT_SID}", wobei *CURRENT_ACCOUNT_SID* ist die SID des die aktuelle Windows-Benutzerkonto:</span><span class="sxs-lookup"><span data-stu-id="3033a-131">Use this convenience method to specify the rule "SID={CURRENT_ACCOUNT_SID}", where *CURRENT_ACCOUNT_SID* is the SID of the current Windows user account:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

<span data-ttu-id="3033a-132">In diesem Szenario ist die Active Directory-Domänencontroller für die Verteilung von den DPAPI-NG-Vorgängen verwendeten Verschlüsselungsschlüssel verantwortlich.</span><span class="sxs-lookup"><span data-stu-id="3033a-132">In this scenario, the AD domain controller is responsible for distributing the encryption keys used by the DPAPI-NG operations.</span></span> <span data-ttu-id="3033a-133">Der Zielbenutzer kann die verschlüsselte Nutzlast über eine beliebige Domäne eingebundenen Computer entschlüsselt werden, (vorausgesetzt, dass der Prozess unter ihrer Identität ausgeführt wird).</span><span class="sxs-lookup"><span data-stu-id="3033a-133">The target user can decipher the encrypted payload from any domain-joined machine (provided that the process is running under their identity).</span></span>

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a><span data-ttu-id="3033a-134">Einem zertifikatbasierten Verschlüsselungsverfahren mit Windows DPAPI-NG</span><span class="sxs-lookup"><span data-stu-id="3033a-134">Certificate-based encryption with Windows DPAPI-NG</span></span>

<span data-ttu-id="3033a-135">Wenn die app unter Windows 8.1 / Windows Server 2012 R2 ausgeführt wird, oder höher, Sie Windows DPAPI-NG verwenden können, um zertifikatbasierte Verschlüsselung auszuführen.</span><span class="sxs-lookup"><span data-stu-id="3033a-135">If the app is running on Windows 8.1/Windows Server 2012 R2 or later, you can use Windows DPAPI-NG to perform certificate-based encryption.</span></span> <span data-ttu-id="3033a-136">Verwenden Sie die Regel sicherheitsbeschreibungs-Zeichenfolge "Zertifikat HashId:THUMBPRINT =", wobei *FINGERABDRUCK* ist der Hexadezimal-codierten SHA1-Fingerabdruck des Zertifikats:</span><span class="sxs-lookup"><span data-stu-id="3033a-136">Use the rule descriptor string "CERTIFICATE=HashId:THUMBPRINT", where *THUMBPRINT* is the hex-encoded SHA1 thumbprint of the certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

<span data-ttu-id="3033a-137">Jede app, die auf dieses Repository verwiesen wird, muss unter Windows 8.1 / Windows Server 2012 R2 oder höher, auf die Schlüssel zu entschlüsseln ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="3033a-137">Any app pointed at this repository must be running on Windows 8.1/Windows Server 2012 R2 or later to decipher the keys.</span></span>

## <a name="custom-key-encryption"></a><span data-ttu-id="3033a-138">Benutzerdefinierte Schlüsselverschlüsselung</span><span class="sxs-lookup"><span data-stu-id="3033a-138">Custom key encryption</span></span>

<span data-ttu-id="3033a-139">Wenn die integrierte Mechanismen nicht geeignet sind, kann der Entwickler einen eigenen Mechanismus für die Schlüsselverschlüsselung angeben, durch Bereitstellen eines benutzerdefinierten [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor).</span><span class="sxs-lookup"><span data-stu-id="3033a-139">If the in-box mechanisms aren't appropriate, the developer can specify their own key encryption mechanism by providing a custom [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor).</span></span>
