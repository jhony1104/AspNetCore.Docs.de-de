---
title: Migrieren der Konfiguration zu ASP.NET Core
author: ardalis
description: Informationen Sie zum Migrieren der Konfiguration in einem ASP.NET MVC-Projekt zu einem ASP.NET Core MVC-Projekt.
ms.author: riande
ms.date: 10/14/2016
uid: migration/configuration
ms.openlocfilehash: c379f1f64dc5ab8aeb48055124e86e4e60d93785
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894927"
---
# <a name="migrate-configuration-to-aspnet-core"></a>Migrieren der Konfiguration zu ASP.NET Core

Von [Steve Smith](https://ardalis.com/) und [Scott Addie](https://scottaddie.com)

Im vorherigen Artikel wir zu Beginn der [migrieren ein ASP.NET MVC-Projekts zu ASP.NET Core MVC](xref:migration/mvc). In diesem Artikel migrieren wir die Konfiguration.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="setup-configuration"></a>Setup-Konfiguration

ASP.NET Core nicht mehr verwendet die *"Global.asax"* und *"Web.config"* Dateien, die frühere Versionen von ASP.NET verwendet. In früheren Versionen von ASP.NET Startlogik der Anwendung platziert wurde, eine `Application_StartUp` -Methode im *"Global.asax"*. Weiter unten in ASP.NET MVC, eine *"Startup.cs"* Datei im Stammverzeichnis des Projekts; enthalten war, und es wurde aufgerufen, wenn die Anwendung gestartet. ASP.NET Core hat dieser Ansatz übernommen, in der vollständig von allen Startlogik in die *"Startup.cs"* Datei.

Die *"Web.config"* in ASP.NET Core wurde auch Datei ersetzt. Konfiguration selbst kann nun konfiguriert werden, und als Teil der Anwendung beim Start-Prozedur, die in beschriebenen *"Startup.cs"*. Konfiguration kann dennoch XML-Dateien nutzen, aber in der Regel ASP.NET Core-Projekten platziert den Werten in einer JSON-formatierte Datei, z. B. *"appSettings.JSON"*. ASP.NET Core-Konfigurationssystem kann auch auf einfache Weise Zugriff auf Umgebungsvariablen, die zur Verfügung stellen können eine [mehr sicherer und stabiler Speicherort](xref:security/app-secrets) für umgebungsspezifische Werte. Dies gilt insbesondere für geheime Daten wie Verbindungszeichenfolgen und API-Schlüssel, die in die quellcodeverwaltung eingecheckt werden, sollte nicht. Finden Sie unter [Konfiguration](xref:fundamentals/configuration/index) Weitere Informationen zur Konfiguration in ASP.NET Core.

In diesem Artikel beginnen wir mit teilweise migrierte ASP.NET Core-Projekt aus [im vorgängerartikel](xref:migration/mvc). Konfiguration einrichten, fügen Sie den folgenden Konstruktor und die Eigenschaft, um die *"Startup.cs"* -Datei im Stammverzeichnis des Projekts:

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

Beachten Sie, dass an diesem Punkt die *"Startup.cs"* Datei nicht kompiliert werden, wie wir weiterhin die folgenden müssen `using` Anweisung:

```csharp
using Microsoft.Extensions.Configuration;
```

Hinzufügen einer *"appSettings.JSON"* Datei im Stammverzeichnis des Projekts mit der entsprechenden Elementvorlage:

![Fügen Sie "appSettings" JSON](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a>Migrieren Sie die Konfigurationseinstellungen aus "Web.config"

Unser ASP.NET MVC-Projekt enthalten, die erforderliche Datenbank-Verbindungszeichenfolge in *"Web.config"* in die `<connectionStrings>` Element. In unserer ASP.NET Core-Projekts werden wir diese Informationen in den *"appSettings.JSON"* Datei. Open *"appSettings.JSON"*, und beachten Sie, dass sie bereits über Folgendes enthält:

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

Die hervorgehobene Zeile oben dargestellten, ändern den Namen der Datenbank von **_CHANGE_ME** auf den Namen der Datenbank.

## <a name="summary"></a>Zusammenfassung

ASP.NET Core platziert alle Startlogik für die Anwendung in einer einzelnen Datei, in der die erforderlichen Dienste und Abhängigkeiten definiert und konfiguriert werden können. Er ersetzt die *"Web.config"* -Datei mit der eine flexible Konfiguration-Funktion, die eine Vielzahl von Dateiformaten wie JSON als auch Umgebungsvariablen nutzen können.
