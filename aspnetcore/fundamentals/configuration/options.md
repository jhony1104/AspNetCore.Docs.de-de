---
title: Optionsmuster in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie Sie Optionsmuster verwenden, um Gruppen von zusammengehörigen Einstellungen in ASP.NET Core-Anwendungen darzustellen.
ms.author: riande
ms.custom: mvc
ms.date: 11/28/2018
uid: fundamentals/configuration/options
ms.openlocfilehash: 0e3784de18be16e3217a015dd94f1b43b6621c1c
ms.sourcegitcommit: 68a3081dd175d6518d1bfa31b4712bd8a2dd3864
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53577889"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="3051a-103">Optionsmuster in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3051a-103">Options pattern in ASP.NET Core</span></span>

<span data-ttu-id="3051a-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3051a-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="3051a-105">Laden Sie die Version 1.1 dieses Artikel unter [Optionsmuster in ASP.NET Core (Version 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/Options_1.1.pdf) herunter.</span><span class="sxs-lookup"><span data-stu-id="3051a-105">For the 1.1 version of this topic, download [Options pattern in ASP.NET Core (version 1.1, PDF)](https://webpifeed.blob.core.windows.net/webpifeed/Partners/Options_1.1.pdf).</span></span>

::: moniker-end

<span data-ttu-id="3051a-106">Das Optionsmuster verwendet Klassen, um Gruppen von zusammengehörigen Einstellungen darzustellen.</span><span class="sxs-lookup"><span data-stu-id="3051a-106">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="3051a-107">Wenn [Konfigurationseinstellungen](xref:fundamentals/configuration/index) nach Szenario in separate Klassen isoliert werden, entspricht die Anwendung zwei wichtigen Prinzipien der Softwareentwicklung:</span><span class="sxs-lookup"><span data-stu-id="3051a-107">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="3051a-108">[Schnittstellentrennungsprinzip (ISP) oder Kapselung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): &ndash;-Szenarios (Klassen), die von Konfigurationseinstellungen abhängen, sind nur von den Konfigurationseinstellungen abhängig, die sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="3051a-108">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="3051a-109">[Trennung von Bereichen](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash;: Einstellungen für die verschiedenen Teile der Anwendung hängen nicht voneinander ab und sind nicht gekoppelt.</span><span class="sxs-lookup"><span data-stu-id="3051a-109">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="3051a-110">Optionen bieten auch einen Mechanismus, um Konfigurationsdaten zu validieren.</span><span class="sxs-lookup"><span data-stu-id="3051a-110">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="3051a-111">Weitere Informationen finden Sie im Abschnitt [Optionsvalidierung](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="3051a-111">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="3051a-112">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3051a-112">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3051a-113">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="3051a-113">Prerequisites</span></span>

<span data-ttu-id="3051a-114">Verweisen Sie auf das [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app), oder fügen Sie einen Paketverweis auf das Paket [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) hinzu.</span><span class="sxs-lookup"><span data-stu-id="3051a-114">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="3051a-115">Optionenschnittstellen</span><span class="sxs-lookup"><span data-stu-id="3051a-115">Options interfaces</span></span>

<span data-ttu-id="3051a-116"><xref:Microsoft.Extensions.Options.IOptionsMonitor`1> wird verwendet, um Optionen abzurufen und Benachrichtigungen über Optionen für `TOptions`-Instanzen zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="3051a-116"><xref:Microsoft.Extensions.Options.IOptionsMonitor`1> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="3051a-117"><xref:Microsoft.Extensions.Options.IOptionsMonitor`1> unterstützt die folgenden Szenarios:</span><span class="sxs-lookup"><span data-stu-id="3051a-117"><xref:Microsoft.Extensions.Options.IOptionsMonitor`1> supports the following scenarios:</span></span>

* <span data-ttu-id="3051a-118">Änderungsbenachrichtigungen</span><span class="sxs-lookup"><span data-stu-id="3051a-118">Change notifications</span></span>
* [<span data-ttu-id="3051a-119">Benannte Optionen</span><span class="sxs-lookup"><span data-stu-id="3051a-119">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="3051a-120">Erneut ladbare Konfiguration</span><span class="sxs-lookup"><span data-stu-id="3051a-120">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="3051a-121">Selektive Optionsvalidierung (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1>)</span><span class="sxs-lookup"><span data-stu-id="3051a-121">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1>)</span></span>

<span data-ttu-id="3051a-122">Mit [Postkonfigurationsszenarien](#options-post-configuration) können Sie Optionen festlegen oder ändern, nachdem alle <xref:Microsoft.Extensions.Options.IConfigureOptions`1>-Konfigurationen durchgeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="3051a-122">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions`1> configuration occurs.</span></span>

<span data-ttu-id="3051a-123"><xref:Microsoft.Extensions.Options.IOptionsFactory`1> ist für das Erstellen neuer Optionsinstanzen zuständig.</span><span class="sxs-lookup"><span data-stu-id="3051a-123"><xref:Microsoft.Extensions.Options.IOptionsFactory`1> is responsible for creating new options instances.</span></span> <span data-ttu-id="3051a-124">Es verfügt über eine einzelne <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>-Methode.</span><span class="sxs-lookup"><span data-stu-id="3051a-124">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="3051a-125">Die Standardimplementierung akzeptiert alle registrierten <xref:Microsoft.Extensions.Options.IConfigureOptions`1> und <xref:Microsoft.Extensions.Options.IPostConfigureOptions`1> und führt alle Konfigurationen zuerst und die Postkonfigurationen danach aus.</span><span class="sxs-lookup"><span data-stu-id="3051a-125">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions`1> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions`1> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="3051a-126">Es wird zwischen <xref:Microsoft.Extensions.Options.IConfigureNamedOptions`1> und <xref:Microsoft.Extensions.Options.IConfigureOptions`1> unterschieden, und es werden nur die entsprechenden Schnittstellen aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="3051a-126">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions`1> and <xref:Microsoft.Extensions.Options.IConfigureOptions`1> and only calls the appropriate interface.</span></span>

<span data-ttu-id="3051a-127"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1> wird von <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> zum Zwischenspeichern der `TOptions`-Instanzen verwendet.</span><span class="sxs-lookup"><span data-stu-id="3051a-127"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> to cache `TOptions` instances.</span></span> <span data-ttu-id="3051a-128"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1> erklärt Optionsinstanzen im Monitor für ungültig, sodass der Wert neu berechnet wird (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="3051a-128">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="3051a-129">Werte können manuell mit <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> eingeführt werden.</span><span class="sxs-lookup"><span data-stu-id="3051a-129">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="3051a-130">Die <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>-Methode wird verwendet, wenn alle benannten Instanzen bei Bedarf neu erstellt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="3051a-130">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="3051a-131"><xref:Microsoft.Extensions.Options.IOptionsSnapshot`1> ist nützlich in Szenarien, in denen Optionen bei jeder Anforderung neu berechnet werden sollten.</span><span class="sxs-lookup"><span data-stu-id="3051a-131"><xref:Microsoft.Extensions.Options.IOptionsSnapshot`1> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="3051a-132">Weitere Informationen finden Sie im Abschnitt [Neuladen der Konfigurationsdaten mit IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="3051a-132">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="3051a-133"><xref:Microsoft.Extensions.Options.IOptions`1> kann zum Unterstützen von Optionen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="3051a-133"><xref:Microsoft.Extensions.Options.IOptions`1> can be used to support options.</span></span> <span data-ttu-id="3051a-134">Allerdings unterstützt <xref:Microsoft.Extensions.Options.IOptions`1> nicht die oben beschriebenen Szenarios von <xref:Microsoft.Extensions.Options.IOptionsMonitor`1>.</span><span class="sxs-lookup"><span data-stu-id="3051a-134">However, <xref:Microsoft.Extensions.Options.IOptions`1> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1>.</span></span> <span data-ttu-id="3051a-135">Sie können <xref:Microsoft.Extensions.Options.IOptions`1> weiterhin in bestehenden Frameworks und Bibliotheken verwenden, die bereits die <xref:Microsoft.Extensions.Options.IOptions`1>-Schnittstelle verwenden und nicht die von <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> bereitgestellten Szenarios benötigen.</span><span class="sxs-lookup"><span data-stu-id="3051a-135">You may continue to use <xref:Microsoft.Extensions.Options.IOptions`1> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions`1> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor`1>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="3051a-136">Allgemeine Optionskonfiguration</span><span class="sxs-lookup"><span data-stu-id="3051a-136">General options configuration</span></span>

<span data-ttu-id="3051a-137">Die allgemeine Optionskonfiguration wird als Beispiel &num;1 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="3051a-137">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="3051a-138">Eine Optionsklasse muss nicht abstrakt sein und über einen öffentlichen parameterlosen Konstruktor verfügen.</span><span class="sxs-lookup"><span data-stu-id="3051a-138">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="3051a-139">Die folgende Klasse `MyOptions` verfügt über die zwei Eigenschaften: `Option1` und `Option2`.</span><span class="sxs-lookup"><span data-stu-id="3051a-139">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="3051a-140">Das Festlegen von Standardwerten ist optional, aber der Klassenkonstruktor im folgenden Beispiel legt den Standardwert von `Option1` fest.</span><span class="sxs-lookup"><span data-stu-id="3051a-140">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="3051a-141">`Option2` hat den Standardwert festgelegt, indem die Eigenschaft direkt initialisiert wurde (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="3051a-141">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="3051a-142">Die `MyOptions`-Klasse wird zum Dienstcontainer mit <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> hinzugefügt und an die Konfiguration gebunden:</span><span class="sxs-lookup"><span data-stu-id="3051a-142">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="3051a-143">Das folgende Seitenmodel verwendet [konstruktorbasierte Dependency Injection](xref:mvc/controllers/dependency-injection) mit <xref:Microsoft.Extensions.Options.IOptionsMonitor`1>, um auf die Einstellungen zugreifen zu können (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="3051a-143">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="3051a-144">Die *appsettings.json*-Datei des Beispiels gibt Werte für `option1` und `option2` an:</span><span class="sxs-lookup"><span data-stu-id="3051a-144">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="3051a-145">Wenn die Anwendung ausgeführt wird, gibt die `OnGet`-Methode des Seitenmodells eine Zeichenfolge zurück, die die Werte der Optionsklasse anzeigt:</span><span class="sxs-lookup"><span data-stu-id="3051a-145">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="3051a-146">Wenn Sie eine benutzerdefinierte <xref:System.Configuration.ConfigurationBuilder>-Klasse verwenden, um Konfigurationsoptionen aus einer Einstellungsdatei zu laden, bestätigen Sie, dass der Basispfad ordnungsgemäß festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="3051a-146">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="3051a-147">Sie müssen den Basispfad nicht explizit festlegen, wenn Sie Konfigurationsoptionen über <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> aus der Einstellungsdatei laden.</span><span class="sxs-lookup"><span data-stu-id="3051a-147">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="3051a-148">Konfigurieren von einfachen Optionen mit einem Delegaten</span><span class="sxs-lookup"><span data-stu-id="3051a-148">Configure simple options with a delegate</span></span>

<span data-ttu-id="3051a-149">Das Konfigurieren von einfachen Optionen mit einem Delegaten wird als Beispiel &num;2 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="3051a-149">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="3051a-150">Verwenden Sie einen Delegaten zum Festlegen von Optionswerten.</span><span class="sxs-lookup"><span data-stu-id="3051a-150">Use a delegate to set options values.</span></span> <span data-ttu-id="3051a-151">Die Beispielanwendung verwendet die `MyOptionsWithDelegateConfig`-Klasse (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="3051a-151">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="3051a-152">Im folgenden Code wird ein zweiter <xref:Microsoft.Extensions.Options.IConfigureOptions`1>-Dienst zum Dienstcontainer hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="3051a-152">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions`1> service is added to the service container.</span></span> <span data-ttu-id="3051a-153">Er verwendet einen Delegaten zum Konfigurieren der Bindung mit `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="3051a-153">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="3051a-154">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="3051a-154">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="3051a-155">Sie können mehrere Konfigurationsanbieter hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="3051a-155">You can add multiple configuration providers.</span></span> <span data-ttu-id="3051a-156">Konfigurationsanbieter sind über NuGet-Pakete verfügbar und werden in der Reihenfolge ihrer Registrierung angewendet.</span><span class="sxs-lookup"><span data-stu-id="3051a-156">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="3051a-157">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="3051a-157">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="3051a-158">Jeder Aufruf von <xref:Microsoft.Extensions.Options.IConfigureOptions`1.Configure*> fügt einen <xref:Microsoft.Extensions.Options.IConfigureOptions`1>-Dienst zum Dienstcontainer hinzu.</span><span class="sxs-lookup"><span data-stu-id="3051a-158">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions`1.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions`1> service to the service container.</span></span> <span data-ttu-id="3051a-159">Im vorherigen Beispiel wurden die Werte von `Option1` und `Option2` beide in *appsettings.json* angegeben, aber die Werte von `Option1` und `Option2` werden vom konfigurierten Delegaten überschrieben.</span><span class="sxs-lookup"><span data-stu-id="3051a-159">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="3051a-160">Wenn mehr als ein Konfigurationsdienst aktiviert ist, *gewinnt* die letzte angegebene Konfigurationsquelle und legt den Konfigurationswert fest.</span><span class="sxs-lookup"><span data-stu-id="3051a-160">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="3051a-161">Wenn die Anwendung ausgeführt wird, gibt die `OnGet`-Methode des Seitenmodells eine Zeichenfolge zurück, die die Werte der Optionsklasse anzeigt:</span><span class="sxs-lookup"><span data-stu-id="3051a-161">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delgate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="3051a-162">Konfigurieren von Unteroptionen</span><span class="sxs-lookup"><span data-stu-id="3051a-162">Suboptions configuration</span></span>

<span data-ttu-id="3051a-163">Das Konfigurieren von Unteroptionen wird als Beispiel &num;3 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="3051a-163">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="3051a-164">Anwendungen sollten Optionsklassen erstellen, die für bestimmte Szenariogruppen (Klassen) in der Anwendung gelten.</span><span class="sxs-lookup"><span data-stu-id="3051a-164">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="3051a-165">Die Komponenten der Anwendung, die Konfigurationswerte erfordern, sollten nur über Zugriff auf die Konfigurationswerte verfügen, die sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="3051a-165">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="3051a-166">Beim Binden von Optionen zur Konfiguration ist jede Eigenschaft im Optionstyp an einen Konfigurationsschlüssel des `property[:sub-property:]`-Formulars gebunden.</span><span class="sxs-lookup"><span data-stu-id="3051a-166">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="3051a-167">Die `MyOptions.Option1`-Eigenschaft ist beispielsweise an den Schlüssel `Option1` gebunden, der von der `option1`-Eigenschaft in *appsettings.json* gelesen wird.</span><span class="sxs-lookup"><span data-stu-id="3051a-167">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="3051a-168">Im folgenden Code wird ein dritter <xref:Microsoft.Extensions.Options.IConfigureOptions`1>-Dienst zum Dienstcontainer hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="3051a-168">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions`1> service is added to the service container.</span></span> <span data-ttu-id="3051a-169">Er verbindet `MySubOptions` mit dem `subsection`-Abschnitt der *appSettings.json*-Datei:</span><span class="sxs-lookup"><span data-stu-id="3051a-169">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="3051a-170">Die `GetSection`-Erweiterungsmethode erfordert das [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/)-NuGet-Paket.</span><span class="sxs-lookup"><span data-stu-id="3051a-170">The `GetSection` extension method requires the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet package.</span></span> <span data-ttu-id="3051a-171">Wenn die Anwendung das [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app)-Metapaket (ASP.NET Core 2.1 oder höher) verwendet, ist das Paket automatisch eingeschlossen.</span><span class="sxs-lookup"><span data-stu-id="3051a-171">If the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later), the package is automatically included.</span></span>

<span data-ttu-id="3051a-172">Die *appsettings.json*-Datei des Beispiels definiert ein `subsection`-Element mit Schlüsseln für `suboption1` und `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="3051a-172">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="3051a-173">Die `MySubOptions`-Klasse definiert die Eigenschaften `SubOption1` und `SubOption2` zur Aufnahme der Optionswerte (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="3051a-173">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="3051a-174">Die `OnGet`-Methode des Seitenmodells gibt eine Zeichenfolge mit den Optionswerten (*Pages/Index.cshtml.cs*) zurück:</span><span class="sxs-lookup"><span data-stu-id="3051a-174">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="3051a-175">Wenn die App ausgeführt wird, gibt die `OnGet`-Methode eine Zeichenfolge zurück, die die Werte der untergeordneten Optionsklasse anzeigt:</span><span class="sxs-lookup"><span data-stu-id="3051a-175">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="3051a-176">Optionen, die durch ein Ansichtsmodell oder mit direkter View Injection bereitgestellt werden</span><span class="sxs-lookup"><span data-stu-id="3051a-176">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="3051a-177">Optionen, die durch ein Ansichtsmodell oder mit direkter View Injection bereitgestellt werden, werden im Beispiel &num;4 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="3051a-177">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="3051a-178">Optionen können in einem Ansichtsmodell oder durch direkte Injection von <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> in eine Ansicht (*Pages/Index.cshtml.cs*) angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="3051a-178">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="3051a-179">Die Beispiel-App zeigt das Einfügen von `IOptionsMonitor<MyOptions>` mit einer `@inject`-Anweisung:</span><span class="sxs-lookup"><span data-stu-id="3051a-179">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="3051a-180">Wenn die App ausgeführt wird, werden die Optionswerte in der gerenderten Seite angezeigt:</span><span class="sxs-lookup"><span data-stu-id="3051a-180">When the app is run, the options values are shown in the rendered page:</span></span>

![Optionswerte Option1: value1_from_json und Option2: -1 werden aus dem Modell geladen und in die Ansicht eingefügt.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="3051a-182">Neuladen der Konfigurationsdaten mit IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="3051a-182">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="3051a-183">Das Neuladen der Konfigurationsdaten mit <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1> wird im Beispiel &num;5 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="3051a-183">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="3051a-184"><xref:Microsoft.Extensions.Options.IOptionsSnapshot`1> unterstützt das Neuladen von Optionen mit minimalem Verarbeitungsaufwand.</span><span class="sxs-lookup"><span data-stu-id="3051a-184"><xref:Microsoft.Extensions.Options.IOptionsSnapshot`1> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="3051a-185">Optionen werden einmal pro Anforderung berechnet. Dies geschieht, wenn auf sie zugegriffen wird und sie für die Dauer der Anforderung zwischengespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="3051a-185">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="3051a-186">Im folgenden Beispiel wird veranschaulicht, wie eine neue <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1> nach den *appsettings.json*-Veränderungen erstellt wird (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="3051a-186">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="3051a-187">Mehrere Anforderungen an den Server geben konstante Werte zurück, die von der *appsettings.json*-Datei bereitgestellt werden, bis die Datei geändert wird und die Konfiguration neu lädt.</span><span class="sxs-lookup"><span data-stu-id="3051a-187">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="3051a-188">Die folgende Abbildung zeigt die anfänglichen `option1`- und `option2`-Werte, die aus der *appsettings.json*-Datei geladen werden:</span><span class="sxs-lookup"><span data-stu-id="3051a-188">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="3051a-189">Ändern Sie die Werte in der *appsettings.json*-Datei auf `value1_from_json UPDATED` und `200`.</span><span class="sxs-lookup"><span data-stu-id="3051a-189">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="3051a-190">Speichern Sie die *appsettings.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="3051a-190">Save the *appsettings.json* file.</span></span> <span data-ttu-id="3051a-191">Aktualisieren Sie den Browser, um festzustellen, ob die Optionswerte aktualisiert wurden:</span><span class="sxs-lookup"><span data-stu-id="3051a-191">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="3051a-192">Unterstützung für benannte Optionen mit IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="3051a-192">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="3051a-193">Unterstützung für benannte Optionen mit <xref:Microsoft.Extensions.Options.IConfigureNamedOptions`1> wird als Beispiel &num;6 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="3051a-193">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions`1> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="3051a-194">Die Unterstützung für *benannte Optionen* ermöglicht es der Anwendung, zwischen den Konfigurationen benannter Optionen zu unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="3051a-194">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="3051a-195">In der Beispiel-App werden benannte Optionen mit <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*> deklariert.</span><span class="sxs-lookup"><span data-stu-id="3051a-195">In the sample app, named options are declared with <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*>.</span></span> <span data-ttu-id="3051a-196">`Configure` ruft die Erweiterungsmethode <xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*> auf:</span><span class="sxs-lookup"><span data-stu-id="3051a-196">`Configure` calls the extension method <xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*> method:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="3051a-197">Die Beispielanwendung greift mit <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> auf die benannten Optionen zu (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="3051a-197">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="3051a-198">Bei der Ausführung der Beispielanwendung werden die benannten Optionen zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="3051a-198">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="3051a-199">`named_options_1`-Werte werden von der Konfiguration bereitgestellt. Sie werden aus der *appsettings.json*-Datei geladen.</span><span class="sxs-lookup"><span data-stu-id="3051a-199">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="3051a-200">`named_options_2`-Werte werden bereitgestellt vom:</span><span class="sxs-lookup"><span data-stu-id="3051a-200">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="3051a-201">`named_options_2`-Delegat in `ConfigureServices` für `Option1`.</span><span class="sxs-lookup"><span data-stu-id="3051a-201">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="3051a-202">Der Standardwert für `Option2` wird von der `MyOptions`-Klasse bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="3051a-202">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="3051a-203">Konfigurieren aller Optionen mit der ConfigureAll-Methode</span><span class="sxs-lookup"><span data-stu-id="3051a-203">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="3051a-204">Konfigurieren Sie alle Optionsinstanzen mit der <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>-Methode.</span><span class="sxs-lookup"><span data-stu-id="3051a-204">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="3051a-205">Der folgende Code konfiguriert `Option1` für alle Konfigurationsinstanzen mit einem gemeinsamen Wert.</span><span class="sxs-lookup"><span data-stu-id="3051a-205">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="3051a-206">Fügen Sie der `Startup.ConfigureServices`-Methode manuell den folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="3051a-206">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="3051a-207">Das Ausführen der Beispielanwendung nach dem Hinzufügen des Codes führt zu folgendem Ergebnis:</span><span class="sxs-lookup"><span data-stu-id="3051a-207">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="3051a-208">Alle Optionen sind benannte Instanzen.</span><span class="sxs-lookup"><span data-stu-id="3051a-208">All options are named instances.</span></span> <span data-ttu-id="3051a-209">Vorhandene <xref:Microsoft.Extensions.Options.IConfigureOptions`1>-Instanzen werden behandelt, als ob sie auf die `Options.DefaultName`-Instanz abzielen. Diese ist `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="3051a-209">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions`1> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="3051a-210"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions`1> implementiert auch <xref:Microsoft.Extensions.Options.IConfigureOptions`1>.</span><span class="sxs-lookup"><span data-stu-id="3051a-210"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions`1> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions`1>.</span></span> <span data-ttu-id="3051a-211">Die standardmäßige Implementierung von <xref:Microsoft.Extensions.Options.IOptionsFactory`1> verfügt über eine Logik, um jede einzelne entsprechend zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="3051a-211">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory`1> has logic to use each appropriately.</span></span> <span data-ttu-id="3051a-212">Die benannte Option `null` wird verwendet, um auf alle benannten Instanzen anstelle einer bestimmten benannten Instanz abzuzielen (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> und <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> verwenden diese Konvention).</span><span class="sxs-lookup"><span data-stu-id="3051a-212">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="3051a-213">OptionsBuilder-API</span><span class="sxs-lookup"><span data-stu-id="3051a-213">OptionsBuilder API</span></span>

<span data-ttu-id="3051a-214"><xref:Microsoft.Extensions.Options.OptionsBuilder`1> dient zum Konfigurieren von `TOptions`-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="3051a-214"><xref:Microsoft.Extensions.Options.OptionsBuilder`1> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="3051a-215">`OptionsBuilder` optimiert die Erstellung von benannten Optionen, da es sich nur um einen einzelnen Parameter für den ersten `AddOptions<TOptions>(string optionsName)`-Aufruf handelt statt um alle nachfolgenden Aufrufe.</span><span class="sxs-lookup"><span data-stu-id="3051a-215">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="3051a-216">Die Optionsvalidierung und die `ConfigureOptions`-Überladungen, die Dienstabhängigkeiten akzeptieren, sind nur über `OptionsBuilder` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="3051a-216">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="configurelttoptions-tdep1--tdep4gt-method"></a><span data-ttu-id="3051a-217">Konfigurieren der Methode &lt;TOptions, TDep1, ... TDep4&gt;</span><span class="sxs-lookup"><span data-stu-id="3051a-217">Configure&lt;TOptions, TDep1, ... TDep4&gt; method</span></span>

<span data-ttu-id="3051a-218">Die Konfiguration von Optionen mit Diensten von DI durch die Implementierung von `IConfigure[Named]Options` als Baustein ist ausführlich.</span><span class="sxs-lookup"><span data-stu-id="3051a-218">Using services from DI to configure options by implementing `IConfigure[Named]Options` in a boilerplate manner is verbose.</span></span> <span data-ttu-id="3051a-219">Überladungen für `ConfigureOptions` in `OptionsBuilder<TOptions>` ermöglichen es Ihnen, Optionen mit bis zu fünf Diensten zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="3051a-219">Overloads for `ConfigureOptions` on `OptionsBuilder<TOptions>` allow you to use up to five services to configure options:</span></span>

```csharp
services.AddOptions<MyOptions>("optionalName")
    .Configure<Service1, Service2, Service3, Service4, Service5>(
        (o, s, s2, s3, s4, s5) => 
            o.Property = DoSomethingWith(s, s2, s3, s4, s5));
```

<span data-ttu-id="3051a-220">Die Überladung registriert eine vorübergehende generische <xref:Microsoft.Extensions.Options.IConfigureNamedOptions`1>, die über einen Konstruktor verfügt, der die angegebenen generischen Diensttypen akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="3051a-220">The overload registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions`1>, which has a constructor that accepts the generic service types specified.</span></span> 

::: moniker range=">= aspnetcore-2.2"

## <a name="options-validation"></a><span data-ttu-id="3051a-221">Überprüfung von Optionen</span><span class="sxs-lookup"><span data-stu-id="3051a-221">Options validation</span></span>

<span data-ttu-id="3051a-222">Sie können Optionen überprüfen, wenn diese konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="3051a-222">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="3051a-223">Rufen Sie `Validate` mit einer Überprüfungsmethode auf, die `true` zurückgibt, wenn die Optionen gültig sind, und `false` zurückgibt, wenn sie es nicht sind:</span><span class="sxs-lookup"><span data-stu-id="3051a-223">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

<span data-ttu-id="3051a-224">Im vorherigen Beispiel wurde die benannte Optionsinstanz auf `optionalOptionsName` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="3051a-224">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="3051a-225">Die Standardoptionsinstanz ist `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="3051a-225">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="3051a-226">Überprüfungen werden ausgeführt, wenn die Optionsinstanz erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="3051a-226">Validation runs when the options instance is created.</span></span> <span data-ttu-id="3051a-227">Ihre Optionsinstanz besteht die Überprüfung beim ersten Zugriff garantiert.</span><span class="sxs-lookup"><span data-stu-id="3051a-227">Your options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="3051a-228">Die Überprüfung von Optionen schützt nicht davor, dass Optionen nach der ersten Konfiguration und Überprüfung geändert werden.</span><span class="sxs-lookup"><span data-stu-id="3051a-228">Options validation doesn't guard against options modifications after the options are initially configured and validated.</span></span>

<span data-ttu-id="3051a-229">Die `Validate`-Methode akzeptiert ein `Func<TOptions, bool>`-Element.</span><span class="sxs-lookup"><span data-stu-id="3051a-229">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="3051a-230">Um die Überprüfung vollständig anzupassen, implementieren Sie `IValidateOptions<TOptions>`. Dies ermöglicht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="3051a-230">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="3051a-231">Eine Überprüfung von mehreren Optionstypen: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="3051a-231">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="3051a-232">Eine Überprüfung, die von einem anderen Optionstyp abhängig ist: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="3051a-232">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="3051a-233">`IValidateOptions` validiert Folgendes:</span><span class="sxs-lookup"><span data-stu-id="3051a-233">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="3051a-234">Eine bestimmte benannte Optionsinstanz.</span><span class="sxs-lookup"><span data-stu-id="3051a-234">A specific named options instance.</span></span>
* <span data-ttu-id="3051a-235">Alle Optionen, wenn `name` den Wert `null` aufweist.</span><span class="sxs-lookup"><span data-stu-id="3051a-235">All options when `name` is `null`.</span></span>

<span data-ttu-id="3051a-236">Geben Sie ein `ValidateOptionsResult` aus Ihrer Implementierung der Schnittstelle zurück:</span><span class="sxs-lookup"><span data-stu-id="3051a-236">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="3051a-237">Die Überprüfung auf Basis von Datenanmerkungen ist über das Paket [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) durch Aufrufen der `ValidateDataAnnotations`-Methode in `OptionsBuilder<TOptions>` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="3051a-237">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the `ValidateDataAnnotations` method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="3051a-238">`Microsoft.Extensions.Options.DataAnnotations` ist im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.2 oder höher) enthalten.</span><span class="sxs-lookup"><span data-stu-id="3051a-238">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.2 or later).</span></span>

```csharp
private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().Value);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="3051a-239">Die vorzeitige Überprüfung (Fail-fast beim Start) wird für ein späteres Release in Erwägung.</span><span class="sxs-lookup"><span data-stu-id="3051a-239">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

::: moniker-end

## <a name="options-post-configuration"></a><span data-ttu-id="3051a-240">Optionen für die Postkonfiguration</span><span class="sxs-lookup"><span data-stu-id="3051a-240">Options post-configuration</span></span>

<span data-ttu-id="3051a-241">Legen Sie die Postkonfiguration mit <xref:Microsoft.Extensions.Options.IPostConfigureOptions`1> fest.</span><span class="sxs-lookup"><span data-stu-id="3051a-241">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions`1>.</span></span> <span data-ttu-id="3051a-242">Die Postkonfiguration erfolgt, nachdem die gesamte <xref:Microsoft.Extensions.Options.IConfigureOptions`1>-Konfiguration abgeschlossen ist:</span><span class="sxs-lookup"><span data-stu-id="3051a-242">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions`1> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="3051a-243"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> ist nach dem Konfigurieren der benannten Optionen verfügbar:</span><span class="sxs-lookup"><span data-stu-id="3051a-243"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="3051a-244">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> zur Postkonfiguration aller Konfigurationsinstanzen:</span><span class="sxs-lookup"><span data-stu-id="3051a-244">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="3051a-245">Zugreifen auf Optionen während des Starts</span><span class="sxs-lookup"><span data-stu-id="3051a-245">Accessing options during startup</span></span>

<span data-ttu-id="3051a-246"><xref:Microsoft.Extensions.Options.IOptions`1> und <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> können in `Startup.Configure` verwendet werden, da Dienste erstellt werden, bevor die `Configure`-Methode ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="3051a-246"><xref:Microsoft.Extensions.Options.IOptions`1> and <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="3051a-247">Verwenden Sie <xref:Microsoft.Extensions.Options.IOptions`1> oder <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> nicht in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3051a-247">Don't use <xref:Microsoft.Extensions.Options.IOptions`1> or <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3051a-248">Es kann einen inkonsistenten Optionszustand geben. Dies liegt an der Reihenfolge der Dienstregistrierungen.</span><span class="sxs-lookup"><span data-stu-id="3051a-248">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3051a-249">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="3051a-249">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
