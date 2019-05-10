---
title: Konfigurieren von ASP.NET Core-Datenschutz
author: rick-anderson
description: Informationen Sie zum Schutz von Daten in ASP.NET Core zu konfigurieren.
ms.author: riande
ms.custom: mvc
ms.date: 04/11/2019
uid: security/data-protection/configuration/overview
ms.openlocfilehash: ee43427fa1e82a365d49df50567b4ca7afb5a5d3
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897297"
---
# <a name="configure-aspnet-core-data-protection"></a><span data-ttu-id="1ed2e-103">Konfigurieren von ASP.NET Core-Datenschutz</span><span class="sxs-lookup"><span data-stu-id="1ed2e-103">Configure ASP.NET Core Data Protection</span></span>

<span data-ttu-id="1ed2e-104">Wenn das System den Schutz von Daten initialisiert wird, gilt es [Standardeinstellungen](xref:security/data-protection/configuration/default-settings) basierend auf die betriebsumgebung.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-104">When the Data Protection system is initialized, it applies [default settings](xref:security/data-protection/configuration/default-settings) based on the operational environment.</span></span> <span data-ttu-id="1ed2e-105">Diese Einstellungen sind im Allgemeinen gut geeignet für apps, die auf einem einzelnen Computer ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-105">These settings are generally appropriate for apps running on a single machine.</span></span> <span data-ttu-id="1ed2e-106">Es gibt Fälle, in denen ein Entwickler die Standardeinstellungen ändern möchten:</span><span class="sxs-lookup"><span data-stu-id="1ed2e-106">There are cases where a developer may want to change the default settings:</span></span>

* <span data-ttu-id="1ed2e-107">Die app wird auf mehrere Computer verteilt.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-107">The app is spread across multiple machines.</span></span>
* <span data-ttu-id="1ed2e-108">Aus Compliance-Gründen.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-108">For compliance reasons.</span></span>

<span data-ttu-id="1ed2e-109">Für solche Szenarien bietet das System den Schutz von Daten eine umfangreiche Konfigurations-API.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-109">For these scenarios, the Data Protection system offers a rich configuration API.</span></span>

