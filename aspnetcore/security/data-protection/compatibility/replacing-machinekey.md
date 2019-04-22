---
title: Ersetzen Sie den ASP.NET MachineKey in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie zum Ersetzen von MachineKey in ASP.NET für die Verwendung eines Systems, neue und sicherer Daten Schutz zu ermöglichen.
ms.author: riande
ms.date: 04/06/2019
uid: security/data-protection/compatibility/replacing-machinekey
ms.openlocfilehash: 2317cb50cfe63226baf336ebfc5d681d1cebe5c6
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59705548"
---
# <a name="replace-the-aspnet-machinekey-in-aspnet-core"></a>Ersetzen Sie den ASP.NET MachineKey in ASP.NET Core

<a name="compatibility-replacing-machinekey"></a>

Die Implementierung der `<machineKey>` Element in ASP.NET [Standardversion](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/). Dadurch werden die meisten Aufrufe zum ASP.NET kryptografischen Routinen über einen Ersatz Data Protection-Mechanismus, einschließlich der neuen System zum Schutz von Daten weitergeleitet werden.

## <a name="package-installation"></a>Paketinstallation

> [!NOTE]
> Das neue System zum Schutz von Daten kann nur in eine vorhandene ASP.NET-Anwendung für .NET 4.5.1 oder höher ausgeführt werden. Installation wird fehlschlagen, wenn die Anwendung auf .NET 4.5 ausgerichtet ist oder zu verringern.

Um das neue System zum Schutz von Daten in ein vorhandenes ASP.NET-Projekt 4.5.1+ installieren zu können, müssen installieren Sie das Paket Microsoft.AspNetCore.DataProtection.SystemWeb. Dies wird instanziiert, die mithilfe von Data Protection System die [Standardkonfiguration](xref:security/data-protection/configuration/default-settings) Einstellungen.

Wenn Sie das Paket installieren, fügt eine Zeile in *"Web.config"* , informiert, dass es für die Verwendung in ASP.NET [die meisten kryptografischen Vorgänge](https://blogs.msdn.microsoft.com/webdev/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2/), einschließlich der Formularauthentifizierung, Ansichtszustand und Aufrufe an MachineKey.Protect. Die Zeile, die eingefügt wird, lautet wie folgt.

```xml
<machineKey compatibilityMode="Framework45" dataProtectorType="..." />
```

>[!TIP]
> Sie können feststellen, ob das neue System zum Schutz von Daten aktiv ist, durch Überprüfen der Felder wie `__VIEWSTATE`, die mit "CfDJ8" wie im folgenden Beispiel beginnen soll. "CfDJ8" ist die base64-Darstellung der Magic-Befehl "09 F0 C9 F0"-Header, der eine Nutzlast, die durch das System zum Schutz von Daten geschützt identifiziert.

```html
<input type="hidden" name="__VIEWSTATE" id="__VIEWSTATE" value="CfDJ8AWPr2EQPTBGs3L2GCZOpk...">
```

## <a name="package-configuration"></a>Paketkonfiguration

Das System zum Schutz von Daten wird mit einer NULL-Setup-Standardkonfiguration instanziiert. Aber da standardmäßig Schlüssel im lokalen Dateisystem beibehalten werden, funktioniert dies Anwendungen nicht für die in einer Farm bereitgestellt werden. Dieses Problem lösen, müssen Sie Configuration können bereitstellen werden, indem Sie einen Typ erstellen, die Unterklassen DataProtectionStartup und überschreibt die Methode "configureservices".

Im folgenden finden Sie ein Beispiel für einen benutzerdefinierten Schutz beim Start Datentyp, der konfiguriert werden, sowohl, wo der Schlüssel gespeichert sind und wie sie im Ruhezustand verschlüsselt sind. Dabei wird auch die Isolation für Apps durch einen eigenen Anwendungsnamen bereitstellen.

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
> Sie können auch `<machineKey applicationName="my-app" ... />` anstelle eines expliziten Aufrufs von SetApplicationName. Dies ist ein Benutzerfreundlicher Mechanismus vermieden, dass den Entwickler einen DataProtectionStartup abgeleiteten Typen zu erstellen, wenn wurde alles, was sie verwenden wollten, konfigurieren Sie den Namen der Anwendung festlegen.

Um diese benutzerdefinierte Konfiguration zu aktivieren, wechseln Sie zurück zur Datei "Web.config", und suchen Sie nach der `<appSettings>` Element, das das Paket zu installieren, auf die Config-Datei hinzugefügt. Es sieht so aus wie das folgende Markup:

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

Geben Sie den leeren Wert mit dem Namen mit assemblyqualifikation des DataProtectionStartup abgeleiteten Typs, die Sie gerade erstellt haben. Wenn der Name der Anwendung DataProtectionDemo ist, sieht dies wie die unten.

```xml
<add key="aspnet:dataProtectionStartupType"
     value="DataProtectionDemo.MyDataProtectionStartup, DataProtectionDemo" />
```

Das System zum Schutz von Daten neu konfigurierten kann jetzt für die Verwendung in der Anwendung.
