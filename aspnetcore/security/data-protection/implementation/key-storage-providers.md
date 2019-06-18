---
title: Softwareschlüsselspeicher-Anbieter in ASP.NET Core
author: rick-anderson
description: Informationen Sie zu den softwareschlüsselspeicher-Anbieter in ASP.NET Core und wichtige Speicherorte zu konfigurieren.
ms.author: riande
ms.date: 06/11/2019
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: 19d51399e24d085f7c34f70098ca02cbba7a888f
ms.sourcegitcommit: 28a2874765cefe9eaa068dceb989a978ba2096aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67167040"
---
# <a name="key-storage-providers-in-aspnet-core"></a>Softwareschlüsselspeicher-Anbieter in ASP.NET Core

Das System zum Schutz von Daten [Discovery-Mechanismus in der Standardeinstellung setzt](xref:security/data-protection/configuration/default-settings) um zu bestimmen, in denen kryptografische Schlüssel beibehalten werden soll. Der Entwickler kann außer Kraft setzen als standardmäßiger Ermittlungsmechanismus verwendet und geben Sie den Speicherort manuell.

> [!WARNING]
> Wenn Sie einem Standort der explizite schlüsselpersistenz angeben, hebt die System zum Schutz von Daten, sodass Schlüssel nicht mehr im Ruhezustand verschlüsselt sind die wichtigsten standardverschlüsselung auf Rest-Mechanismus, Registrierung. Es wird empfohlen, die Sie darüber hinaus [Geben Sie einen Mechanismus für die explizite Schlüsselverschlüsselung](xref:security/data-protection/implementation/key-encryption-at-rest) für produktionsbereitstellungen.

## <a name="file-system"></a>Dateisystem

Um eine Datei systembasierte Key Repository zu konfigurieren, rufen Sie die [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) Configuration-Routine, wie unten dargestellt. Geben Sie einen [DirectoryInfo](/dotnet/api/system.io.directoryinfo) auf das Repository, in dem Schlüssel gespeichert werden soll:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

