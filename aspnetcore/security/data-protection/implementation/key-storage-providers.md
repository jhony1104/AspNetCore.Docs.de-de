---
title: Softwareschlüsselspeicher-Anbieter in ASP.NET Core
author: rick-anderson
description: Informationen Sie zu den softwareschlüsselspeicher-Anbieter in ASP.NET Core und wichtige Speicherorte zu konfigurieren.
ms.author: riande
ms.date: 06/11/2019
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: 64c7e6b25d5b4acc72e96747a77826efaeb693fd
ms.sourcegitcommit: 335a88c1b6e7f0caa8a3a27db57c56664d676d34
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/12/2019
ms.locfileid: "67034766"
---
# <a name="key-storage-providers-in-aspnet-core"></a><span data-ttu-id="397ae-103">Softwareschlüsselspeicher-Anbieter in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="397ae-103">Key storage providers in ASP.NET Core</span></span>

<span data-ttu-id="397ae-104">Das System zum Schutz von Daten [Discovery-Mechanismus in der Standardeinstellung setzt](xref:security/data-protection/configuration/default-settings) um zu bestimmen, in denen kryptografische Schlüssel beibehalten werden soll.</span><span class="sxs-lookup"><span data-stu-id="397ae-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine where cryptographic keys should be persisted.</span></span> <span data-ttu-id="397ae-105">Der Entwickler kann außer Kraft setzen als standardmäßiger Ermittlungsmechanismus verwendet und geben Sie den Speicherort manuell.</span><span class="sxs-lookup"><span data-stu-id="397ae-105">The developer can override the default discovery mechanism and manually specify the location.</span></span>

> [!WARNING]
> <span data-ttu-id="397ae-106">Wenn Sie einem Standort der explizite schlüsselpersistenz angeben, hebt die System zum Schutz von Daten, sodass Schlüssel nicht mehr im Ruhezustand verschlüsselt sind die wichtigsten standardverschlüsselung auf Rest-Mechanismus, Registrierung.</span><span class="sxs-lookup"><span data-stu-id="397ae-106">If you specify an explicit key persistence location, the data protection system deregisters the default key encryption at rest mechanism, so keys are no longer encrypted at rest.</span></span> <span data-ttu-id="397ae-107">Es wird empfohlen, die Sie darüber hinaus [Geben Sie einen Mechanismus für die explizite Schlüsselverschlüsselung](xref:security/data-protection/implementation/key-encryption-at-rest) für produktionsbereitstellungen.</span><span class="sxs-lookup"><span data-stu-id="397ae-107">It's recommended that you additionally [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span>

## <a name="file-system"></a><span data-ttu-id="397ae-108">Dateisystem</span><span class="sxs-lookup"><span data-stu-id="397ae-108">File system</span></span>

<span data-ttu-id="397ae-109">Um eine Datei systembasierte Key Repository zu konfigurieren, rufen Sie die [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) Configuration-Routine, wie unten dargestellt.</span><span class="sxs-lookup"><span data-stu-id="397ae-109">To configure a file system-based key repository, call the [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) configuration routine as shown below.</span></span> <span data-ttu-id="397ae-110">Geben Sie einen [DirectoryInfo](/dotnet/api/system.io.directoryinfo) auf das Repository, in dem Schlüssel gespeichert werden soll:</span><span class="sxs-lookup"><span data-stu-id="397ae-110">Provide a [DirectoryInfo](/dotnet/api/system.io.directoryinfo) pointing to the repository where keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

