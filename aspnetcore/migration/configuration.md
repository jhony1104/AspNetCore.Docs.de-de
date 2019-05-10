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
# <a name="migrate-configuration-to-aspnet-core"></a><span data-ttu-id="59f7b-103">Migrieren der Konfiguration zu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="59f7b-103">Migrate configuration to ASP.NET Core</span></span>

<span data-ttu-id="59f7b-104">Von [Steve Smith](https://ardalis.com/) und [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="59f7b-104">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="59f7b-105">Im vorherigen Artikel wir zu Beginn der [migrieren ein ASP.NET MVC-Projekts zu ASP.NET Core MVC](xref:migration/mvc).</span><span class="sxs-lookup"><span data-stu-id="59f7b-105">In the previous article, we began [migrate an ASP.NET MVC project to ASP.NET Core MVC](xref:migration/mvc).</span></span> <span data-ttu-id="59f7b-106">In diesem Artikel migrieren wir die Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="59f7b-106">In this article, we migrate configuration.</span></span>

<span data-ttu-id="59f7b-107">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="59f7b-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="setup-configuration"></a><span data-ttu-id="59f7b-108">Setup-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="59f7b-108">Setup configuration</span></span>

<span data-ttu-id="59f7b-109">ASP.NET Core nicht mehr verwendet die *"Global.asax"* und *"Web.config"* Dateien, die frühere Versionen von ASP.NET verwendet.</span><span class="sxs-lookup"><span data-stu-id="59f7b-109">ASP.NET Core no longer uses the *Global.asax* and *web.config* files that previous versions of ASP.NET utilized.</span></span> <span data-ttu-id="59f7b-110">In früheren Versionen von ASP.NET Startlogik der Anwendung platziert wurde, eine `Application_StartUp` -Methode im *"Global.asax"*.</span><span class="sxs-lookup"><span data-stu-id="59f7b-110">In earlier versions of ASP.NET, application startup logic was placed in an `Application_StartUp` method within *Global.asax*.</span></span> <span data-ttu-id="59f7b-111">Weiter unten in ASP.NET MVC, eine *"Startup.cs"* Datei im Stammverzeichnis des Projekts; enthalten war, und es wurde aufgerufen, wenn die Anwendung gestartet.</span><span class="sxs-lookup"><span data-stu-id="59f7b-111">Later, in ASP.NET MVC, a *Startup.cs* file was included in the root of the project; and, it was called when the application started.</span></span> <span data-ttu-id="59f7b-112">ASP.NET Core hat dieser Ansatz übernommen, in der vollständig von allen Startlogik in die *"Startup.cs"* Datei.</span><span class="sxs-lookup"><span data-stu-id="59f7b-112">ASP.NET Core has adopted this approach completely by placing all startup logic in the *Startup.cs* file.</span></span>

<span data-ttu-id="59f7b-113">Die *"Web.config"* in ASP.NET Core wurde auch Datei ersetzt.</span><span class="sxs-lookup"><span data-stu-id="59f7b-113">The *web.config* file has also been replaced in ASP.NET Core.</span></span> <span data-ttu-id="59f7b-114">Konfiguration selbst kann nun konfiguriert werden, und als Teil der Anwendung beim Start-Prozedur, die in beschriebenen *"Startup.cs"*.</span><span class="sxs-lookup"><span data-stu-id="59f7b-114">Configuration itself can now be configured, as part of the application startup procedure described in *Startup.cs*.</span></span> <span data-ttu-id="59f7b-115">Konfiguration kann dennoch XML-Dateien nutzen, aber in der Regel ASP.NET Core-Projekten platziert den Werten in einer JSON-formatierte Datei, z. B. *"appSettings.JSON"*.</span><span class="sxs-lookup"><span data-stu-id="59f7b-115">Configuration can still utilize XML files, but typically ASP.NET Core projects will place configuration values in a JSON-formatted file, such as *appsettings.json*.</span></span> <span data-ttu-id="59f7b-116">ASP.NET Core-Konfigurationssystem kann auch auf einfache Weise Zugriff auf Umgebungsvariablen, die zur Verfügung stellen können eine [mehr sicherer und stabiler Speicherort](xref:security/app-secrets) für umgebungsspezifische Werte.</span><span class="sxs-lookup"><span data-stu-id="59f7b-116">ASP.NET Core's configuration system can also easily access environment variables, which can provide a [more secure and robust location](xref:security/app-secrets) for environment-specific values.</span></span> <span data-ttu-id="59f7b-117">Dies gilt insbesondere für geheime Daten wie Verbindungszeichenfolgen und API-Schlüssel, die in die quellcodeverwaltung eingecheckt werden, sollte nicht.</span><span class="sxs-lookup"><span data-stu-id="59f7b-117">This is especially true for secrets like connection strings and API keys that shouldn't be checked into source control.</span></span> <span data-ttu-id="59f7b-118">Finden Sie unter [Konfiguration](xref:fundamentals/configuration/index) Weitere Informationen zur Konfiguration in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="59f7b-118">See [Configuration](xref:fundamentals/configuration/index) to learn more about configuration in ASP.NET Core.</span></span>

<span data-ttu-id="59f7b-119">In diesem Artikel beginnen wir mit teilweise migrierte ASP.NET Core-Projekt aus [im vorgängerartikel](xref:migration/mvc).</span><span class="sxs-lookup"><span data-stu-id="59f7b-119">For this article, we are starting with the partially migrated ASP.NET Core project from [the previous article](xref:migration/mvc).</span></span> <span data-ttu-id="59f7b-120">Konfiguration einrichten, fügen Sie den folgenden Konstruktor und die Eigenschaft, um die *"Startup.cs"* -Datei im Stammverzeichnis des Projekts:</span><span class="sxs-lookup"><span data-stu-id="59f7b-120">To setup configuration, add the following constructor and property to the *Startup.cs* file located in the root of the project:</span></span>

[!code-csharp[](configuration/samples/WebApp1/src/WebApp1/Startup.cs?range=11-16)]

<span data-ttu-id="59f7b-121">Beachten Sie, dass an diesem Punkt die *"Startup.cs"* Datei nicht kompiliert werden, wie wir weiterhin die folgenden müssen `using` Anweisung:</span><span class="sxs-lookup"><span data-stu-id="59f7b-121">Note that at this point, the *Startup.cs* file won't compile, as we still need to add the following `using` statement:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="59f7b-122">Hinzufügen einer *"appSettings.JSON"* Datei im Stammverzeichnis des Projekts mit der entsprechenden Elementvorlage:</span><span class="sxs-lookup"><span data-stu-id="59f7b-122">Add an *appsettings.json* file to the root of the project using the appropriate item template:</span></span>

![Fügen Sie "appSettings" JSON](configuration/_static/add-appsettings-json.png)

## <a name="migrate-configuration-settings-from-webconfig"></a><span data-ttu-id="59f7b-124">Migrieren Sie die Konfigurationseinstellungen aus "Web.config"</span><span class="sxs-lookup"><span data-stu-id="59f7b-124">Migrate configuration settings from web.config</span></span>

<span data-ttu-id="59f7b-125">Unser ASP.NET MVC-Projekt enthalten, die erforderliche Datenbank-Verbindungszeichenfolge in *"Web.config"* in die `<connectionStrings>` Element.</span><span class="sxs-lookup"><span data-stu-id="59f7b-125">Our ASP.NET MVC project included the required database connection string in *web.config*, in the `<connectionStrings>` element.</span></span> <span data-ttu-id="59f7b-126">In unserer ASP.NET Core-Projekts werden wir diese Informationen in den *"appSettings.JSON"* Datei.</span><span class="sxs-lookup"><span data-stu-id="59f7b-126">In our ASP.NET Core project, we are going to store this information in the *appsettings.json* file.</span></span> <span data-ttu-id="59f7b-127">Open *"appSettings.JSON"*, und beachten Sie, dass sie bereits über Folgendes enthält:</span><span class="sxs-lookup"><span data-stu-id="59f7b-127">Open *appsettings.json*, and note that it already includes the following:</span></span>

[!code-json[](../migration/configuration/samples/WebApp1/src/WebApp1/appsettings.json?highlight=4)]

<span data-ttu-id="59f7b-128">Die hervorgehobene Zeile oben dargestellten, ändern den Namen der Datenbank von **_CHANGE_ME** auf den Namen der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="59f7b-128">In the highlighted line depicted above, change the name of the database from **_CHANGE_ME** to the name of your database.</span></span>

## <a name="summary"></a><span data-ttu-id="59f7b-129">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="59f7b-129">Summary</span></span>

<span data-ttu-id="59f7b-130">ASP.NET Core platziert alle Startlogik für die Anwendung in einer einzelnen Datei, in der die erforderlichen Dienste und Abhängigkeiten definiert und konfiguriert werden können.</span><span class="sxs-lookup"><span data-stu-id="59f7b-130">ASP.NET Core places all startup logic for the application in a single file, in which the necessary services and dependencies can be defined and configured.</span></span> <span data-ttu-id="59f7b-131">Er ersetzt die *"Web.config"* -Datei mit der eine flexible Konfiguration-Funktion, die eine Vielzahl von Dateiformaten wie JSON als auch Umgebungsvariablen nutzen können.</span><span class="sxs-lookup"><span data-stu-id="59f7b-131">It replaces the *web.config* file with a flexible configuration feature that can leverage a variety of file formats, such as JSON, as well as environment variables.</span></span>