> [!WARNING]
> <span data-ttu-id="1ed2e-110">Ähnlich wie Konfigurationsdateien, sollte der Data Protection-Schlüsselbund geschützt werden mit entsprechenden Berechtigungen.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-110">Similar to configuration files, the data protection key ring should be protected using appropriate permissions.</span></span> <span data-ttu-id="1ed2e-111">Sie können auch die Schlüssel im Ruhezustand verschlüsselt, aber dadurch nicht verhindert, dass Angreifer Erstellen neuer Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-111">You can choose to encrypt keys at rest, but this doesn't prevent attackers from creating new keys.</span></span> <span data-ttu-id="1ed2e-112">Daher wird der app-Sicherheit beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-112">Consequently, your app's security is impacted.</span></span> <span data-ttu-id="1ed2e-113">Der Speicherort für den Schutz von Daten konfiguriert müssen den Zugriff auf die app selbst ähnliche Weise, wie Sie die Konfigurationsdateien schützen würden beschränkt.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-113">The storage location configured with Data Protection should have its access limited to the app itself, similar to the way you would protect configuration files.</span></span> <span data-ttu-id="1ed2e-114">Wenn Sie Ihrem Schlüsselbund auf dem Datenträger speichern möchten, verwenden Sie z. B. Dateisystemberechtigungen.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-114">For example, if you choose to store your key ring on disk, use file system permissions.</span></span> <span data-ttu-id="1ed2e-115">Stellen Sie nur die Identität unter sicher gelesen hat, die Ihre Web-app ausgeführt wird, schreiben und erstellen Sie auf das Verzeichnis zugreifen.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-115">Ensure only the identity under which your web app runs has read, write, and create access to that directory.</span></span> <span data-ttu-id="1ed2e-116">Wenn Sie Azure Table Storage verwenden, sollten nur die Web-app haben die Möglichkeit zu lesen, schreiben oder neue Einträge in den Tabellenspeicher usw. erstellen.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-116">If you use Azure Table Storage, only the web app should have the ability to read, write, or create new entries in the table store, etc.</span></span>
>
> <span data-ttu-id="1ed2e-117">Die Erweiterungsmethode [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) gibt ein [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="1ed2e-117">The extension method [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) returns an [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder).</span></span> <span data-ttu-id="1ed2e-118">`IDataProtectionBuilder` stellt Erweiterungsmethoden bereit, dass Sie miteinander Optionen verketten können so konfigurieren Sie den Schutz von Daten an.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-118">`IDataProtectionBuilder` exposes extension methods that you can chain together to configure Data Protection options.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a><span data-ttu-id="1ed2e-119">ProtectKeysWithAzureKeyVault</span><span class="sxs-lookup"><span data-stu-id="1ed2e-119">ProtectKeysWithAzureKeyVault</span></span>

<span data-ttu-id="1ed2e-120">Zum Speichern von Schlüsseln in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), konfigurieren Sie das System mit [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in die `Startup` Klasse:</span><span class="sxs-lookup"><span data-stu-id="1ed2e-120">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

<span data-ttu-id="1ed2e-121">Legen Sie den Schlüsselbund für den Storage-Speicherort (z. B. [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)).</span><span class="sxs-lookup"><span data-stu-id="1ed2e-121">Set the key ring storage location (for example, [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)).</span></span> <span data-ttu-id="1ed2e-122">Der Speicherort muss festgelegt werden, da Aufrufen `ProtectKeysWithAzureKeyVault` implementiert eine [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) , die Daten automatisch Protection-Einstellungen, einschließlich der den Speicherort für den Schlüsselbund deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-122">The location must be set because calling `ProtectKeysWithAzureKeyVault` implements an [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) that disables automatic data protection settings, including the key ring storage location.</span></span> <span data-ttu-id="1ed2e-123">Im vorherige Beispiel verwendet Azure Blob Storage, um den Schlüsselbund beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-123">The preceding example uses Azure Blob Storage to persist the key ring.</span></span> <span data-ttu-id="1ed2e-124">Weitere Informationen finden Sie unter [Schlüsselspeicheranbieter: Azure und Redis](xref:security/data-protection/implementation/key-storage-providers#azure-and-redis).</span><span class="sxs-lookup"><span data-stu-id="1ed2e-124">For more information, see [Key storage providers: Azure and Redis](xref:security/data-protection/implementation/key-storage-providers#azure-and-redis).</span></span> <span data-ttu-id="1ed2e-125">Sie können auch den Schlüsselbund lokal beibehalten [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).</span><span class="sxs-lookup"><span data-stu-id="1ed2e-125">You can also persist the key ring locally with [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).</span></span>

<span data-ttu-id="1ed2e-126">Die `keyIdentifier` den Schlüsselbezeichner Key Vault-Instanz, für die Schlüsselverschlüsselung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-126">The `keyIdentifier` is the key vault key identifier used for key encryption.</span></span> <span data-ttu-id="1ed2e-127">Z. B. einen Schlüssel in Key Vault-Instanz, die mit dem Namen erstellt `dataprotection` in die `contosokeyvault` hat den Schlüsselbezeichner `https://contosokeyvault.vault.azure.net/keys/dataprotection/`.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-127">For example, a key created in key vault named `dataprotection` in the `contosokeyvault` has the key identifier `https://contosokeyvault.vault.azure.net/keys/dataprotection/`.</span></span> <span data-ttu-id="1ed2e-128">Geben Sie die app mit **Schlüssel Entpacken** und **Schlüssel Packen** Berechtigungen auf den schlüsseltresor.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-128">Provide the app with **Unwrap Key** and **Wrap Key** permissions to the key vault.</span></span>

<span data-ttu-id="1ed2e-129">`ProtectKeysWithAzureKeyVault` Überladungen:</span><span class="sxs-lookup"><span data-stu-id="1ed2e-129">`ProtectKeysWithAzureKeyVault` overloads:</span></span>

* <span data-ttu-id="1ed2e-130">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) erlaubt die Verwendung einer [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) So aktivieren Sie die System zum Schutz von Daten auf den schlüsseltresor verwenden.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-130">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) permits the use of a [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) to enable the data protection system to use the key vault.</span></span>
* <span data-ttu-id="1ed2e-131">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, String, String, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) erlaubt die Verwendung einer `ClientId` und [X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) So aktivieren Sie die System zum Schutz von Daten auf den schlüsseltresor verwenden.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-131">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, String, String, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) permits the use of a `ClientId` and [X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) to enable the data protection system to use the key vault.</span></span>
* <span data-ttu-id="1ed2e-132">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, String, String, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) erlaubt die Verwendung einer `ClientId` und `ClientSecret` So aktivieren Sie die System zum Schutz von Daten auf den schlüsseltresor verwenden.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-132">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, String, String, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) permits the use of a `ClientId` and `ClientSecret` to enable the data protection system to use the key vault.</span></span>