<span data-ttu-id="397ae-111">Die `DirectoryInfo` kann in ein Verzeichnis auf dem lokalen Computer, oder es kann auf einen Ordner auf einer Netzwerkfreigabe verweisen.</span><span class="sxs-lookup"><span data-stu-id="397ae-111">The `DirectoryInfo` can point to a directory on the local machine, or it can point to a folder on a network share.</span></span> <span data-ttu-id="397ae-112">Wenn ein Verzeichnis auf dem lokalen Computer verweist (und das Szenario ist, dass nur die apps auf dem lokalen Computer Zugriff auf dieses Repository verwenden müssen) in Betracht [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (unter Windows), die Schlüssel im Ruhezustand verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="397ae-112">If pointing to a directory on the local machine (and the scenario is that only apps on the local machine require access to use this repository), consider using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (on Windows) to encrypt the keys at rest.</span></span> <span data-ttu-id="397ae-113">Erwägen Sie andernfalls die Verwendung einer [x. 509-Zertifikat](xref:security/data-protection/implementation/key-encryption-at-rest) Schlüsseln im ruhenden Zustand verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="397ae-113">Otherwise, consider using an [X.509 certificate](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt keys at rest.</span></span>

## <a name="azure-storage"></a><span data-ttu-id="397ae-114">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="397ae-114">Azure Storage</span></span>

<span data-ttu-id="397ae-115">Die [Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) Paket ermöglicht das Speichern von Data Protection-Schlüssel in Azure Blob Storage.</span><span class="sxs-lookup"><span data-stu-id="397ae-115">The [Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) package allows storing data protection keys in Azure Blob Storage.</span></span> <span data-ttu-id="397ae-116">Schlüssel können auf mehrere Instanzen einer Web-App gemeinsam genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="397ae-116">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="397ae-117">Apps können Authentifizierungscookies oder CSRF-Schutz über mehrere Server hinweg freigeben.</span><span class="sxs-lookup"><span data-stu-id="397ae-117">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

<span data-ttu-id="397ae-118">Zum Konfigurieren des Azure Blob Storage-Anbieters rufen Sie eine der der [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) Überladungen.</span><span class="sxs-lookup"><span data-stu-id="397ae-118">To configure the Azure Blob Storage provider, call one of the [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) overloads.</span></span> 

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

<span data-ttu-id="397ae-119">Wenn die Web-app als Azure-Dienst ausgeführt wird, Authentifizierungstoken automatisch erstellt werden, mithilfe von [ Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/).</span><span class="sxs-lookup"><span data-stu-id="397ae-119">If the web app is running as an Azure service, authentication tokens can be automatically created using [ Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/).</span></span> 

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

<span data-ttu-id="397ae-120">Finden Sie unter [Weitere Informationen zum Konfigurieren von Dienst-zu-Dienst-Authentifizierung.](/azure/key-vault/service-to-service-authentication)</span><span class="sxs-lookup"><span data-stu-id="397ae-120">See [more details about configuring service-to-service authentication.](/azure/key-vault/service-to-service-authentication)</span></span>

## <a name="redis"></a><span data-ttu-id="397ae-121">Redis</span><span class="sxs-lookup"><span data-stu-id="397ae-121">Redis</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="397ae-122">Die [Microsoft.AspNetCore.DataProtection.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) Paket ermöglicht das Speichern von Data Protection-Schlüssel in einem Redis-Cache.</span><span class="sxs-lookup"><span data-stu-id="397ae-122">The [Microsoft.AspNetCore.DataProtection.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) package allows storing data protection keys in a Redis cache.</span></span> <span data-ttu-id="397ae-123">Schlüssel können auf mehrere Instanzen einer Web-App gemeinsam genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="397ae-123">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="397ae-124">Apps können Authentifizierungscookies oder CSRF-Schutz über mehrere Server hinweg freigeben.</span><span class="sxs-lookup"><span data-stu-id="397ae-124">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="397ae-125">Die [Microsoft.AspNetCore.DataProtection.Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) Paket ermöglicht das Speichern von Data Protection-Schlüssel in einem Redis-Cache.</span><span class="sxs-lookup"><span data-stu-id="397ae-125">The [Microsoft.AspNetCore.DataProtection.Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) package allows storing data protection keys in a Redis cache.</span></span> <span data-ttu-id="397ae-126">Schlüssel können auf mehrere Instanzen einer Web-App gemeinsam genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="397ae-126">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="397ae-127">Apps können Authentifizierungscookies oder CSRF-Schutz über mehrere Server hinweg freigeben.</span><span class="sxs-lookup"><span data-stu-id="397ae-127">Apps can share authentication cookies or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="397ae-128">Um auf Redis konfigurieren, rufen Sie eine der der [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) Überladungen:</span><span class="sxs-lookup"><span data-stu-id="397ae-128">To configure on Redis, call one of the [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) overloads:</span></span>

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

<span data-ttu-id="397ae-129">Um auf Redis konfigurieren, rufen Sie eine der der [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) Überladungen:</span><span class="sxs-lookup"><span data-stu-id="397ae-129">To configure on Redis, call one of the [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

<span data-ttu-id="397ae-130">Weitere Informationen finden Sie unter den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="397ae-130">For more information, see the following topics:</span></span>

* [<span data-ttu-id="397ae-131">"Stackexchange.redis" ConnectionMultiplexer</span><span class="sxs-lookup"><span data-stu-id="397ae-131">StackExchange.Redis ConnectionMultiplexer</span></span>](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [<span data-ttu-id="397ae-132">Azure Redis Cache</span><span class="sxs-lookup"><span data-stu-id="397ae-132">Azure Redis Cache</span></span>](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [<span data-ttu-id="397ae-133">ASPNET/DataProtection-Beispiele</span><span class="sxs-lookup"><span data-stu-id="397ae-133">aspnet/DataProtection samples</span></span>](https://github.com/aspnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a><span data-ttu-id="397ae-134">Registrierung</span><span class="sxs-lookup"><span data-stu-id="397ae-134">Registry</span></span>

<span data-ttu-id="397ae-135">**Gilt nur für Windows-Bereitstellungen.**</span><span class="sxs-lookup"><span data-stu-id="397ae-135">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="397ae-136">Die app möglicherweise manchmal nicht über Schreibzugriff auf das Dateisystem.</span><span class="sxs-lookup"><span data-stu-id="397ae-136">Sometimes the app might not have write access to the file system.</span></span> <span data-ttu-id="397ae-137">Stellen Sie sich ein Szenario, in denen eine app als ein virtuelles Dienstkonto ausgeführt wird (z. B. *w3wp.exe*des app-Pool-Identität).</span><span class="sxs-lookup"><span data-stu-id="397ae-137">Consider a scenario where an app is running as a virtual service account (such as *w3wp.exe*'s app pool identity).</span></span> <span data-ttu-id="397ae-138">In diesen Fällen kann der Administrator einen Registrierungsschlüssel bereitstellen, der von der dienstkontoidentität zugänglich ist.</span><span class="sxs-lookup"><span data-stu-id="397ae-138">In these cases, the administrator can provision a registry key that's accessible by the service account identity.</span></span> <span data-ttu-id="397ae-139">Rufen Sie die [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) Erweiterungsmethode wie unten dargestellt.</span><span class="sxs-lookup"><span data-stu-id="397ae-139">Call the [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) extension method as shown below.</span></span> <span data-ttu-id="397ae-140">Geben Sie einen [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) verweist auf den Speicherort, in der kryptografischen Schlüssel gespeichert werden soll:</span><span class="sxs-lookup"><span data-stu-id="397ae-140">Provide a [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) pointing to the location where cryptographic keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
}
```

> [!IMPORTANT]
> <span data-ttu-id="397ae-141">Es wird empfohlen, [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) den Schlüsseln im ruhenden Zustand verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="397ae-141">We recommend using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt the keys at rest.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a><span data-ttu-id="397ae-142">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="397ae-142">Entity Framework Core</span></span>

<span data-ttu-id="397ae-143">Die [Microsoft.AspNetCore.DataProtection.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) -Paket bietet einen Mechanismus zum Speichern von Data Protection-Schlüssel in einer Datenbank mit Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="397ae-143">The [Microsoft.AspNetCore.DataProtection.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) package provides a mechanism for storing data protection keys to a database using Entity Framework Core.</span></span> <span data-ttu-id="397ae-144">Die `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` NuGet-Paket muss hinzugefügt werden der Projektdatei, es ist nicht Teil der [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="397ae-144">The `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` NuGet package must be added to the project file, it's not part of the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="397ae-145">Mit diesem Paket können Schlüssel über mehrere Instanzen einer Web-app hinweg freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="397ae-145">With this package, keys can be shared across multiple instances of a web app.</span></span>

<span data-ttu-id="397ae-146">Rufen Sie zum Konfigurieren des EF Core-Anbieters die [ `PersistKeysToDbContext<TContext>` ](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) Methode:</span><span class="sxs-lookup"><span data-stu-id="397ae-146">To configure the EF Core provider, call the [`PersistKeysToDbContext<TContext>`](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) method:</span></span>

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-15)]

<span data-ttu-id="397ae-147">Der generische Parameter, `TContext`, erben müssen ["DbContext"](/dotnet/api/microsoft.entityframeworkcore.dbcontext) und [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span><span class="sxs-lookup"><span data-stu-id="397ae-147">The generic parameter, `TContext`, must inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span></span>

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

<span data-ttu-id="397ae-148">Erstellen der `DataProtectionKeys` Tabelle.</span><span class="sxs-lookup"><span data-stu-id="397ae-148">Create the `DataProtectionKeys` table.</span></span> 

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="397ae-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="397ae-149">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="397ae-150">Führen Sie die folgenden Befehle in der **-Paket-Manager-Konsole** (PMC) im Fenster:</span><span class="sxs-lookup"><span data-stu-id="397ae-150">Execute the following commands in the **Package Manager Console** (PMC) window:</span></span>

```PowerShell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="397ae-151">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="397ae-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="397ae-152">Führen Sie die folgenden Befehle in einer Befehlsshell ein:</span><span class="sxs-lookup"><span data-stu-id="397ae-152">Execute the following commands in a command shell:</span></span>

```console
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

<span data-ttu-id="397ae-153">`MyKeysContext` ist die `DbContext` im obigen Codebeispiel definiert.</span><span class="sxs-lookup"><span data-stu-id="397ae-153">`MyKeysContext` is the `DbContext` defined in the preceding code sample.</span></span> <span data-ttu-id="397ae-154">Bei Verwendung einer `DbContext` mit einem anderen Namen ersetzen Ihrer `DbContext` für `MyKeysContext`.</span><span class="sxs-lookup"><span data-stu-id="397ae-154">If you're using a `DbContext` with a different name, substitute your `DbContext` name for `MyKeysContext`.</span></span>

<span data-ttu-id="397ae-155">Die  `DataProtectionKeys` /die überprüfungsentität-Klasse übernimmt die Struktur, in der folgenden Tabelle dargestellt.</span><span class="sxs-lookup"><span data-stu-id="397ae-155">The `DataProtectionKeys` class/entity adopts the structure shown in the following table.</span></span>

| <span data-ttu-id="397ae-156">Feld/Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="397ae-156">Property/Field</span></span> | <span data-ttu-id="397ae-157">CLR-Typ</span><span class="sxs-lookup"><span data-stu-id="397ae-157">CLR Type</span></span> | <span data-ttu-id="397ae-158">SQL-Typ</span><span class="sxs-lookup"><span data-stu-id="397ae-158">SQL Type</span></span>              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | <span data-ttu-id="397ae-159">`int`, Primärschlüssel, nicht null</span><span class="sxs-lookup"><span data-stu-id="397ae-159">`int`, PK, not null</span></span>   |
| `FriendlyName` | `string` | <span data-ttu-id="397ae-160">`nvarchar(MAX)`, null</span><span class="sxs-lookup"><span data-stu-id="397ae-160">`nvarchar(MAX)`, null</span></span> |
| `Xml`          | `string` | <span data-ttu-id="397ae-161">`nvarchar(MAX)`, null</span><span class="sxs-lookup"><span data-stu-id="397ae-161">`nvarchar(MAX)`, null</span></span> |

::: moniker-end

## <a name="custom-key-repository"></a><span data-ttu-id="397ae-162">Benutzerdefinierte Schlüssel-repository</span><span class="sxs-lookup"><span data-stu-id="397ae-162">Custom key repository</span></span>

<span data-ttu-id="397ae-163">Wenn die integrierte Mechanismen nicht geeignet sind, kann Entwickler ihre eigenen Schlüssel persistenzmechanismus angeben, durch Bereitstellen eines benutzerdefinierten [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).</span><span class="sxs-lookup"><span data-stu-id="397ae-163">If the in-box mechanisms aren't appropriate, the developer can specify their own key persistence mechanism by providing a custom [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).</span></span>
