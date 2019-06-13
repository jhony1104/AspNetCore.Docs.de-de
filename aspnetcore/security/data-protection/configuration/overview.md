---
title: Konfigurieren von ASP.NET Core-Datenschutz
author: rick-anderson
description: Informationen Sie zum Schutz von Daten in ASP.NET Core zu konfigurieren.
ms.author: riande
ms.custom: mvc
ms.date: 04/11/2019
uid: security/data-protection/configuration/overview
ms.openlocfilehash: 65a927b6288ca6cc41ee1bedd1080e52ffe0d3e1
ms.sourcegitcommit: 335a88c1b6e7f0caa8a3a27db57c56664d676d34
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/12/2019
ms.locfileid: "67034921"
---
# <a name="configure-aspnet-core-data-protection"></a>Konfigurieren von ASP.NET Core-Datenschutz

Wenn das System den Schutz von Daten initialisiert wird, gilt es [Standardeinstellungen](xref:security/data-protection/configuration/default-settings) basierend auf die betriebsumgebung. Diese Einstellungen sind im Allgemeinen gut geeignet für apps, die auf einem einzelnen Computer ausgeführt. Es gibt Fälle, in denen ein Entwickler die Standardeinstellungen ändern möchten:

* Die app wird auf mehrere Computer verteilt.
* Aus Compliance-Gründen.

Für solche Szenarien bietet das System den Schutz von Daten eine umfangreiche Konfigurations-API.

> [!WARNING]
> Ähnlich wie Konfigurationsdateien, sollte der Data Protection-Schlüsselbund geschützt werden mit entsprechenden Berechtigungen. Sie können auch die Schlüssel im Ruhezustand verschlüsselt, aber dadurch nicht verhindert, dass Angreifer Erstellen neuer Schlüssel. Daher wird der app-Sicherheit beeinträchtigt. Der Speicherort für den Schutz von Daten konfiguriert müssen den Zugriff auf die app selbst ähnliche Weise, wie Sie die Konfigurationsdateien schützen würden beschränkt. Wenn Sie Ihrem Schlüsselbund auf dem Datenträger speichern möchten, verwenden Sie z. B. Dateisystemberechtigungen. Stellen Sie nur die Identität unter sicher gelesen hat, die Ihre Web-app ausgeführt wird, schreiben und erstellen Sie auf das Verzeichnis zugreifen. Wenn Sie Azure Blob Storage verwenden, sollten nur für die Web-app haben die Möglichkeit zu lesen, schreiben oder neue Einträge in den Blob-Speicher usw. erstellen.
>
> Die Erweiterungsmethode [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) gibt ein [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder). `IDataProtectionBuilder` stellt Erweiterungsmethoden bereit, dass Sie miteinander Optionen verketten können so konfigurieren Sie den Schutz von Daten an.

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a>ProtectKeysWithAzureKeyVault