::: moniker-end

## <a name="persistkeystofilesystem"></a><span data-ttu-id="1ed2e-133">PersistKeysToFileSystem</span><span class="sxs-lookup"><span data-stu-id="1ed2e-133">PersistKeysToFileSystem</span></span>

<span data-ttu-id="1ed2e-134">Zum Speichern von Schlüsseln auf einen UNC-Freigabe statt auf die *%LocalAppData%* Standardspeicherort, konfigurieren Sie das System mit [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):</span><span class="sxs-lookup"><span data-stu-id="1ed2e-134">To store keys on a UNC share instead of at the *%LOCALAPPDATA%* default location, configure the system with [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> <span data-ttu-id="1ed2e-135">Wenn Sie den Speicherort der schlüsselpersistenz ändern, verschlüsselt das System nicht mehr automatisch Schlüsseln im ruhenden Zustand, da er nicht weiß, ob die DPAPI mit einem entsprechenden Verschlüsselungsmechanismus ist.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-135">If you change the key persistence location, the system no longer automatically encrypts keys at rest, since it doesn't know whether DPAPI is an appropriate encryption mechanism.</span></span>

## <a name="protectkeyswith"></a><span data-ttu-id="1ed2e-136">ProtectKeysWith\*</span><span class="sxs-lookup"><span data-stu-id="1ed2e-136">ProtectKeysWith\*</span></span>

<span data-ttu-id="1ed2e-137">Sie können konfigurieren, dass das System zum Schutz von Schlüsseln im ruhenden Zustand durch Aufrufen eines der [ProtectKeysWith\* ](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) Konfigurations-APIs.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-137">You can configure the system to protect keys at rest by calling any of the [ProtectKeysWith\*](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) configuration APIs.</span></span> <span data-ttu-id="1ed2e-138">Beachten Sie die folgenden Beispiel wird der Schlüssel auf einer UNC-Freigabe gespeichert und verschlüsselt diese Schlüssel im Ruhezustand mit einem bestimmten x. 509-Zertifikat:</span><span class="sxs-lookup"><span data-stu-id="1ed2e-138">Consider the example below, which stores keys on a UNC share and encrypts those keys at rest with a specific X.509 certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="1ed2e-139">In ASP.NET Core 2.1 oder höher, bieten Sie eine [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) zu [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), z. B. ein Zertifikat aus einer Datei geladen:</span><span class="sxs-lookup"><span data-stu-id="1ed2e-139">In ASP.NET Core 2.1 or later, you can provide an [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) to [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), such as a certificate loaded from a file:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(
            new X509Certificate2("certificate.pfx", "password"));
}
```

::: moniker-end

<span data-ttu-id="1ed2e-140">Finden Sie unter [Schlüssel Verschlüsselung ruhender](xref:security/data-protection/implementation/key-encryption-at-rest) Weitere Beispiele und Informationen zu den integrierten Schlüsselverschlüsselung-Mechanismen.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-140">See [Key Encryption At Rest](xref:security/data-protection/implementation/key-encryption-at-rest) for more examples and discussion on the built-in key encryption mechanisms.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a><span data-ttu-id="1ed2e-141">UnprotectKeysWithAnyCertificate</span><span class="sxs-lookup"><span data-stu-id="1ed2e-141">UnprotectKeysWithAnyCertificate</span></span>

<span data-ttu-id="1ed2e-142">In ASP.NET Core 2.1 oder höher können Sie zertifikatrotation und Entschlüsseln von Schlüsseln im ruhenden Zustand mithilfe eines Arrays von [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) Zertifikate mit [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):</span><span class="sxs-lookup"><span data-stu-id="1ed2e-142">In ASP.NET Core 2.1 or later, you can rotate certificates and decrypt keys at rest using an array of [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) certificates with [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(
            new X509Certificate2("certificate.pfx", "password"));
        .UnprotectKeysWithAnyCertificate(
            new X509Certificate2("certificate_old_1.pfx", "password_1"),
            new X509Certificate2("certificate_old_2.pfx", "password_2"));
}
```

