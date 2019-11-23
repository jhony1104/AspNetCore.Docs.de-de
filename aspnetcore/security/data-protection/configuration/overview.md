---
title: Konfigurieren von ASP.net Core Datenschutz
author: rick-anderson
description: Erfahren Sie, wie Sie den Schutz von Daten in ASP.net Core konfigurieren.
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: security/data-protection/configuration/overview
ms.openlocfilehash: 380293f650c9548c286f98c0447c7ed08b918f2a
ms.sourcegitcommit: 3d082bd46e9e00a3297ea0314582b1ed2abfa830
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2019
ms.locfileid: "72007378"
---
# <a name="configure-aspnet-core-data-protection"></a>Konfigurieren von ASP.net Core Datenschutz

Wenn das Datenschutzsystem initialisiert ist, werden die [Standardeinstellungen](xref:security/data-protection/configuration/default-settings) basierend auf der Betriebsumgebung angewendet. Diese Einstellungen eignen sich in der Regel für apps, die auf einem einzelnen Computer ausgeführt werden. Es gibt Fälle, in denen Entwickler möglicherweise die Standardeinstellungen ändern möchten:

* Die APP ist auf mehrere Computer verteilt.
* Aus Kompatibilitätsgründen.

In diesen Szenarien bietet das Datenschutzsystem eine umfangreiche Konfigurations-API.

> [!WARNING]
> Ähnlich wie Konfigurationsdateien sollte der Schlüsselbund für den Datenschutz mit den entsprechenden Berechtigungen geschützt werden. Sie können auswählen, dass Schlüssel im Ruhezustand verschlüsselt werden, aber dies hindert Angreifer daran, keine neuen Schlüssel zu erstellen. Folglich ist die Sicherheit Ihrer APP beeinträchtigt. Der mit dem Schutz von Daten konfigurierte Speicherort muss auf die APP selbst beschränkt sein, ähnlich wie beim Schutz von Konfigurationsdateien. Wenn Sie z. b. den Schlüsselbund auf einem Datenträger speichern möchten, verwenden Sie die Dateisystem Berechtigungen. Stellen Sie sicher, dass nur die Identität, unter der Ihre Web-App ausgeführt wird, Lese-und Schreibzugriff auf dieses Verzeichnis hat. Wenn Sie Azure BLOB Storage verwenden, sollte nur die Web-App in der Lage sein, neue Einträge im BLOB-Speicher zu lesen, zu schreiben oder zu erstellen, usw.
>
> Die Erweiterungsmethode [adddataprotection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) gibt ein [idataschutzbuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder)-Element zurück. `IDataProtectionBuilder` macht Erweiterungs Methoden verfügbar, die Sie verbinden können, um Datenschutzoptionen zu konfigurieren.

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a>ProtectKeysWithAzureKeyVault

Um Schlüssel in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)zu speichern, konfigurieren Sie das System mit " [protectkeyswithazurekeyvault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) " in der `Startup`-Klasse:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

