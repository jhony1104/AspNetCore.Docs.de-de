---
title: Ersetzen Sie den ASP.NET MachineKey in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie zum Ersetzen von MachineKey in ASP.NET für die Verwendung eines Systems, neue und sicherer Daten Schutz zu ermöglichen.
ms.author: riande
ms.date: 04/06/2019
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: 2317cb50cfe63226baf336ebfc5d681d1cebe5c6
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64895337"
---
# <a name="replace-the-aspnet-machinekey-in-aspnet-core"></a><span data-ttu-id="376a0-103">Ersetzen Sie den ASP.NET MachineKey in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="376a0-103">Replace the ASP.NET machineKey in ASP.NET Core</span></span>

<a name="compatibility-replacing-machinekey"></a>

<span data-ttu-id="376a0-104">Die Implementierung der `<machineKey>` Element in ASP.NET [Standardversion](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/).</span><span class="sxs-lookup"><span data-stu-id="376a0-104">The implementation of the `<machineKey>` element in ASP.NET [is replaceable](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/).</span></span> <span data-ttu-id="376a0-105">Dadurch werden die meisten Aufrufe zum ASP.NET kryptografischen Routinen über einen Ersatz Data Protection-Mechanismus, einschließlich der neuen System zum Schutz von Daten weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="376a0-105">This allows most calls to ASP.NET cryptographic routines to be routed through a replacement data protection mechanism, including the new data protection system.</span></span>

## <a name="package-installation"></a><span data-ttu-id="376a0-106">Paketinstallation</span><span class="sxs-lookup"><span data-stu-id="376a0-106">Package installation</span></span>

> [!NOTE]
> <span data-ttu-id="376a0-107">Das neue System zum Schutz von Daten kann nur in eine vorhandene ASP.NET-Anwendung für .NET 4.5.1 oder höher ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="376a0-107">The new data protection system can only be installed into an existing ASP.NET application targeting .NET 4.5.1 or later.</span></span> <span data-ttu-id="376a0-108">Installation wird fehlschlagen, wenn die Anwendung auf .NET 4.5 ausgerichtet ist oder zu verringern.</span><span class="sxs-lookup"><span data-stu-id="376a0-108">Installation will fail if the application targets .NET 4.5 or lower.</span></span>

<span data-ttu-id="376a0-109">Um das neue System zum Schutz von Daten in ein vorhandenes ASP.NET-Projekt 4.5.1+ installieren zu können, müssen installieren Sie das Paket Microsoft.AspNetCore.DataProtection.SystemWeb.</span><span class="sxs-lookup"><span data-stu-id="376a0-109">To install the new data protection system into an existing ASP.NET 4.5.1+ project, install the package Microsoft.AspNetCore.DataProtection.SystemWeb.</span></span> <span data-ttu-id="376a0-110">Dies wird instanziiert, die mithilfe von Data Protection System die [Standardkonfiguration](xref:security/data-protection/configuration/default-settings) Einstellungen.</span><span class="sxs-lookup"><span data-stu-id="376a0-110">This will instantiate the data protection system using the [default configuration](xref:security/data-protection/configuration/default-settings) settings.</span></span>

<span data-ttu-id="376a0-111">Wenn Sie das Paket installieren, fügt eine Zeile in *"Web.config"* , informiert, dass es für die Verwendung in ASP.NET [die meisten kryptografischen Vorgänge](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), einschließlich der Formularauthentifizierung, Ansichtszustand und Aufrufe an MachineKey.Protect.</span><span class="sxs-lookup"><span data-stu-id="376a0-111">When you install the package, it inserts a line into *Web.config* that tells ASP.NET to use it for [most cryptographic operations](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), including forms authentication, view state, and calls to MachineKey.Protect.</span></span> <span data-ttu-id="376a0-112">Die Zeile, die eingefügt wird, lautet wie folgt.</span><span class="sxs-lookup"><span data-stu-id="376a0-112">The line that's inserted reads as follows.</span></span>

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> <span data-ttu-id="376a0-113">Sie können feststellen, ob das neue System zum Schutz von Daten aktiv ist, durch Überprüfen der Felder wie `__VIEWSTATE`, die mit "CfDJ8" wie im folgenden Beispiel beginnen soll.</span><span class="sxs-lookup"><span data-stu-id="376a0-113">You can tell if the new data protection system is active by inspecting fields like `__VIEWSTATE`, which should begin with "CfDJ8" as in the example below.</span></span> <span data-ttu-id="376a0-114">"CfDJ8" ist die base64-Darstellung der Magic-Befehl "09 F0 C9 F0"-Header, der eine Nutzlast, die durch das System zum Schutz von Daten geschützt identifiziert.</span><span class="sxs-lookup"><span data-stu-id="376a0-114">"CfDJ8" is the base64 representation of the magic "09 F0 C9 F0" header that identifies a payload protected by the data protection system.</span></span>

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk...">
```

## <a name="package-configuration"></a><span data-ttu-id="376a0-115">Paketkonfiguration</span><span class="sxs-lookup"><span data-stu-id="376a0-115">Package configuration</span></span>

<span data-ttu-id="376a0-116">Das System zum Schutz von Daten wird mit einer NULL-Setup-Standardkonfiguration instanziiert.</span><span class="sxs-lookup"><span data-stu-id="376a0-116">The data protection system is instantiated with a default zero-setup configuration.</span></span> <span data-ttu-id="376a0-117">Aber da standardmäßig Schlüssel im lokalen Dateisystem beibehalten werden, funktioniert dies Anwendungen nicht für die in einer Farm bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="376a0-117">However, since by default keys are persisted to the local file system, this won't work for applications which are deployed in a farm.</span></span> <span data-ttu-id="376a0-118">Dieses Problem lösen, müssen Sie Configuration können bereitstellen werden, indem Sie einen Typ erstellen, die Unterklassen DataProtectionStartup und überschreibt die Methode "configureservices".</span><span class="sxs-lookup"><span data-stu-id="376a0-118">To resolve this, you can provide configuration by creating a type which subclasses DataProtectionStartup and overrides its ConfigureServices method.</span></span>

<span data-ttu-id="376a0-119">Im folgenden finden Sie ein Beispiel für einen benutzerdefinierten Schutz beim Start Datentyp, der konfiguriert werden, sowohl, wo der Schlüssel gespeichert sind und wie sie im Ruhezustand verschlüsselt sind.</span><span class="sxs-lookup"><span data-stu-id="376a0-119">Below is an example of a custom data protection startup type which configured both where keys are persisted and how they're encrypted at rest.</span></span> <span data-ttu-id="376a0-120">Dabei wird auch die Isolation für Apps durch einen eigenen Anwendungsnamen bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="376a0-120">It also overrides the default app isolation policy by providing its own application name.</span></span>

```csharp
using System;
using System.IO;
using Microsoft.AspNetCore.DataProtection;
using Microsoft.AspNetCore.DataProtection.SystemWeb;
using Microsoft.Extensions.DependencyInjection;