::: moniker-end

## <a name="setdefaultkeylifetime"></a><span data-ttu-id="1ed2e-143">SetDefaultKeyLifetime</span><span class="sxs-lookup"><span data-stu-id="1ed2e-143">SetDefaultKeyLifetime</span></span>

<span data-ttu-id="1ed2e-144">Verwenden Sie zum Konfigurieren des Systems um anstatt der standardmäßig 90 Tage eine Lebensdauer von 14 Tagen verwendet [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):</span><span class="sxs-lookup"><span data-stu-id="1ed2e-144">To configure the system to use a key lifetime of 14 days instead of the default 90 days, use [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a><span data-ttu-id="1ed2e-145">SetApplicationName</span><span class="sxs-lookup"><span data-stu-id="1ed2e-145">SetApplicationName</span></span>

<span data-ttu-id="1ed2e-146">Standardmäßig isoliert das System den Schutz von Daten apps basierend auf ihrer Pfade inhaltsstammverzeichnis voneinander, auch wenn sie im gleiche physische Schlüssel Repository gemeinsam nutzen.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-146">By default, the Data Protection system isolates apps from one another based on their content root paths, even if they're sharing the same physical key repository.</span></span> <span data-ttu-id="1ed2e-147">Dadurch wird verhindert, dass die apps des jeweils anderen geschützten Payloads zu verstehen.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-147">This prevents the apps from understanding each other's protected payloads.</span></span>

<span data-ttu-id="1ed2e-148">Geschützte Nutzlasten zwischen apps freigeben:</span><span class="sxs-lookup"><span data-stu-id="1ed2e-148">To share protected payloads among apps:</span></span>

* <span data-ttu-id="1ed2e-149">Konfigurieren von <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> in jeder app mit dem gleichen Wert.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-149">Configure <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> in each app with the same value.</span></span>
* <span data-ttu-id="1ed2e-150">Verwenden Sie die gleiche Version des Stapels Datenschutz-API für die apps an.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-150">Use the same version of the Data Protection API stack across the apps.</span></span> <span data-ttu-id="1ed2e-151">Führen Sie **entweder** Folgendes in der apps-Projektdateien:</span><span class="sxs-lookup"><span data-stu-id="1ed2e-151">Perform **either** of the following in the apps' project files:</span></span>
  * <span data-ttu-id="1ed2e-152">Verweisen auf die gleiche Version von gemeinsam genutzten Frameworks über die [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="1ed2e-152">Reference the same shared framework version via the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="1ed2e-153">Verweisen auf die gleiche [Datenschutz Paket](xref:security/data-protection/introduction#package-layout) Version.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-153">Reference the same [Data Protection package](xref:security/data-protection/introduction#package-layout) version.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a><span data-ttu-id="1ed2e-154">DisableAutomaticKeyGeneration</span><span class="sxs-lookup"><span data-stu-id="1ed2e-154">DisableAutomaticKeyGeneration</span></span>

<span data-ttu-id="1ed2e-155">Sie können ein Szenario haben, möchten Sie keine app aus, um automatisch einen Schlüssel (Erstellen neuer Schlüssel) für, wie sie Ablaufdatum nähert.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-155">You may have a scenario where you don't want an app to automatically roll keys (create new keys) as they approach expiration.</span></span> <span data-ttu-id="1ed2e-156">Ein Beispiel hierfür ist möglicherweise apps, die in einer Beziehung primär/Sekundär, in denen nur die primäre app ist verantwortlich für die schlüsselverwaltung Bedenken und sekundären apps haben einfach eine schreibgeschützte Ansicht von den Schlüsselbund einrichten.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-156">One example of this might be apps set up in a primary/secondary relationship, where only the primary app is responsible for key management concerns and secondary apps simply have a read-only view of the key ring.</span></span> <span data-ttu-id="1ed2e-157">Die sekundären apps können konfiguriert werden, um den Schlüsselbund als schreibgeschützt zu behandeln, indem Sie die Konfiguration des Systems mit <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>:</span><span class="sxs-lookup"><span data-stu-id="1ed2e-157">The secondary apps can be configured to treat the key ring as read-only by configuring the system with <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a><span data-ttu-id="1ed2e-158">Pro-Anwendungsisolation</span><span class="sxs-lookup"><span data-stu-id="1ed2e-158">Per-application isolation</span></span>

<span data-ttu-id="1ed2e-159">Wenn das System den Schutz von Daten von einem ASP.NET Core-Host bereitgestellt wird, isoliert sie automatisch apps voneinander, auch wenn diese apps in das gleiche workerkonto-Prozess ausgeführt werden, und die gleiche Schlüsselinformationen für den master verwenden.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-159">When the Data Protection system is provided by an ASP.NET Core host, it automatically isolates apps from one another, even if those apps are running under the same worker process account and are using the same master keying material.</span></span> <span data-ttu-id="1ed2e-160">Dies ähnelt dem IsolateApps-Modifizierer aus der von "System.Web" `<machineKey>` Element.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-160">This is somewhat similar to the IsolateApps modifier from System.Web's `<machineKey>` element.</span></span>

<span data-ttu-id="1ed2e-161">Der Isolationsmechanismus hochverfügbarkeitsbereitstellung jede app auf dem lokalen Computer als Mandant eindeutig, daher funktioniert die <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> Stamm eine bestimmte app automatisch auf die app-ID als Diskriminator enthält.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-161">The isolation mechanism works by considering each app on the local machine as a unique tenant, thus the <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> rooted for any given app automatically includes the app ID as a discriminator.</span></span> <span data-ttu-id="1ed2e-162">Eindeutige ID der app ist physische Pfad der app an:</span><span class="sxs-lookup"><span data-stu-id="1ed2e-162">The app's unique ID is the app's physical path:</span></span>

* <span data-ttu-id="1ed2e-163">Für apps in gehostet [IIS](xref:fundamentals/servers/index#iis-http-server), die eindeutige ID wird dem physischen IIS-Pfad der app.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-163">For apps hosted in [IIS](xref:fundamentals/servers/index#iis-http-server), the unique ID is the IIS physical path of the app.</span></span> <span data-ttu-id="1ed2e-164">Wenn eine app in einer webfarmumgebung bereitgestellt wird, ist dieser Wert stabil, vorausgesetzt, dass die IIS-Umgebungen auf allen Computern in der Webfarm auf ähnliche Weise konfiguriert sind.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-164">If an app is deployed in a web farm environment, this value is stable assuming that the IIS environments are configured similarly across all machines in the web farm.</span></span>
* <span data-ttu-id="1ed2e-165">Für selbst gehostete apps, die unter der [Kestrel-Server](xref:fundamentals/servers/index#kestrel), die eindeutige ID wird der physische Pfad für die app auf dem Datenträger.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-165">For self-hosted apps running on the [Kestrel server](xref:fundamentals/servers/index#kestrel), the unique ID is the physical path to the app on disk.</span></span>

<span data-ttu-id="1ed2e-166">Der eindeutige Bezeichner dient zum Zurücksetzen von Kennwörtern zu überstehen&mdash;sowohl der einzelnen app und dem Computer selbst.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-166">The unique identifier is designed to survive resets&mdash;both of the individual app and of the machine itself.</span></span>

<span data-ttu-id="1ed2e-167">Dieser Isolationsmechanismus wird davon ausgegangen, dass die apps nicht böswillig sind.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-167">This isolation mechanism assumes that the apps are not malicious.</span></span> <span data-ttu-id="1ed2e-168">Eine böswillige Anwendung kann immer jede andere app, die das gleiche workerkonto-Prozess unter auswirken.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-168">A malicious app can always impact any other app running under the same worker process account.</span></span> <span data-ttu-id="1ed2e-169">In einer freigegebenen Hostingumgebung, in denen apps gegenseitig nicht vertrauenswürdig sind, dauert der Hostinganbieter Schritte aus, um sicherzustellen, dass auf der Betriebssystemebene Isolation zwischen apps, die auch das Trennen der apps zugrunde liegt, zu den wichtigsten Repositorys.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-169">In a shared hosting environment where apps are mutually untrusted, the hosting provider should take steps to ensure OS-level isolation between apps, including separating the apps' underlying key repositories.</span></span>

<span data-ttu-id="1ed2e-170">Wenn das System den Schutz von Daten von einem ASP.NET Core-Host nicht bereitgestellt wird (z. B., wenn Sie über Instanziieren der `DataProtectionProvider` konkreten Typ) Isolierung ist standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-170">If the Data Protection system isn't provided by an ASP.NET Core host (for example, if you instantiate it via the `DataProtectionProvider` concrete type) app isolation is disabled by default.</span></span> <span data-ttu-id="1ed2e-171">Wenn Isolierung deaktiviert ist, alle apps, die durch die gleichen Schlüsselinformationen gesichert können freigeben Nutzlasten, solange sie die entsprechende bieten [Zwecke](xref:security/data-protection/consumer-apis/purpose-strings).</span><span class="sxs-lookup"><span data-stu-id="1ed2e-171">When app isolation is disabled, all apps backed by the same keying material can share payloads as long as they provide the appropriate [purposes](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="1ed2e-172">Um app-Isolation in dieser Umgebung bereitzustellen, rufen die [SetApplicationName](#setapplicationname) Methode für die Konfiguration Objekt aus, und geben Sie einen eindeutigen Namen für jede app.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-172">To provide app isolation in this environment, call the [SetApplicationName](#setapplicationname) method on the configuration object and provide a unique name for each app.</span></span>

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a><span data-ttu-id="1ed2e-173">Algorithmen mit UseCryptographicAlgorithms ändern</span><span class="sxs-lookup"><span data-stu-id="1ed2e-173">Changing algorithms with UseCryptographicAlgorithms</span></span>

<span data-ttu-id="1ed2e-174">Stapel für den Schutz von Daten können Sie den Standardalgorithmus, der neu generierten Schlüssel ein, zu ändern.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-174">The Data Protection stack allows you to change the default algorithm used by newly-generated keys.</span></span> <span data-ttu-id="1ed2e-175">Die einfachste Möglichkeit hierzu ist, rufen Sie [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) über den Rückruf für die Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="1ed2e-175">The simplest way to do this is to call [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) from the configuration callback:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptorConfiguration()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptionSettings()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

::: moniker-end

<span data-ttu-id="1ed2e-176">Der Standardwert "EncryptionAlgorithm" ist AES-256-CBC, und der Standardwert ValidationAlgorithm ist HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-176">The default EncryptionAlgorithm is AES-256-CBC, and the default ValidationAlgorithm is HMACSHA256.</span></span> <span data-ttu-id="1ed2e-177">Die Standardrichtlinie kann festgelegt werden, von einem Systemadministrator über eine [computerweiten Richtlinie](xref:security/data-protection/configuration/machine-wide-policy), aber ein expliziter Aufruf von `UseCryptographicAlgorithms` überschreibt die Standardrichtlinie.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-177">The default policy can be set by a system administrator via a [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy), but an explicit call to `UseCryptographicAlgorithms` overrides the default policy.</span></span>

<span data-ttu-id="1ed2e-178">Aufrufen von `UseCryptographicAlgorithms` können Sie den gewünschten Algorithmus aus einer vordefinierten, integrierten Liste angeben.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-178">Calling `UseCryptographicAlgorithms` allows you to specify the desired algorithm from a predefined built-in list.</span></span> <span data-ttu-id="1ed2e-179">Sie müssen nicht die Implementierung des Algorithmus kümmern.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-179">You don't need to worry about the implementation of the algorithm.</span></span> <span data-ttu-id="1ed2e-180">Das System den Schutz von Daten versucht, in dem oben beschriebenen Szenario die CNG-Implementierung von AES zu verwenden, wenn auf dem Windows ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-180">In the scenario above, the Data Protection system attempts to use the CNG implementation of AES if running on Windows.</span></span> <span data-ttu-id="1ed2e-181">Anderenfalls wird wieder an die verwaltete [System.Security.Cryptography.Aes](/dotnet/api/system.security.cryptography.aes) Klasse.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-181">Otherwise, it falls back to the managed [System.Security.Cryptography.Aes](/dotnet/api/system.security.cryptography.aes) class.</span></span>

<span data-ttu-id="1ed2e-182">Sie können manuell angeben, eine Implementierung über einen Aufruf an [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).</span><span class="sxs-lookup"><span data-stu-id="1ed2e-182">You can manually specify an implementation via a call to [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).</span></span>

> [!TIP]
> <span data-ttu-id="1ed2e-183">Ändern die Algorithmen wirkt sich nicht auf vorhandene Schlüssel in den Schlüsselbund aus.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-183">Changing algorithms doesn't affect existing keys in the key ring.</span></span> <span data-ttu-id="1ed2e-184">Sie wirkt sich nur auf neu generierten Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-184">It only affects newly-generated keys.</span></span>

### <a name="specifying-custom-managed-algorithms"></a><span data-ttu-id="1ed2e-185">Benutzerdefinierte Algorithmen, die verwaltete angeben</span><span class="sxs-lookup"><span data-stu-id="1ed2e-185">Specifying custom managed algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="1ed2e-186">Um benutzerdefinierte Algorithmen, die verwaltete anzugeben, erstellen Sie eine [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) -Instanz, die Implementierungstypen zeigt:</span><span class="sxs-lookup"><span data-stu-id="1ed2e-186">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) instance that points to the implementation types:</span></span>

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptorConfiguration()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="1ed2e-187">Um benutzerdefinierte Algorithmen, die verwaltete anzugeben, erstellen Sie eine [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) -Instanz, die Implementierungstypen zeigt:</span><span class="sxs-lookup"><span data-stu-id="1ed2e-187">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) instance that points to the implementation types:</span></span>

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptionSettings()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

::: moniker-end

<span data-ttu-id="1ed2e-188">In der Regel die \*Typeigenschaften müssen auf konkret zeigen instanziiert werden kann (über einen öffentlichen parameterlosen "ctor") Implementierungen von [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) und [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), obwohl die System spezielle-Fällen einige Werte wie `typeof(Aes)` der Einfachheit halber.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-188">Generally the \*Type properties must point to concrete, instantiable (via a public parameterless ctor) implementations of [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) and [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), though the system special-cases some values like `typeof(Aes)` for convenience.</span></span>

> [!NOTE]
> <span data-ttu-id="1ed2e-189">Die SymmetricAlgorithm muss eine Schlüssellänge von ≥ 128 Bits und eine Blockgröße von 64-Bit-≥ haben und es muss CBC-Modus-Verschlüsselung mit PKCS #7-Auffüllung unterstützen.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-189">The SymmetricAlgorithm must have a key length of ≥ 128 bits and a block size of ≥ 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="1ed2e-190">Die KeyedHashAlgorithm benötigen eine Digest-Größe von > = 128 Bits und müssen Schlüssel Länge gleich der Hashalgorithmus Digestlänge unterstützt.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-190">The KeyedHashAlgorithm must have a digest size of >= 128 bits, and it must support keys of length equal to the hash algorithm's digest length.</span></span> <span data-ttu-id="1ed2e-191">Die KeyedHashAlgorithm ist, nicht unbedingt erforderlich ist, HMAC sein.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-191">The KeyedHashAlgorithm isn't strictly required to be HMAC.</span></span>

### <a name="specifying-custom-windows-cng-algorithms"></a><span data-ttu-id="1ed2e-192">Angeben von benutzerdefinierten Windows CNG-Algorithmen</span><span class="sxs-lookup"><span data-stu-id="1ed2e-192">Specifying custom Windows CNG algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="1ed2e-193">Um einen benutzerdefinierten Windows CNG-Algorithmus, die HMAC-Validierung mit Verschlüsselung CBC-Modus anzugeben, erstellen eine [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) -Instanz, die algorithmische Informationen enthält:</span><span class="sxs-lookup"><span data-stu-id="1ed2e-193">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="1ed2e-194">Um einen benutzerdefinierten Windows CNG-Algorithmus, die HMAC-Validierung mit Verschlüsselung CBC-Modus anzugeben, erstellen eine [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) -Instanz, die algorithmische Informationen enthält:</span><span class="sxs-lookup"><span data-stu-id="1ed2e-194">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="1ed2e-195">Der Verschlüsselungsalgorithmus symmetrischer Blöcke müssen eine Schlüssellänge von > = 128 Bits und eine Blockgröße von > = 64 Bit, und es muss CBC-Modus-Verschlüsselung mit PKCS #7-Auffüllung unterstützen.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-195">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of >= 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="1ed2e-196">Der Hashalgorithmus benötigen eine Digest-Größe von > = 128 Bits und geöffnet wird, mit der BCRYPT unterstützen\_"alg"\_BEHANDELN\_HMAC\_FLAG-Flag.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-196">The hash algorithm must have a digest size of >= 128 bits and must support being opened with the BCRYPT\_ALG\_HANDLE\_HMAC\_FLAG flag.</span></span> <span data-ttu-id="1ed2e-197">Die \*Anbietereigenschaften können mit den Standardanbieter für den angegebenen Algorithmus auf null festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-197">The \*Provider properties can be set to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="1ed2e-198">Finden Sie unter den [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) Dokumentation zu informieren.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-198">See the [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation for more information.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="1ed2e-199">Erstellen Sie zum Angeben eines benutzerdefinierten Windows CNG-Algorithmus Überprüfung Galois/Counter-Modus-Verschlüsselung mit einem [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) -Instanz, die algorithmische Informationen enthält:</span><span class="sxs-lookup"><span data-stu-id="1ed2e-199">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="1ed2e-200">Erstellen Sie zum Angeben eines benutzerdefinierten Windows CNG-Algorithmus Überprüfung Galois/Counter-Modus-Verschlüsselung mit einem [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) -Instanz, die algorithmische Informationen enthält:</span><span class="sxs-lookup"><span data-stu-id="1ed2e-200">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="1ed2e-201">Der Verschlüsselungsalgorithmus symmetrischer Blöcke müssen eine Schlüssellänge von > = 128 Bits und eine Blockgröße von genau 128 Bits und müssen GCM-Verschlüsselung unterstützt.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-201">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of exactly 128 bits, and it must support GCM encryption.</span></span> <span data-ttu-id="1ed2e-202">Sie können festlegen, die [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) Eigenschaft auf null, wenn den Standardanbieter für den angegebenen Algorithmus verwendet.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-202">You can set the [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) property to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="1ed2e-203">Finden Sie unter den [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) Dokumentation zu informieren.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-203">See the [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) documentation for more information.</span></span>

### <a name="specifying-other-custom-algorithms"></a><span data-ttu-id="1ed2e-204">Andere benutzerdefinierte Algorithmen angeben</span><span class="sxs-lookup"><span data-stu-id="1ed2e-204">Specifying other custom algorithms</span></span>

<span data-ttu-id="1ed2e-205">Jedoch nicht als erstklassige-API verfügbar gemacht, wird das System den Schutz von Daten erweitern, dass die Angabe von nahezu jede Art von Algorithmus zu.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-205">Though not exposed as a first-class API, the Data Protection system is extensible enough to allow specifying almost any kind of algorithm.</span></span> <span data-ttu-id="1ed2e-206">Beispielsweise ist es möglich, für die alle Schlüssel in einem Hardwaresicherheitsmodul (HSM) enthalten und eine benutzerdefinierte Implementierung der wichtigsten Ver- und Entschlüsselung Routinen bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-206">For example, it's possible to keep all keys contained within a Hardware Security Module (HSM) and to provide a custom implementation of the core encryption and decryption routines.</span></span> <span data-ttu-id="1ed2e-207">Finden Sie unter [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) in [Core kryptografieerweiterbarkeit](xref:security/data-protection/extensibility/core-crypto) für Weitere Informationen.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-207">See [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) in [Core cryptography extensibility](xref:security/data-protection/extensibility/core-crypto) for more information.</span></span>

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a><span data-ttu-id="1ed2e-208">Beibehalten von Schlüsseln, die beim Hosten in einem Docker-container</span><span class="sxs-lookup"><span data-stu-id="1ed2e-208">Persisting keys when hosting in a Docker container</span></span>

<span data-ttu-id="1ed2e-209">Beim Hosten in einem [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) Container Schlüssel sollte entweder beibehalten werden:</span><span class="sxs-lookup"><span data-stu-id="1ed2e-209">When hosting in a [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) container, keys should be maintained in either:</span></span>

* <span data-ttu-id="1ed2e-210">Ein Ordner, der ein Docker-Volume ist, die über die Lebensdauer des Containers, z. B. ein freigegebenes Volume oder einem Host eingebundenen Volume erhalten bleibt.</span><span class="sxs-lookup"><span data-stu-id="1ed2e-210">A folder that's a Docker volume that persists beyond the container's lifetime, such as a shared volume or a host-mounted volume.</span></span>
* <span data-ttu-id="1ed2e-211">Einen externen Anbieter, wie z. B. [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) oder [Redis](https://redis.io/).</span><span class="sxs-lookup"><span data-stu-id="1ed2e-211">An external provider, such as [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) or [Redis](https://redis.io/).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1ed2e-212">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="1ed2e-212">Additional resources</span></span>

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