Die `DirectoryInfo` kann in ein Verzeichnis auf dem lokalen Computer, oder es kann auf einen Ordner auf einer Netzwerkfreigabe verweisen. Wenn ein Verzeichnis auf dem lokalen Computer verweist (und das Szenario ist, dass nur die apps auf dem lokalen Computer Zugriff auf dieses Repository verwenden müssen) in Betracht [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (unter Windows), die Schlüssel im Ruhezustand verschlüsselt. Erwägen Sie andernfalls die Verwendung einer [x. 509-Zertifikat](xref:security/data-protection/implementation/key-encryption-at-rest) Schlüsseln im ruhenden Zustand verschlüsselt.

## <a name="azure-storage"></a>Azure Storage

Die [Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) Paket ermöglicht das Speichern von Data Protection-Schlüssel in Azure Blob Storage. Schlüssel können auf mehrere Instanzen einer Web-App gemeinsam genutzt werden. Apps können Authentifizierungscookies oder CSRF-Schutz über mehrere Server hinweg freigeben.

Zum Konfigurieren des Azure Blob Storage-Anbieters rufen Sie eine der der [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) Überladungen.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

Wenn die Web-app als Azure-Dienst ausgeführt wird, Authentifizierungstoken automatisch erstellt werden, mithilfe von [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/).

```csharp
var tokenProvider = new AzureServiceTokenProvider();
var token = await tokenProvider.GetAccessTokenAsync("https://storage.azure.com/");
var credentials = new StorageCredentials(new TokenCredential(token));
var storageAccount = new CloudStorageAccount(credentials, "mystorageaccount", "core.windows.net", useHttps: true);
var client = storageAccount.CreateCloudBlobClient();
var container = client.GetContainerReference("my-key-container");

// optional - provision the container automatically
await container.CreateIfNotExistsAsync();

services.AddDataProtection()
    .PersistKeysToAzureBlobStorage(container, "keys.xml");
```

Finden Sie unter [Weitere Informationen zum Konfigurieren von Dienst-zu-Dienst-Authentifizierung.](/azure/key-vault/service-to-service-authentication)

## <a name="redis"></a>Redis

::: moniker range=">= aspnetcore-2.2"

Die [Microsoft.AspNetCore.DataProtection.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) Paket ermöglicht das Speichern von Data Protection-Schlüssel in einem Redis-Cache. Schlüssel können auf mehrere Instanzen einer Web-App gemeinsam genutzt werden. Apps können Authentifizierungscookies oder CSRF-Schutz über mehrere Server hinweg freigeben.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Die [Microsoft.AspNetCore.DataProtection.Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) Paket ermöglicht das Speichern von Data Protection-Schlüssel in einem Redis-Cache. Schlüssel können auf mehrere Instanzen einer Web-App gemeinsam genutzt werden. Apps können Authentifizierungscookies oder CSRF-Schutz über mehrere Server hinweg freigeben.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Um auf Redis konfigurieren, rufen Sie eine der der [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) Überladungen:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToStackExchangeRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Um auf Redis konfigurieren, rufen Sie eine der der [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) Überladungen:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

Weitere Informationen finden Sie unter den folgenden Themen:

* ["Stackexchange.redis" ConnectionMultiplexer](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [Azure Redis Cache](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [ASPNET/DataProtection-Beispiele](https://github.com/aspnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a>Registrierung

**Gilt nur für Windows-Bereitstellungen.**

Die app möglicherweise manchmal nicht über Schreibzugriff auf das Dateisystem. Stellen Sie sich ein Szenario, in denen eine app als ein virtuelles Dienstkonto ausgeführt wird (z. B. *w3wp.exe*des app-Pool-Identität). In diesen Fällen kann der Administrator einen Registrierungsschlüssel bereitstellen, der von der dienstkontoidentität zugänglich ist. Rufen Sie die [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) Erweiterungsmethode wie unten dargestellt. Geben Sie einen [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) verweist auf den Speicherort, in der kryptografischen Schlüssel gespeichert werden soll:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
}
```

> [!IMPORTANT]
> Es wird empfohlen, [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) den Schlüsseln im ruhenden Zustand verschlüsselt.

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a>Entity Framework Core

Die [Microsoft.AspNetCore.DataProtection.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) -Paket bietet einen Mechanismus zum Speichern von Data Protection-Schlüssel in einer Datenbank mit Entity Framework Core. Die `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` NuGet-Paket muss hinzugefügt werden der Projektdatei, es ist nicht Teil der [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app).

Mit diesem Paket können Schlüssel über mehrere Instanzen einer Web-app hinweg freigegeben werden.

Rufen Sie zum Konfigurieren des EF Core-Anbieters die [ `PersistKeysToDbContext<TContext>` ](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) Methode:

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-15)]

Der generische Parameter, `TContext`, erben müssen ["DbContext"](/dotnet/api/microsoft.entityframeworkcore.dbcontext) und implementieren Sie [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

Erstellen der `DataProtectionKeys` Tabelle.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Führen Sie die folgenden Befehle in der **-Paket-Manager-Konsole** (PMC) im Fenster:

```PowerShell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Führen Sie die folgenden Befehle in einer Befehlsshell ein:

```console
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

`MyKeysContext` ist die `DbContext` im obigen Codebeispiel definiert. Bei Verwendung einer `DbContext` mit einem anderen Namen ersetzen Ihrer `DbContext` für `MyKeysContext`.

Die  `DataProtectionKeys` /die überprüfungsentität-Klasse übernimmt die Struktur, in der folgenden Tabelle dargestellt.

| Feld/Eigenschaft | CLR-Typ | SQL-Typ              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | `int`, Primärschlüssel, nicht null   |
| `FriendlyName` | `string` | `nvarchar(MAX)`, null |
| `Xml`          | `string` | `nvarchar(MAX)`, null |

::: moniker-end

## <a name="custom-key-repository"></a>Benutzerdefinierte Schlüssel-repository

Wenn die integrierte Mechanismen nicht geeignet sind, kann Entwickler ihre eigenen Schlüssel persistenzmechanismus angeben, durch Bereitstellen eines benutzerdefinierten [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).