Legen Sie den Speicherort des Schlüsselrings fest (z. [b. persistkeystoazureblobstorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)). Der Speicherort muss festgelegt werden, da der aufrufende `ProtectKeysWithAzureKeyVault` einen [ixmlencryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) implementiert, der die Einstellungen für den automatischen Datenschutz deaktiviert, einschließlich des Speicher Orts für den Schlüsselring. Im vorangehenden Beispiel wird Azure BLOB Storage verwendet, um den Schlüsselring beizubehalten. Weitere Informationen finden Sie unter [Schlüsselspeicher Anbieter: Azure Storage](xref:security/data-protection/implementation/key-storage-providers#azure-storage). Sie können den Schlüsselring auch lokal mit [persistkeystofile System](xref:security/data-protection/implementation/key-storage-providers#file-system)speichern.

Der `keyIdentifier` ist die Key Vault-Schlüssel Kennung, die für die Schlüssel Verschlüsselung verwendet wird. Ein Schlüssel, der in Key Vault namens `dataprotection` in der `contosokeyvault` erstellt wurde, verfügt z. b. über den Schlüssel Bezeichner `https://contosokeyvault.vault.azure.net/keys/dataprotection/`. Stellen Sie für die APP die Berechtigungen **Entpacken** **und Schlüssel einschließen** für den Schlüssel Tresor bereit.

`ProtectKeysWithAzureKeyVault` Überladungen:

* [Protectkeyswithazurekeyvault (idataschutzbuilder, keyvaultclient, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_Microsoft_Azure_KeyVault_KeyVaultClient_System_String_) ermöglicht die Verwendung eines [keyvaultclient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient) , damit das Datenschutzsystem den Schlüssel Tresor verwenden kann.
* [Protectkeyswithazurekeyvault (idataschutzbuilder, String, String, X509Certificate2)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_Security_Cryptography_X509Certificates_X509Certificate2_) ermöglicht die Verwendung eines `ClientId` und [X509Certificate](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) , damit das Datenschutzsystem den Schlüssel Tresor verwenden kann.
* [Protectkeyswithazurekeyvault (idataschutzbuilder, String, String, String)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_System_String_System_String_) gestattet die Verwendung eines `ClientId` und `ClientSecret`, damit das Datenschutzsystem den Schlüssel Tresor verwenden kann.

::: moniker-end

## <a name="persistkeystofilesystem"></a>PersistKeysToFileSystem

Zum Speichern von Schlüsseln auf einer UNC-Freigabe anstelle von *% LocalAppData%* Standard Speicherort konfigurieren Sie das System mit [persistkeystofile](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem)System:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> Wenn Sie den Speicherort für die Schlüssel Persistenz ändern, verschlüsselt das System die Schlüssel im Ruhezustand nicht mehr automatisch, da nicht bekannt ist, ob DPAPI ein geeigneter Verschlüsselungsmechanismus ist.

## <a name="protectkeyswith"></a>ProtectKeysWith\*

Sie können das System konfigurieren, um Schlüssel im Ruhezustand zu schützen, indem Sie eine der [protectkeyswith-\*](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) Konfigurations-APIs aufrufen. Betrachten Sie das folgende Beispiel, in dem Schlüssel auf einer UNC-Freigabe gespeichert werden und die Schlüssel im Ruhezustand mit einem bestimmten X. 509-Zertifikat verschlüsselt werden:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

In ASP.net Core 2,1 oder höher können Sie eine [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) für [protectkeyswithcertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate)bereitstellen, z. b. ein Zertifikat, das aus einer Datei geladen wurde:

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

Weitere Beispiele und Erörterung der integrierten Schlüssel Verschlüsselungsmechanismen finden Sie unter [Verschlüsselung ruhender Schlüssel](xref:security/data-protection/implementation/key-encryption-at-rest) .

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a>UnprotectKeysWithAnyCertificate

In ASP.net Core 2,1 oder höher können Sie Zertifikate rotieren und Schlüssel im Ruhezustand mit einem Array von [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) -Zertifikaten mit [unprotectkeyswithanycertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate)entschlüsseln:

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

Verwenden Sie [setdefaultkeylifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime), um das System so zu konfigurieren, dass eine Schlüssel Lebensdauer von 14 Tagen anstelle der standardmäßigen 90 Tage verwendet wird:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a>SetApplicationName

Standardmäßig isoliert das Datenschutzsystem apps auf der Grundlage ihrer [Inhalts](xref:fundamentals/index#content-root) Stamm Pfade voneinander, auch wenn Sie das gleiche physische schlüsselrepository nutzen. Dadurch wird verhindert, dass die apps gegenseitig die geschützten Nutzlasten verstehen.

So geben Sie geschützte Nutzlasten für apps frei:

* Konfigurieren Sie <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> in jeder App mit dem gleichen Wert.
* Verwenden Sie die gleiche Version des Datenschutz-API-Stapels in den apps. Führen **Sie** in den Projektdateien der apps eine der folgenden Aktionen aus:
  * Verweisen Sie über das [Metapaket "Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)" auf dieselbe freigegebene Framework-Version.
  * Verweisen auf die gleiche Version des [datenschutzpakets](xref:security/data-protection/introduction#package-layout) .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a>DisableAutomaticKeyGeneration

Möglicherweise gibt es ein Szenario, in dem eine APP nicht automatisch ein Rollback für Schlüssel durchführen soll (neue Schlüssel erstellen), wenn Sie den Ablauf erreichen. Ein Beispiel hierfür sind apps, die in einer primären/sekundären Beziehung eingerichtet sind, bei der nur die primäre App für wichtige Verwaltungsprobleme zuständig ist und sekundäre Apps einfach nur eine schreibgeschützte Ansicht des Schlüsselrings haben. Die sekundären Apps können so konfiguriert werden, dass Sie den Schlüsselbund als schreibgeschützt behandeln, indem Sie das System mit <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>konfigurieren:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a>Isolation pro Anwendung

Wenn das Datenschutzsystem von einem ASP.net Core Host bereitgestellt wird, werden die Apps automatisch voneinander isoliert, auch wenn diese apps unter demselben Workerprozesskonto ausgeführt werden und dasselbe Masterschlüssel Material verwenden. Dies ähnelt dem IsolateApps-Modifizierer aus dem `<machineKey>`-Element von System. Web.

Der Isolations Mechanismus funktioniert, indem jede APP auf dem lokalen Computer als eindeutiger Mandant betrachtet wird, sodass die <xref:Microsoft.AspNetCore.DataProtection.IDataProtector>, die für eine bestimmte App verwendet wird, die APP-ID automatisch als Diskriminator enthält. Die eindeutige ID der APP ist der physische Pfad der APP:

* Bei in IIS gehosteten Apps ist die eindeutige ID der physische IIS-Pfad der app. Wenn eine app in einer Webfarm Umgebung bereitgestellt wird, ist dieser wertstabil, wenn Sie davon ausgehen, dass die IIS-Umgebungen auf allen Computern in der Webfarm ähnlich konfiguriert sind.
* Für selbst gehostete Apps, die auf dem [Kestrel-Server](xref:fundamentals/servers/index#kestrel)ausgeführt werden, ist die eindeutige ID der physische Pfad zur APP auf dem Datenträger.

Der eindeutige Bezeichner ist so konzipiert, dass er zurück Stellungen&mdash;beiden einzelnen apps und des Computers selbst überstehen.

Dieser Isolations Mechanismus geht davon aus, dass die apps nicht schädlich sind. Eine böswillige App kann sich immer auf jede andere APP auswirken, die unter demselben Workerprozesskonto ausgeführt wird. In einer freigegebenen Hostingumgebung, in der apps gegenseitig nicht vertrauenswürdig sind, sollte der Hostinganbieter Maßnahmen ergreifen, um die Isolation auf Betriebssystemebene zwischen apps sicherzustellen, einschließlich der Trennung der zugrunde liegenden Schlüssel Depots

Wenn das Datenschutzsystem nicht von einem ASP.net Core Host bereitgestellt wird (wenn Sie z. b. über den `DataProtectionProvider` konkreten Typ instanziieren), ist die APP-Isolation standardmäßig deaktiviert. Wenn die APP-Isolation deaktiviert ist, können alle apps, die durch das gleiche Schlüssel Erstellungs Material unterstützt werden, Nutzlasten gemeinsam nutzen, sofern Sie die entsprechenden [Zwecke](xref:security/data-protection/consumer-apis/purpose-strings)bereitstellen. Um die APP-Isolation in dieser Umgebung bereitzustellen, wenden Sie die [setapplicationname](#setapplicationname) -Methode für das Konfigurationsobjekt an, und geben Sie einen eindeutigen Namen für jede APP an.

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a>Ändern von Algorithmen mit usecrypumgraphicalgorithms

Der Datenschutz Stapel ermöglicht es Ihnen, den von neu generierten Schlüsseln verwendeten Standard Algorithmus zu ändern. Der einfachste Weg hierfür ist das Abrufen von " [usecrypregraphicalgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) " aus dem Konfigurations Rückruf:

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

Der Standardverschlüsselungsalgorithmus ist AES-256-CBC, und der standardmäßige validationalgorithmus ist HMACSHA256. Die Standard Richtlinie kann von einem Systemadministrator über eine [Computer weite Richtlinie](xref:security/data-protection/configuration/machine-wide-policy)festgelegt werden, aber ein expliziter-`UseCryptographicAlgorithms` überschreibt die Standard Richtlinie.

Wenn Sie `UseCryptographicAlgorithms` aufrufen, können Sie den gewünschten Algorithmus aus einer vordefinierten integrierten Liste angeben. Sie müssen sich keine Gedanken über die Implementierung des Algorithmus machen. Im obigen Szenario versucht das Datenschutzsystem, die CNG-Implementierung von AES zu verwenden, wenn es unter Windows ausgeführt wird. Andernfalls greift Sie auf die verwaltete [System. Security. Cryptography. AES](/dotnet/api/system.security.cryptography.aes) -Klasse zurück.

Sie können eine Implementierung manuell über einen [callcustomcryptographicalgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms)-Befehl angeben.

> [!TIP]
> Veränderliche Algorithmen wirken sich nicht auf vorhandene Schlüssel im Schlüsselbund aus. Er wirkt sich nur auf neu generierte Schlüssel aus.

### <a name="specifying-custom-managed-algorithms"></a>Angeben von benutzerdefinierten verwalteten Algorithmen

::: moniker range=">= aspnetcore-2.0"

Um benutzerdefinierte verwaltete Algorithmen anzugeben, erstellen Sie eine [managedauthenticatedencryptorconfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) -Instanz, die auf die Implementierungs Typen zeigt:

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

Um benutzerdefinierte verwaltete Algorithmen anzugeben, erstellen Sie eine [managedauthenticatedencryptionsettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) -Instanz, die auf die Implementierungs Typen zeigt:

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

Im Allgemeinen müssen die \*-Typeigenschaften auf konkrete, instanziier Bare Implementierungen (über einen öffentlichen Parameter losen ctor) von [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) und [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm)verweisen, obwohl das System bestimmte Werte wie `typeof(Aes)` aus Gründen der Beweglichkeit als Sonderfall.

> [!NOTE]
> Der SymmetricAlgorithm muss eine Schlüssellänge von 128 Bits und eine Blockgröße von 64 Bits aufweisen und muss die Verschlüsselung im CBC-Modus mit PKCS #7 Padding unterstützen. Der KeyedHashAlgorithm muss eine Hashgröße von > = 128 Bits aufweisen, und er muss Schlüssel mit einer Länge unterstützen, die der Digest-Länge des Hash Algorithmus entspricht. KeyedHashAlgorithm muss nicht unbedingt HMAC sein.

### <a name="specifying-custom-windows-cng-algorithms"></a>Angeben von benutzerdefinierten Windows CNG-Algorithmen

::: moniker range=">= aspnetcore-2.0"

Wenn Sie einen benutzerdefinierten Windows CNG-Algorithmus mithilfe der CBC-modusverschlüsselung mit HMAC-Überprüfung angeben möchten, erstellen Sie eine [cngcbcauthenticatedencryptor Configuration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) -Instanz, die algorithmische Informationen enthält:

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

Wenn Sie einen benutzerdefinierten Windows CNG-Algorithmus mithilfe der CBC-modusverschlüsselung mit HMAC-Überprüfung angeben möchten, erstellen Sie eine [cngcbcauthenticatedencryptionsettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) -Instanz, die die algorithmischen Informationen enthält:

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
> Der Algorithmus für symmetrische Blockchiffre muss eine Schlüssellänge von > = 128 Bits, eine Blockgröße von > = 64 Bits aufweisen und muss die Verschlüsselung im CBC-Modus mit PKCS #7 Padding unterstützen. Der Hash Algorithmus muss eine Hashgröße von > = 128 Bits aufweisen und muss Unterstützung für das Öffnen mit dem bcrypt-\_ALG\_handle\_HMAC\_Flag-Flag aufweisen. Die Eigenschaften des \*Anbieters können auf NULL festgelegt werden, um den Standardanbieter für den angegebenen Algorithmus zu verwenden. Weitere Informationen finden Sie in der Dokumentation zu [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) .

::: moniker range=">= aspnetcore-2.0"

Zum Angeben eines benutzerdefinierten Windows CNG-Algorithmus mit der Datei "Galois/Counter Mode Encryption with Validation" erstellen Sie eine [cnggcmauthenticatedencryptor Configuration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) -Instanz, die die algorithmischen Informationen enthält:

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

Zum Angeben eines benutzerdefinierten Windows CNG-Algorithmus mit der Datei "Galois/Counter Mode Encryption with Validation" erstellen Sie eine [cnggcmauthenticatedencryptionsettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) -Instanz, die die algorithmischen Informationen enthält:

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
> Der Algorithmus für symmetrische Blockchiffre muss eine Schlüssellänge von > = 128 Bits, eine Blockgröße von genau 128 Bits und eine GCM-Verschlüsselung unterstützen. Sie können die Eigenschaft " [verschlüsselungalgorithmprovider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) " auf NULL festlegen, um den Standardanbieter für den angegebenen Algorithmus zu verwenden. Weitere Informationen finden Sie in der Dokumentation zu [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/library/windows/desktop/aa375479(v=vs.85).aspx) .

### <a name="specifying-other-custom-algorithms"></a>Angeben anderer benutzerdefinierter Algorithmen

Obwohl das Datenschutzsystem nicht als erstklassige API verfügbar gemacht wird, ist es erweiterbar, um eine beliebige Art von Algorithmus angeben zu können. Beispielsweise ist es möglich, alle Schlüssel, die in einem Hardware Sicherheitsmodul (HSM) enthalten sind, beizubehalten und eine benutzerdefinierte Implementierung der wichtigsten Verschlüsselungs-und Entschlüsselungs Routinen bereitzustellen. Weitere Informationen finden Sie unter [iauthenticatedencryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) in der [zentralen kryptografieerweiterbarkeit](xref:security/data-protection/extensibility/core-crypto) .

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a>Beibehalten von Schlüsseln beim Hosting in einem docker-Container

Beim Hosting in einem [docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) -Container sollten Schlüssel in folgenden Umgebungen verwaltet werden:

* Ein Ordner, bei dem es sich um ein docker-Volume handelt, das über die Lebensdauer des Containers hinaus beibehalten wird, z. b. ein frei gegebenes Volume oder ein Host
* Ein externer Anbieter, z. b. [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) oder [redis](https://redis.io/).

## <a name="persisting-keys-with-redis"></a>Beibehalten von Schlüsseln mit redis

Zum Speichern von Schlüsseln sollten nur redis-Versionen verwendet werden, die [redis-Daten Persistenz](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) unterstützen. [Azure BLOB Storage](/azure/storage/blobs/storage-blobs-introduction) ist persistent und kann zum Speichern von Schlüsseln verwendet werden. Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/aspnet/AspNetCore/issues/13476).

## <a name="additional-resources"></a>Weitere Ressourcen

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