namespace DataProtectionDemo
{
    public class MyDataProtectionStartup : DataProtectionStartup
    {
        public override void ConfigureServices(IServiceCollection services)
        {
            services.AddDataProtection()
                .SetApplicationName("my-app")
                .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\myapp-keys\"))
                .ProtectKeysWithCertificate("thumbprint");
        }
    }
}
```

>[!TIP]
> <span data-ttu-id="376a0-121">Sie können auch `<machineKey applicationName="my-app" ... />` anstelle eines expliziten Aufrufs von SetApplicationName.</span><span class="sxs-lookup"><span data-stu-id="376a0-121">You can also use `<machineKey applicationName="my-app" ... />` in place of an explicit call to SetApplicationName.</span></span> <span data-ttu-id="376a0-122">Dies ist ein Benutzerfreundlicher Mechanismus vermieden, dass den Entwickler einen DataProtectionStartup abgeleiteten Typen zu erstellen, wenn wurde alles, was sie verwenden wollten, konfigurieren Sie den Namen der Anwendung festlegen.</span><span class="sxs-lookup"><span data-stu-id="376a0-122">This is a convenience mechanism to avoid forcing the developer to create a DataProtectionStartup-derived type if all they wanted to configure was setting the application name.</span></span>

<span data-ttu-id="376a0-123">Um diese benutzerdefinierte Konfiguration zu aktivieren, wechseln Sie zurück zur Datei "Web.config", und suchen Sie nach der `<appSettings>` Element, das das Paket zu installieren, auf die Config-Datei hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="376a0-123">To enable this custom configuration, go back to Web.config and look for the `<appSettings>` element that the package install added to the config file.</span></span> <span data-ttu-id="376a0-124">Es sieht so aus wie das folgende Markup:</span><span class="sxs-lookup"><span data-stu-id="376a0-124">It will look like the following markup:</span></span>

```xml
<appSettings>
  <!--
  If you want to customize the behavior of the ASP.NET Core Data Protection stack, set the
  "aspnet:dataProtectionStartupType" switch below to be the fully-qualified name of a
  type which subclasses Microsoft.AspNetCore.DataProtection.SystemWeb.DataProtectionStartup.
  -->
  <add key="aspnet:dataProtectionStartupType" value="" />
</appSettings>
```

<span data-ttu-id="376a0-125">Geben Sie den leeren Wert mit dem Namen mit assemblyqualifikation des DataProtectionStartup abgeleiteten Typs, die Sie gerade erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="376a0-125">Fill in the blank value with the assembly-qualified name of the DataProtectionStartup-derived type you just created.</span></span> <span data-ttu-id="376a0-126">Wenn der Name der Anwendung DataProtectionDemo ist, sieht dies wie die unten.</span><span class="sxs-lookup"><span data-stu-id="376a0-126">If the name of the application is DataProtectionDemo, this would look like the below.</span></span>

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

<span data-ttu-id="376a0-127">Das System zum Schutz von Daten neu konfigurierten kann jetzt für die Verwendung in der Anwendung.</span><span class="sxs-lookup"><span data-stu-id="376a0-127">The newly-configured data protection system is now ready for use inside the application.</span></span>