Zum Speichern von Schlüsseln in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), konfigurieren Sie das System mit [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in die `Startup` Klasse:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

Legen Sie den Schlüsselbund für den Storage-Speicherort (z. B. [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)). Der Speicherort muss festgelegt werden, da Aufrufen `ProtectKeysWithAzureKeyVault` implementiert eine [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) , die Daten automatisch Protection-Einstellungen, einschließlich der den Speicherort für den Schlüsselbund deaktiviert. Im vorherige Beispiel verwendet Azure Blob Storage, um den Schlüsselbund beizubehalten. Weitere Informationen finden Sie unter [Schlüsselspeicheranbieter: Azure Storage](xref:security/data-protection/implementation/key-storage-providers#azure-storage). Sie können auch den Schlüsselbund lokal beibehalten [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).

Die `keyIdentifier` den Schlüsselbezeichner Key Vault-Instanz, für die Schlüsselverschlüsselung verwendet wird. Z. B. einen Schlüssel in Key Vault-Instanz, die mit dem Namen erstellt `dataprotection` in die `contosokeyvault` hat den Schlüsselbezeichner `https://contosokeyvault.vault.azure.net/keys/dataprotection/`. Geben Sie die app mit **Schlüssel Entpacken** und **Schlüssel Packen** Berechtigungen auf den schlüsseltresor.

`ProtectKeysWithAzureKeyVault` Überladungen:

* [ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, KeyVaultClient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) erlaubt die Verwendung einer [KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) So aktivieren Sie die System zum Schutz von Daten auf den schlüsseltresor verwenden.
* [ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, String, String, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) erlaubt die Verwendung einer `ClientId` und [X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) So aktivieren Sie die System zum Schutz von Daten auf den schlüsseltresor verwenden.
* [ProtectKeysWithAzureKeyVault (IDataProtectionBuilder, String, String, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) erlaubt die Verwendung einer `ClientId` und `ClientSecret` So aktivieren Sie die System zum Schutz von Daten auf den schlüsseltresor verwenden.

::: moniker-end

## <a name="persistkeystofilesystem"></a>PersistKeysToFileSystem

Zum Speichern von Schlüsseln auf einen UNC-Freigabe statt auf die *%LocalAppData%* Standardspeicherort, konfigurieren Sie das System mit [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> Wenn Sie den Speicherort der schlüsselpersistenz ändern, verschlüsselt das System nicht mehr automatisch Schlüsseln im ruhenden Zustand, da er nicht weiß, ob die DPAPI mit einem entsprechenden Verschlüsselungsmechanismus ist.

## <a name="protectkeyswith"></a>ProtectKeysWith\*

Sie können konfigurieren, dass das System zum Schutz von Schlüsseln im ruhenden Zustand durch Aufrufen eines der [ProtectKeysWith\* ](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) Konfigurations-APIs. Beachten Sie die folgenden Beispiel wird der Schlüssel auf einer UNC-Freigabe gespeichert und verschlüsselt diese Schlüssel im Ruhezustand mit einem bestimmten x. 509-Zertifikat:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

In ASP.NET Core 2.1 oder höher, bieten Sie eine [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) zu [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), z. B. ein Zertifikat aus einer Datei geladen:

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

Finden Sie unter [Schlüssel Verschlüsselung ruhender](xref:security/data-protection/implementation/key-encryption-at-rest) Weitere Beispiele und Informationen zu den integrierten Schlüsselverschlüsselung-Mechanismen.

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a>UnprotectKeysWithAnyCertificate

In ASP.NET Core 2.1 oder höher können Sie zertifikatrotation und Entschlüsseln von Schlüsseln im ruhenden Zustand mithilfe eines Arrays von [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) Zertifikate mit [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):

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

## <a name="setdefaultkeylifetime"></a>SetDefaultKeyLifetime

Verwenden Sie zum Konfigurieren des Systems um anstatt der standardmäßig 90 Tage eine Lebensdauer von 14 Tagen verwendet [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a>SetApplicationName

Standardmäßig isoliert das System den Schutz von Daten apps basierend auf ihrer Pfade inhaltsstammverzeichnis voneinander, auch wenn sie im gleiche physische Schlüssel Repository gemeinsam nutzen. Dadurch wird verhindert, dass die apps des jeweils anderen geschützten Payloads zu verstehen.

Geschützte Nutzlasten zwischen apps freigeben:

* Konfigurieren von <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> in jeder app mit dem gleichen Wert.
* Verwenden Sie die gleiche Version des Stapels Datenschutz-API für die apps an. Führen Sie **entweder** Folgendes in der apps-Projektdateien:
  * Verweisen auf die gleiche Version von gemeinsam genutzten Frameworks über die [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app).
  * Verweisen auf die gleiche [Datenschutz Paket](xref:security/data-protection/introduction#package-layout) Version.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a>DisableAutomaticKeyGeneration

Sie können ein Szenario haben, möchten Sie keine app aus, um automatisch einen Schlüssel (Erstellen neuer Schlüssel) für, wie sie Ablaufdatum nähert. Ein Beispiel hierfür ist möglicherweise apps, die in einer Beziehung primär/Sekundär, in denen nur die primäre app ist verantwortlich für die schlüsselverwaltung Bedenken und sekundären apps haben einfach eine schreibgeschützte Ansicht von den Schlüsselbund einrichten. Die sekundären apps können konfiguriert werden, um den Schlüsselbund als schreibgeschützt zu behandeln, indem Sie die Konfiguration des Systems mit <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a>Pro-Anwendungsisolation

Wenn das System den Schutz von Daten von einem ASP.NET Core-Host bereitgestellt wird, isoliert sie automatisch apps voneinander, auch wenn diese apps in das gleiche workerkonto-Prozess ausgeführt werden, und die gleiche Schlüsselinformationen für den master verwenden. Dies ähnelt dem IsolateApps-Modifizierer aus der von "System.Web" `<machineKey>` Element.

Der Isolationsmechanismus hochverfügbarkeitsbereitstellung jede app auf dem lokalen Computer als Mandant eindeutig, daher funktioniert die <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> Stamm eine bestimmte app automatisch auf die app-ID als Diskriminator enthält. Eindeutige ID der app ist physische Pfad der app an:

* Für apps, die in IIS gehostet wird ist die eindeutige ID der physischen IIS-Pfad der app. Wenn eine app in einer webfarmumgebung bereitgestellt wird, ist dieser Wert stabil, vorausgesetzt, dass die IIS-Umgebungen auf allen Computern in der Webfarm auf ähnliche Weise konfiguriert sind.
* Für selbst gehostete apps, die unter der [Kestrel-Server](xref:fundamentals/servers/index#kestrel), die eindeutige ID wird der physische Pfad für die app auf dem Datenträger.

Der eindeutige Bezeichner dient zum Zurücksetzen von Kennwörtern zu überstehen&mdash;sowohl der einzelnen app und dem Computer selbst.

Dieser Isolationsmechanismus wird davon ausgegangen, dass die apps nicht böswillig sind. Eine böswillige Anwendung kann immer jede andere app, die das gleiche workerkonto-Prozess unter auswirken. In einer freigegebenen Hostingumgebung, in denen apps gegenseitig nicht vertrauenswürdig sind, dauert der Hostinganbieter Schritte aus, um sicherzustellen, dass auf der Betriebssystemebene Isolation zwischen apps, die auch das Trennen der apps zugrunde liegt, zu den wichtigsten Repositorys.

Wenn das System den Schutz von Daten von einem ASP.NET Core-Host nicht bereitgestellt wird (z. B., wenn Sie über Instanziieren der `DataProtectionProvider` konkreten Typ) Isolierung ist standardmäßig deaktiviert. Wenn Isolierung deaktiviert ist, alle apps, die durch die gleichen Schlüsselinformationen gesichert können freigeben Nutzlasten, solange sie die entsprechende bieten [Zwecke](xref:security/data-protection/consumer-apis/purpose-strings). Um app-Isolation in dieser Umgebung bereitzustellen, rufen die [SetApplicationName](#setapplicationname) Methode für die Konfiguration Objekt aus, und geben Sie einen eindeutigen Namen für jede app.

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a>Algorithmen mit UseCryptographicAlgorithms ändern

Stapel für den Schutz von Daten können Sie den Standardalgorithmus, der neu generierten Schlüssel ein, zu ändern. Die einfachste Möglichkeit hierzu ist, rufen Sie [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) über den Rückruf für die Konfiguration:

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

Der Standardwert "EncryptionAlgorithm" ist AES-256-CBC, und der Standardwert ValidationAlgorithm ist HMACSHA256. Die Standardrichtlinie kann festgelegt werden, von einem Systemadministrator über eine [computerweiten Richtlinie](xref:security/data-protection/configuration/machine-wide-policy), aber ein expliziter Aufruf von `UseCryptographicAlgorithms` überschreibt die Standardrichtlinie.

Aufrufen von `UseCryptographicAlgorithms` können Sie den gewünschten Algorithmus aus einer vordefinierten, integrierten Liste angeben. Sie müssen nicht die Implementierung des Algorithmus kümmern. Das System den Schutz von Daten versucht, in dem oben beschriebenen Szenario die CNG-Implementierung von AES zu verwenden, wenn auf dem Windows ausgeführt. Anderenfalls wird wieder an die verwaltete [System.Security.Cryptography.Aes](/dotnet/api/system.security.cryptography.aes) Klasse.

Sie können manuell angeben, eine Implementierung über einen Aufruf an [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).

> [!TIP]
> Ändern die Algorithmen wirkt sich nicht auf vorhandene Schlüssel in den Schlüsselbund aus. Sie wirkt sich nur auf neu generierten Schlüssel.

### <a name="specifying-custom-managed-algorithms"></a>Benutzerdefinierte Algorithmen, die verwaltete angeben

::: moniker range=">= aspnetcore-2.0"

Um benutzerdefinierte Algorithmen, die verwaltete anzugeben, erstellen Sie eine [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) -Instanz, die Implementierungstypen zeigt:

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

Um benutzerdefinierte Algorithmen, die verwaltete anzugeben, erstellen Sie eine [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) -Instanz, die Implementierungstypen zeigt:

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

In der Regel die \*Typeigenschaften müssen auf konkret zeigen instanziiert werden kann (über einen öffentlichen parameterlosen "ctor") Implementierungen von [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) und [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), obwohl die System spezielle-Fällen einige Werte wie `typeof(Aes)` der Einfachheit halber.

> [!NOTE]
> Die SymmetricAlgorithm muss eine Schlüssellänge von ≥ 128 Bits und eine Blockgröße von 64-Bit-≥ haben und es muss CBC-Modus-Verschlüsselung mit PKCS #7-Auffüllung unterstützen. Die KeyedHashAlgorithm benötigen eine Digest-Größe von > = 128 Bits und müssen Schlüssel Länge gleich der Hashalgorithmus Digestlänge unterstützt. Die KeyedHashAlgorithm ist, nicht unbedingt erforderlich ist, HMAC sein.

### <a name="specifying-custom-windows-cng-algorithms"></a>Angeben von benutzerdefinierten Windows CNG-Algorithmen

::: moniker range=">= aspnetcore-2.0"

Um einen benutzerdefinierten Windows CNG-Algorithmus, die HMAC-Validierung mit Verschlüsselung CBC-Modus anzugeben, erstellen eine [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) -Instanz, die algorithmische Informationen enthält:

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

Um einen benutzerdefinierten Windows CNG-Algorithmus, die HMAC-Validierung mit Verschlüsselung CBC-Modus anzugeben, erstellen eine [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) -Instanz, die algorithmische Informationen enthält:

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
> Der Verschlüsselungsalgorithmus symmetrischer Blöcke müssen eine Schlüssellänge von > = 128 Bits und eine Blockgröße von > = 64 Bit, und es muss CBC-Modus-Verschlüsselung mit PKCS #7-Auffüllung unterstützen. Der Hashalgorithmus benötigen eine Digest-Größe von > = 128 Bits und geöffnet wird, mit der BCRYPT unterstützen\_"alg"\_BEHANDELN\_HMAC\_FLAG-Flag. Die \*Anbietereigenschaften können mit den Standardanbieter für den angegebenen Algorithmus auf null festgelegt werden. Finden Sie unter den [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) Dokumentation zu informieren.

::: moniker range=">= aspnetcore-2.0"

Erstellen Sie zum Angeben eines benutzerdefinierten Windows CNG-Algorithmus Überprüfung Galois/Counter-Modus-Verschlüsselung mit einem [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) -Instanz, die algorithmische Informationen enthält:

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

Erstellen Sie zum Angeben eines benutzerdefinierten Windows CNG-Algorithmus Überprüfung Galois/Counter-Modus-Verschlüsselung mit einem [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) -Instanz, die algorithmische Informationen enthält:

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
> Der Verschlüsselungsalgorithmus symmetrischer Blöcke müssen eine Schlüssellänge von > = 128 Bits und eine Blockgröße von genau 128 Bits und müssen GCM-Verschlüsselung unterstützt. Sie können festlegen, die [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) Eigenschaft auf null, wenn den Standardanbieter für den angegebenen Algorithmus verwendet. Finden Sie unter den [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) Dokumentation zu informieren.

### <a name="specifying-other-custom-algorithms"></a>Andere benutzerdefinierte Algorithmen angeben

Jedoch nicht als erstklassige-API verfügbar gemacht, wird das System den Schutz von Daten erweitern, dass die Angabe von nahezu jede Art von Algorithmus zu. Beispielsweise ist es möglich, für die alle Schlüssel in einem Hardwaresicherheitsmodul (HSM) enthalten und eine benutzerdefinierte Implementierung der wichtigsten Ver- und Entschlüsselung Routinen bereitzustellen. Finden Sie unter [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) in [Core kryptografieerweiterbarkeit](xref:security/data-protection/extensibility/core-crypto) für Weitere Informationen.

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a>Beibehalten von Schlüsseln, die beim Hosten in einem Docker-container

Beim Hosten in einem [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) Container Schlüssel sollte entweder beibehalten werden:

* Ein Ordner, der ein Docker-Volume ist, die über die Lebensdauer des Containers, z. B. ein freigegebenes Volume oder einem Host eingebundenen Volume erhalten bleibt.
* Einen externen Anbieter, wie z. B. [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) oder [Redis](https://redis.io/).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
