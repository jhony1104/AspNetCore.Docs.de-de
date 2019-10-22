---
title: Optionsmuster in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie Sie Optionsmuster verwenden, um Gruppen von zusammengehörigen Einstellungen in ASP.NET Core-Anwendungen darzustellen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/11/2019
uid: fundamentals/configuration/options
ms.openlocfilehash: eb0b7f3f4596b63cf3142017c5c5fe4923aac3a4
ms.sourcegitcommit: dd026eceee79e943bd6b4a37b144803b50617583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72378739"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="8a788-103">Optionsmuster in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8a788-103">Options pattern in ASP.NET Core</span></span>

<span data-ttu-id="8a788-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8a788-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8a788-105">Das Optionsmuster verwendet Klassen, um Gruppen von zusammengehörigen Einstellungen darzustellen.</span><span class="sxs-lookup"><span data-stu-id="8a788-105">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="8a788-106">Wenn [Konfigurationseinstellungen](xref:fundamentals/configuration/index) nach Szenario in separate Klassen isoliert werden, entspricht die Anwendung zwei wichtigen Prinzipien der Softwareentwicklung:</span><span class="sxs-lookup"><span data-stu-id="8a788-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="8a788-107">[Schnittstellentrennungsprinzip (ISP) oder Kapselung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): &ndash;-Szenarios (Klassen), die von Konfigurationseinstellungen abhängen, sind nur von den Konfigurationseinstellungen abhängig, die sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="8a788-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="8a788-108">[Trennung von Bereichen](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash;: Einstellungen für die verschiedenen Teile der Anwendung hängen nicht voneinander ab und sind nicht gekoppelt.</span><span class="sxs-lookup"><span data-stu-id="8a788-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="8a788-109">Optionen bieten auch einen Mechanismus, um Konfigurationsdaten zu validieren.</span><span class="sxs-lookup"><span data-stu-id="8a788-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="8a788-110">Weitere Informationen finden Sie im Abschnitt [Optionsvalidierung](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="8a788-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="8a788-111">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8a788-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="package"></a><span data-ttu-id="8a788-112">Package</span><span class="sxs-lookup"><span data-stu-id="8a788-112">Package</span></span>

<span data-ttu-id="8a788-113">Auf das Paket [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) wird implizit in ASP.NET Core-Apps verwiesen.</span><span class="sxs-lookup"><span data-stu-id="8a788-113">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="8a788-114">Optionenschnittstellen</span><span class="sxs-lookup"><span data-stu-id="8a788-114">Options interfaces</span></span>

<span data-ttu-id="8a788-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> wird verwendet, um Optionen abzurufen und Benachrichtigungen über Optionen für `TOptions`-Instanzen zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="8a788-115"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="8a788-116"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> unterstützt die folgenden Szenarios:</span><span class="sxs-lookup"><span data-stu-id="8a788-116"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="8a788-117">Änderungsbenachrichtigungen</span><span class="sxs-lookup"><span data-stu-id="8a788-117">Change notifications</span></span>
* [<span data-ttu-id="8a788-118">Benannte Optionen</span><span class="sxs-lookup"><span data-stu-id="8a788-118">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="8a788-119">Erneut ladbare Konfiguration</span><span class="sxs-lookup"><span data-stu-id="8a788-119">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="8a788-120">Selektive Optionsvalidierung (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="8a788-120">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="8a788-121">Mit [Postkonfigurationsszenarien](#options-post-configuration) können Sie Optionen festlegen oder ändern, nachdem alle <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Konfigurationen durchgeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="8a788-121">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="8a788-122"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> ist für das Erstellen neuer Optionsinstanzen zuständig.</span><span class="sxs-lookup"><span data-stu-id="8a788-122"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="8a788-123">Es verfügt über eine einzelne <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>-Methode.</span><span class="sxs-lookup"><span data-stu-id="8a788-123">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="8a788-124">Die Standardimplementierung akzeptiert alle registrierten <xref:Microsoft.Extensions.Options.IConfigureOptions%601> und <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> und führt alle Konfigurationen zuerst und die Postkonfigurationen danach aus.</span><span class="sxs-lookup"><span data-stu-id="8a788-124">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="8a788-125">Es wird zwischen <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> und <xref:Microsoft.Extensions.Options.IConfigureOptions%601> unterschieden, und es werden nur die entsprechenden Schnittstellen aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="8a788-125">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="8a788-126"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> wird von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> zum Zwischenspeichern der `TOptions`-Instanzen verwendet.</span><span class="sxs-lookup"><span data-stu-id="8a788-126"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="8a788-127"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> erklärt Optionsinstanzen im Monitor für ungültig, sodass der Wert neu berechnet wird (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="8a788-127">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="8a788-128">Werte können manuell mit <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> eingeführt werden.</span><span class="sxs-lookup"><span data-stu-id="8a788-128">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="8a788-129">Die <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>-Methode wird verwendet, wenn alle benannten Instanzen bei Bedarf neu erstellt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="8a788-129">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="8a788-130"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> ist nützlich in Szenarien, in denen Optionen bei jeder Anforderung neu berechnet werden sollten.</span><span class="sxs-lookup"><span data-stu-id="8a788-130"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="8a788-131">Weitere Informationen finden Sie im Abschnitt [Neuladen der Konfigurationsdaten mit IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="8a788-131">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="8a788-132"><xref:Microsoft.Extensions.Options.IOptions%601> kann zum Unterstützen von Optionen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="8a788-132"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="8a788-133">Allerdings unterstützt <xref:Microsoft.Extensions.Options.IOptions%601> nicht die oben beschriebenen Szenarios von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="8a788-133">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="8a788-134">Sie können <xref:Microsoft.Extensions.Options.IOptions%601> weiterhin in bestehenden Frameworks und Bibliotheken verwenden, die bereits die <xref:Microsoft.Extensions.Options.IOptions%601>-Schnittstelle verwenden und nicht die von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> bereitgestellten Szenarios benötigen.</span><span class="sxs-lookup"><span data-stu-id="8a788-134">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="8a788-135">Allgemeine Optionskonfiguration</span><span class="sxs-lookup"><span data-stu-id="8a788-135">General options configuration</span></span>

<span data-ttu-id="8a788-136">Die allgemeine Optionskonfiguration wird als Beispiel &num;1 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="8a788-136">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="8a788-137">Eine Optionsklasse muss nicht abstrakt sein und über einen öffentlichen parameterlosen Konstruktor verfügen.</span><span class="sxs-lookup"><span data-stu-id="8a788-137">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="8a788-138">Die folgende Klasse `MyOptions` verfügt über die zwei Eigenschaften: `Option1` und `Option2`.</span><span class="sxs-lookup"><span data-stu-id="8a788-138">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="8a788-139">Das Festlegen von Standardwerten ist optional, aber der Klassenkonstruktor im folgenden Beispiel legt den Standardwert von `Option1` fest.</span><span class="sxs-lookup"><span data-stu-id="8a788-139">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="8a788-140">`Option2` hat den Standardwert festgelegt, indem die Eigenschaft direkt initialisiert wurde (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="8a788-140">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="8a788-141">Die `MyOptions`-Klasse wird zum Dienstcontainer mit <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> hinzugefügt und an die Konfiguration gebunden:</span><span class="sxs-lookup"><span data-stu-id="8a788-141">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="8a788-142">Das folgende Seitenmodel verwendet [konstruktorbasierte Dependency Injection](xref:mvc/controllers/dependency-injection) mit <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>, um auf die Einstellungen zugreifen zu können (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="8a788-142">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="8a788-143">Die *appsettings.json*-Datei des Beispiels gibt Werte für `option1` und `option2` an:</span><span class="sxs-lookup"><span data-stu-id="8a788-143">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="8a788-144">Wenn die Anwendung ausgeführt wird, gibt die `OnGet`-Methode des Seitenmodells eine Zeichenfolge zurück, die die Werte der Optionsklasse anzeigt:</span><span class="sxs-lookup"><span data-stu-id="8a788-144">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="8a788-145">Wenn Sie eine benutzerdefinierte <xref:System.Configuration.ConfigurationBuilder>-Klasse verwenden, um Konfigurationsoptionen aus einer Einstellungsdatei zu laden, bestätigen Sie, dass der Basispfad ordnungsgemäß festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="8a788-145">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="8a788-146">Sie müssen den Basispfad nicht explizit festlegen, wenn Sie Konfigurationsoptionen über <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> aus der Einstellungsdatei laden.</span><span class="sxs-lookup"><span data-stu-id="8a788-146">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="8a788-147">Konfigurieren von einfachen Optionen mit einem Delegaten</span><span class="sxs-lookup"><span data-stu-id="8a788-147">Configure simple options with a delegate</span></span>

<span data-ttu-id="8a788-148">Das Konfigurieren von einfachen Optionen mit einem Delegaten wird als Beispiel &num;2 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="8a788-148">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="8a788-149">Verwenden Sie einen Delegaten zum Festlegen von Optionswerten.</span><span class="sxs-lookup"><span data-stu-id="8a788-149">Use a delegate to set options values.</span></span> <span data-ttu-id="8a788-150">Die Beispielanwendung verwendet die `MyOptionsWithDelegateConfig`-Klasse (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="8a788-150">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="8a788-151">Im folgenden Code wird ein zweiter <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Dienst zum Dienstcontainer hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="8a788-151">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="8a788-152">Er verwendet einen Delegaten zum Konfigurieren der Bindung mit `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="8a788-152">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="8a788-153">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="8a788-153">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="8a788-154">Sie können mehrere Konfigurationsanbieter hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="8a788-154">You can add multiple configuration providers.</span></span> <span data-ttu-id="8a788-155">Konfigurationsanbieter sind über NuGet-Pakete verfügbar und werden in der Reihenfolge ihrer Registrierung angewendet.</span><span class="sxs-lookup"><span data-stu-id="8a788-155">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="8a788-156">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="8a788-156">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="8a788-157">Jeder Aufruf von <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> fügt einen <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Dienst zum Dienstcontainer hinzu.</span><span class="sxs-lookup"><span data-stu-id="8a788-157">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="8a788-158">Im vorherigen Beispiel wurden die Werte von `Option1` und `Option2` beide in *appsettings.json* angegeben, aber die Werte von `Option1` und `Option2` werden vom konfigurierten Delegaten überschrieben.</span><span class="sxs-lookup"><span data-stu-id="8a788-158">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="8a788-159">Wenn mehr als ein Konfigurationsdienst aktiviert ist, *gewinnt* die letzte angegebene Konfigurationsquelle und legt den Konfigurationswert fest.</span><span class="sxs-lookup"><span data-stu-id="8a788-159">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="8a788-160">Wenn die Anwendung ausgeführt wird, gibt die `OnGet`-Methode des Seitenmodells eine Zeichenfolge zurück, die die Werte der Optionsklasse anzeigt:</span><span class="sxs-lookup"><span data-stu-id="8a788-160">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="8a788-161">Konfigurieren von Unteroptionen</span><span class="sxs-lookup"><span data-stu-id="8a788-161">Suboptions configuration</span></span>

<span data-ttu-id="8a788-162">Das Konfigurieren von Unteroptionen wird als Beispiel &num;3 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="8a788-162">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="8a788-163">Anwendungen sollten Optionsklassen erstellen, die für bestimmte Szenariogruppen (Klassen) in der Anwendung gelten.</span><span class="sxs-lookup"><span data-stu-id="8a788-163">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="8a788-164">Die Komponenten der Anwendung, die Konfigurationswerte erfordern, sollten nur über Zugriff auf die Konfigurationswerte verfügen, die sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="8a788-164">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="8a788-165">Beim Binden von Optionen zur Konfiguration ist jede Eigenschaft im Optionstyp an einen Konfigurationsschlüssel des `property[:sub-property:]`-Formulars gebunden.</span><span class="sxs-lookup"><span data-stu-id="8a788-165">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="8a788-166">Die `MyOptions.Option1`-Eigenschaft ist beispielsweise an den Schlüssel `Option1` gebunden, der von der `option1`-Eigenschaft in *appsettings.json* gelesen wird.</span><span class="sxs-lookup"><span data-stu-id="8a788-166">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="8a788-167">Im folgenden Code wird ein dritter <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Dienst zum Dienstcontainer hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="8a788-167">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="8a788-168">Er verbindet `MySubOptions` mit dem `subsection`-Abschnitt der *appSettings.json*-Datei:</span><span class="sxs-lookup"><span data-stu-id="8a788-168">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="8a788-169">Die `GetSection`-Erweiterungsmethode erfordert das [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/)-NuGet-Paket.</span><span class="sxs-lookup"><span data-stu-id="8a788-169">The `GetSection` extension method requires the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet package.</span></span> <span data-ttu-id="8a788-170">Auf `Microsoft.Extensions.Options.ConfigurationExtensions` wird in ASP.NET Core-Apps implizit verwiesen.</span><span class="sxs-lookup"><span data-stu-id="8a788-170">`Microsoft.Extensions.Options.ConfigurationExtensions` is implicitly referenced in ASP.NET Core apps.</span></span>

<span data-ttu-id="8a788-171">Die *appsettings.json*-Datei des Beispiels definiert ein `subsection`-Element mit Schlüsseln für `suboption1` und `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="8a788-171">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/3.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="8a788-172">Die `MySubOptions`-Klasse definiert die Eigenschaften `SubOption1` und `SubOption2` zur Aufnahme der Optionswerte (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="8a788-172">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="8a788-173">Die `OnGet`-Methode des Seitenmodells gibt eine Zeichenfolge mit den Optionswerten (*Pages/Index.cshtml.cs*) zurück:</span><span class="sxs-lookup"><span data-stu-id="8a788-173">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="8a788-174">Wenn die App ausgeführt wird, gibt die `OnGet`-Methode eine Zeichenfolge zurück, die die Werte der untergeordneten Optionsklasse anzeigt:</span><span class="sxs-lookup"><span data-stu-id="8a788-174">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="8a788-175">Optionen, die durch ein Ansichtsmodell oder mit direkter View Injection bereitgestellt werden</span><span class="sxs-lookup"><span data-stu-id="8a788-175">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="8a788-176">Optionen, die durch ein Ansichtsmodell oder mit direkter View Injection bereitgestellt werden, werden im Beispiel &num;4 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="8a788-176">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="8a788-177">Optionen können in einem Ansichtsmodell oder durch direkte Injection von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in eine Ansicht (*Pages/Index.cshtml.cs*) angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="8a788-177">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="8a788-178">Die Beispiel-App zeigt das Einfügen von `IOptionsMonitor<MyOptions>` mit einer `@inject`-Anweisung:</span><span class="sxs-lookup"><span data-stu-id="8a788-178">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/3.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="8a788-179">Wenn die App ausgeführt wird, werden die Optionswerte in der gerenderten Seite angezeigt:</span><span class="sxs-lookup"><span data-stu-id="8a788-179">When the app is run, the options values are shown in the rendered page:</span></span>

![Optionswerte Option1: value1_from_json und Option2: -1 werden aus dem Modell geladen und in die Ansicht eingefügt.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="8a788-181">Neuladen der Konfigurationsdaten mit IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="8a788-181">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="8a788-182">Das Neuladen der Konfigurationsdaten mit <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> wird im Beispiel &num;5 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="8a788-182">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="8a788-183"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> unterstützt das Neuladen von Optionen mit minimalem Verarbeitungsaufwand.</span><span class="sxs-lookup"><span data-stu-id="8a788-183"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="8a788-184">Optionen werden einmal pro Anforderung berechnet. Dies geschieht, wenn auf sie zugegriffen wird und sie für die Dauer der Anforderung zwischengespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="8a788-184">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="8a788-185">Im folgenden Beispiel wird veranschaulicht, wie eine neue <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> nach den *appsettings.json*-Veränderungen erstellt wird (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="8a788-185">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="8a788-186">Mehrere Anforderungen an den Server geben konstante Werte zurück, die von der *appsettings.json*-Datei bereitgestellt werden, bis die Datei geändert wird und die Konfiguration neu lädt.</span><span class="sxs-lookup"><span data-stu-id="8a788-186">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="8a788-187">Die folgende Abbildung zeigt die anfänglichen `option1`- und `option2`-Werte, die aus der *appsettings.json*-Datei geladen werden:</span><span class="sxs-lookup"><span data-stu-id="8a788-187">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="8a788-188">Ändern Sie die Werte in der *appsettings.json*-Datei auf `value1_from_json UPDATED` und `200`.</span><span class="sxs-lookup"><span data-stu-id="8a788-188">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="8a788-189">Speichern Sie die *appsettings.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="8a788-189">Save the *appsettings.json* file.</span></span> <span data-ttu-id="8a788-190">Aktualisieren Sie den Browser, um festzustellen, ob die Optionswerte aktualisiert wurden:</span><span class="sxs-lookup"><span data-stu-id="8a788-190">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="8a788-191">Unterstützung für benannte Optionen mit IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="8a788-191">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="8a788-192">Unterstützung für benannte Optionen mit <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> wird als Beispiel &num;6 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="8a788-192">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="8a788-193">Die Unterstützung für *benannte Optionen* ermöglicht es der Anwendung, zwischen den Konfigurationen benannter Optionen zu unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="8a788-193">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="8a788-194">In der Beispiel-App werden benannte Optionen mit [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) deklariert. Dadurch wird wiederum die Erweiterungsmethode [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="8a788-194">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="8a788-195">Die Beispielanwendung greift mit <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> auf die benannten Optionen zu (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="8a788-195">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="8a788-196">Bei der Ausführung der Beispielanwendung werden die benannten Optionen zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="8a788-196">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="8a788-197">`named_options_1`-Werte werden von der Konfiguration bereitgestellt. Sie werden aus der *appsettings.json*-Datei geladen.</span><span class="sxs-lookup"><span data-stu-id="8a788-197">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="8a788-198">`named_options_2`-Werte werden bereitgestellt vom:</span><span class="sxs-lookup"><span data-stu-id="8a788-198">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="8a788-199">`named_options_2`-Delegat in `ConfigureServices` für `Option1`.</span><span class="sxs-lookup"><span data-stu-id="8a788-199">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="8a788-200">Der Standardwert für `Option2` wird von der `MyOptions`-Klasse bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="8a788-200">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="8a788-201">Konfigurieren aller Optionen mit der ConfigureAll-Methode</span><span class="sxs-lookup"><span data-stu-id="8a788-201">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="8a788-202">Konfigurieren Sie alle Optionsinstanzen mit der <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>-Methode.</span><span class="sxs-lookup"><span data-stu-id="8a788-202">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="8a788-203">Der folgende Code konfiguriert `Option1` für alle Konfigurationsinstanzen mit einem gemeinsamen Wert.</span><span class="sxs-lookup"><span data-stu-id="8a788-203">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="8a788-204">Fügen Sie der `Startup.ConfigureServices`-Methode manuell den folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="8a788-204">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="8a788-205">Das Ausführen der Beispielanwendung nach dem Hinzufügen des Codes führt zu folgendem Ergebnis:</span><span class="sxs-lookup"><span data-stu-id="8a788-205">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="8a788-206">Alle Optionen sind benannte Instanzen.</span><span class="sxs-lookup"><span data-stu-id="8a788-206">All options are named instances.</span></span> <span data-ttu-id="8a788-207">Vorhandene <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Instanzen werden behandelt, als ob sie auf die `Options.DefaultName`-Instanz abzielen. Diese ist `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="8a788-207">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="8a788-208"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementiert auch <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="8a788-208"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="8a788-209">Die standardmäßige Implementierung von <xref:Microsoft.Extensions.Options.IOptionsFactory%601> verfügt über eine Logik, um jede einzelne entsprechend zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="8a788-209">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="8a788-210">Die benannte Option `null` wird verwendet, um auf alle benannten Instanzen anstelle einer bestimmten benannten Instanz abzuzielen (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> und <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> verwenden diese Konvention).</span><span class="sxs-lookup"><span data-stu-id="8a788-210">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="8a788-211">OptionsBuilder-API</span><span class="sxs-lookup"><span data-stu-id="8a788-211">OptionsBuilder API</span></span>

<span data-ttu-id="8a788-212"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> dient zum Konfigurieren von `TOptions`-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="8a788-212"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="8a788-213">`OptionsBuilder` optimiert die Erstellung von benannten Optionen, da es sich nur um einen einzelnen Parameter für den ersten `AddOptions<TOptions>(string optionsName)`-Aufruf handelt statt um alle nachfolgenden Aufrufe.</span><span class="sxs-lookup"><span data-stu-id="8a788-213">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="8a788-214">Die Optionsvalidierung und die `ConfigureOptions`-Überladungen, die Dienstabhängigkeiten akzeptieren, sind nur über `OptionsBuilder` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="8a788-214">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="8a788-215">Verwenden von DI-Diensten zum Konfigurieren von Optionen</span><span class="sxs-lookup"><span data-stu-id="8a788-215">Use DI services to configure options</span></span>

<span data-ttu-id="8a788-216">Beim Konfigurieren von Optionen haben Sie zwei Möglichkeiten, auf andere Dienste aus Dependency Injections zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="8a788-216">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="8a788-217">Übergeben eines Konfigurationsdelegaten an [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) auf [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="8a788-217">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="8a788-218">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) stellt Überladungen von [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) zur Verfügung, die es Ihnen ermöglichen, bis zu fünf Dienste zu verwenden, um Optionen zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="8a788-218">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="8a788-219">Erstellen Ihres eigenen Typs, der <xref:Microsoft.Extensions.Options.IConfigureOptions%601> oder <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementiert, und Registrieren des Typs als Dienst.</span><span class="sxs-lookup"><span data-stu-id="8a788-219">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="8a788-220">Es empfiehlt sich das Übergeben eines Konfigurationsdelegaten an [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), da das Erstellen eines Diensts etwas komplexer ist.</span><span class="sxs-lookup"><span data-stu-id="8a788-220">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="8a788-221">Selbst einen eigenen Typ zu erstellen ist gleichbedeutend mit dem, was das Framework für Sie übernimmt, wenn Sie [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) verwenden.</span><span class="sxs-lookup"><span data-stu-id="8a788-221">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="8a788-222">Wenn [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) aufgerufen wird, wird eine vorübergehende, generische <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> registriert, die über einen Konstruktor verfügt, der die angegeben generischen Diensttypen akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="8a788-222">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="8a788-223">Überprüfung von Optionen</span><span class="sxs-lookup"><span data-stu-id="8a788-223">Options validation</span></span>

<span data-ttu-id="8a788-224">Sie können Optionen überprüfen, wenn diese konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="8a788-224">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="8a788-225">Rufen Sie `Validate` mit einer Überprüfungsmethode auf, die `true` zurückgibt, wenn die Optionen gültig sind, und `false` zurückgibt, wenn sie es nicht sind:</span><span class="sxs-lookup"><span data-stu-id="8a788-225">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="8a788-226">Im vorherigen Beispiel wurde die benannte Optionsinstanz auf `optionalOptionsName` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="8a788-226">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="8a788-227">Die Standardoptionsinstanz ist `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="8a788-227">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="8a788-228">Überprüfungen werden ausgeführt, wenn die Optionsinstanz erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="8a788-228">Validation runs when the options instance is created.</span></span> <span data-ttu-id="8a788-229">Ihre Optionsinstanz besteht die Überprüfung beim ersten Zugriff garantiert.</span><span class="sxs-lookup"><span data-stu-id="8a788-229">Your options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="8a788-230">Die Überprüfung von Optionen schützt nicht davor, dass Optionen nach der ersten Konfiguration und Überprüfung geändert werden.</span><span class="sxs-lookup"><span data-stu-id="8a788-230">Options validation doesn't guard against options modifications after the options are initially configured and validated.</span></span>

<span data-ttu-id="8a788-231">Die `Validate`-Methode akzeptiert ein `Func<TOptions, bool>`-Element.</span><span class="sxs-lookup"><span data-stu-id="8a788-231">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="8a788-232">Um die Überprüfung vollständig anzupassen, implementieren Sie `IValidateOptions<TOptions>`. Dies ermöglicht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="8a788-232">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="8a788-233">Eine Überprüfung von mehreren Optionstypen: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="8a788-233">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="8a788-234">Eine Überprüfung, die von einem anderen Optionstyp abhängig ist: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="8a788-234">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="8a788-235">`IValidateOptions` validiert Folgendes:</span><span class="sxs-lookup"><span data-stu-id="8a788-235">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="8a788-236">Eine bestimmte benannte Optionsinstanz.</span><span class="sxs-lookup"><span data-stu-id="8a788-236">A specific named options instance.</span></span>
* <span data-ttu-id="8a788-237">Alle Optionen, wenn `name` den Wert `null` aufweist.</span><span class="sxs-lookup"><span data-stu-id="8a788-237">All options when `name` is `null`.</span></span>

<span data-ttu-id="8a788-238">Geben Sie ein `ValidateOptionsResult` aus Ihrer Implementierung der Schnittstelle zurück:</span><span class="sxs-lookup"><span data-stu-id="8a788-238">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="8a788-239">Die Überprüfung auf Basis von Datenanmerkungen ist über das Paket [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) durch Aufrufen der <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*>-Methode in `OptionsBuilder<TOptions>` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="8a788-239">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="8a788-240">Auf `Microsoft.Extensions.Options.DataAnnotations` wird in ASP.NET Core-Apps implizit verwiesen.</span><span class="sxs-lookup"><span data-stu-id="8a788-240">`Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.</span></span>

```csharp
using System.ComponentModel.DataAnnotations;
using Microsoft.Extensions.DependencyInjection;

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
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="8a788-241">Die vorzeitige Überprüfung (Fail-fast beim Start) wird für ein späteres Release in Erwägung.</span><span class="sxs-lookup"><span data-stu-id="8a788-241">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="8a788-242">Optionen für die Postkonfiguration</span><span class="sxs-lookup"><span data-stu-id="8a788-242">Options post-configuration</span></span>

<span data-ttu-id="8a788-243">Legen Sie die Postkonfiguration mit <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> fest.</span><span class="sxs-lookup"><span data-stu-id="8a788-243">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="8a788-244">Die Postkonfiguration erfolgt, nachdem die gesamte <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Konfiguration abgeschlossen ist:</span><span class="sxs-lookup"><span data-stu-id="8a788-244">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="8a788-245"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> ist nach dem Konfigurieren der benannten Optionen verfügbar:</span><span class="sxs-lookup"><span data-stu-id="8a788-245"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="8a788-246">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> zur Postkonfiguration aller Konfigurationsinstanzen:</span><span class="sxs-lookup"><span data-stu-id="8a788-246">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="8a788-247">Zugreifen auf Optionen während des Starts</span><span class="sxs-lookup"><span data-stu-id="8a788-247">Accessing options during startup</span></span>

<span data-ttu-id="8a788-248"><xref:Microsoft.Extensions.Options.IOptions%601> und <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> können in `Startup.Configure` verwendet werden, da Dienste erstellt werden, bevor die `Configure`-Methode ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="8a788-248"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="8a788-249">Verwenden Sie <xref:Microsoft.Extensions.Options.IOptions%601> oder <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> nicht in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8a788-249">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8a788-250">Es kann einen inkonsistenten Optionszustand geben. Dies liegt an der Reihenfolge der Dienstregistrierungen.</span><span class="sxs-lookup"><span data-stu-id="8a788-250">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="8a788-251">Das Optionsmuster verwendet Klassen, um Gruppen von zusammengehörigen Einstellungen darzustellen.</span><span class="sxs-lookup"><span data-stu-id="8a788-251">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="8a788-252">Wenn [Konfigurationseinstellungen](xref:fundamentals/configuration/index) nach Szenario in separate Klassen isoliert werden, entspricht die Anwendung zwei wichtigen Prinzipien der Softwareentwicklung:</span><span class="sxs-lookup"><span data-stu-id="8a788-252">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="8a788-253">[Schnittstellentrennungsprinzip (ISP) oder Kapselung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): &ndash;-Szenarios (Klassen), die von Konfigurationseinstellungen abhängen, sind nur von den Konfigurationseinstellungen abhängig, die sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="8a788-253">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="8a788-254">[Trennung von Bereichen](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash;: Einstellungen für die verschiedenen Teile der Anwendung hängen nicht voneinander ab und sind nicht gekoppelt.</span><span class="sxs-lookup"><span data-stu-id="8a788-254">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="8a788-255">Optionen bieten auch einen Mechanismus, um Konfigurationsdaten zu validieren.</span><span class="sxs-lookup"><span data-stu-id="8a788-255">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="8a788-256">Weitere Informationen finden Sie im Abschnitt [Optionsvalidierung](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="8a788-256">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="8a788-257">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8a788-257">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8a788-258">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="8a788-258">Prerequisites</span></span>

<span data-ttu-id="8a788-259">Verweisen Sie auf das [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app), oder fügen Sie einen Paketverweis auf das Paket [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) hinzu.</span><span class="sxs-lookup"><span data-stu-id="8a788-259">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="8a788-260">Optionenschnittstellen</span><span class="sxs-lookup"><span data-stu-id="8a788-260">Options interfaces</span></span>

<span data-ttu-id="8a788-261"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> wird verwendet, um Optionen abzurufen und Benachrichtigungen über Optionen für `TOptions`-Instanzen zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="8a788-261"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="8a788-262"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> unterstützt die folgenden Szenarios:</span><span class="sxs-lookup"><span data-stu-id="8a788-262"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="8a788-263">Änderungsbenachrichtigungen</span><span class="sxs-lookup"><span data-stu-id="8a788-263">Change notifications</span></span>
* [<span data-ttu-id="8a788-264">Benannte Optionen</span><span class="sxs-lookup"><span data-stu-id="8a788-264">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="8a788-265">Erneut ladbare Konfiguration</span><span class="sxs-lookup"><span data-stu-id="8a788-265">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="8a788-266">Selektive Optionsvalidierung (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="8a788-266">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="8a788-267">Mit [Postkonfigurationsszenarien](#options-post-configuration) können Sie Optionen festlegen oder ändern, nachdem alle <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Konfigurationen durchgeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="8a788-267">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="8a788-268"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> ist für das Erstellen neuer Optionsinstanzen zuständig.</span><span class="sxs-lookup"><span data-stu-id="8a788-268"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="8a788-269">Es verfügt über eine einzelne <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>-Methode.</span><span class="sxs-lookup"><span data-stu-id="8a788-269">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="8a788-270">Die Standardimplementierung akzeptiert alle registrierten <xref:Microsoft.Extensions.Options.IConfigureOptions%601> und <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> und führt alle Konfigurationen zuerst und die Postkonfigurationen danach aus.</span><span class="sxs-lookup"><span data-stu-id="8a788-270">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="8a788-271">Es wird zwischen <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> und <xref:Microsoft.Extensions.Options.IConfigureOptions%601> unterschieden, und es werden nur die entsprechenden Schnittstellen aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="8a788-271">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="8a788-272"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> wird von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> zum Zwischenspeichern der `TOptions`-Instanzen verwendet.</span><span class="sxs-lookup"><span data-stu-id="8a788-272"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="8a788-273"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> erklärt Optionsinstanzen im Monitor für ungültig, sodass der Wert neu berechnet wird (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="8a788-273">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="8a788-274">Werte können manuell mit <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> eingeführt werden.</span><span class="sxs-lookup"><span data-stu-id="8a788-274">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="8a788-275">Die <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>-Methode wird verwendet, wenn alle benannten Instanzen bei Bedarf neu erstellt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="8a788-275">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="8a788-276"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> ist nützlich in Szenarien, in denen Optionen bei jeder Anforderung neu berechnet werden sollten.</span><span class="sxs-lookup"><span data-stu-id="8a788-276"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="8a788-277">Weitere Informationen finden Sie im Abschnitt [Neuladen der Konfigurationsdaten mit IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="8a788-277">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="8a788-278"><xref:Microsoft.Extensions.Options.IOptions%601> kann zum Unterstützen von Optionen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="8a788-278"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="8a788-279">Allerdings unterstützt <xref:Microsoft.Extensions.Options.IOptions%601> nicht die oben beschriebenen Szenarios von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="8a788-279">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="8a788-280">Sie können <xref:Microsoft.Extensions.Options.IOptions%601> weiterhin in bestehenden Frameworks und Bibliotheken verwenden, die bereits die <xref:Microsoft.Extensions.Options.IOptions%601>-Schnittstelle verwenden und nicht die von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> bereitgestellten Szenarios benötigen.</span><span class="sxs-lookup"><span data-stu-id="8a788-280">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="8a788-281">Allgemeine Optionskonfiguration</span><span class="sxs-lookup"><span data-stu-id="8a788-281">General options configuration</span></span>

<span data-ttu-id="8a788-282">Die allgemeine Optionskonfiguration wird als Beispiel &num;1 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="8a788-282">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="8a788-283">Eine Optionsklasse muss nicht abstrakt sein und über einen öffentlichen parameterlosen Konstruktor verfügen.</span><span class="sxs-lookup"><span data-stu-id="8a788-283">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="8a788-284">Die folgende Klasse `MyOptions` verfügt über die zwei Eigenschaften: `Option1` und `Option2`.</span><span class="sxs-lookup"><span data-stu-id="8a788-284">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="8a788-285">Das Festlegen von Standardwerten ist optional, aber der Klassenkonstruktor im folgenden Beispiel legt den Standardwert von `Option1` fest.</span><span class="sxs-lookup"><span data-stu-id="8a788-285">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="8a788-286">`Option2` hat den Standardwert festgelegt, indem die Eigenschaft direkt initialisiert wurde (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="8a788-286">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="8a788-287">Die `MyOptions`-Klasse wird zum Dienstcontainer mit <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> hinzugefügt und an die Konfiguration gebunden:</span><span class="sxs-lookup"><span data-stu-id="8a788-287">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="8a788-288">Das folgende Seitenmodel verwendet [konstruktorbasierte Dependency Injection](xref:mvc/controllers/dependency-injection) mit <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>, um auf die Einstellungen zugreifen zu können (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="8a788-288">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="8a788-289">Die *appsettings.json*-Datei des Beispiels gibt Werte für `option1` und `option2` an:</span><span class="sxs-lookup"><span data-stu-id="8a788-289">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="8a788-290">Wenn die Anwendung ausgeführt wird, gibt die `OnGet`-Methode des Seitenmodells eine Zeichenfolge zurück, die die Werte der Optionsklasse anzeigt:</span><span class="sxs-lookup"><span data-stu-id="8a788-290">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="8a788-291">Wenn Sie eine benutzerdefinierte <xref:System.Configuration.ConfigurationBuilder>-Klasse verwenden, um Konfigurationsoptionen aus einer Einstellungsdatei zu laden, bestätigen Sie, dass der Basispfad ordnungsgemäß festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="8a788-291">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="8a788-292">Sie müssen den Basispfad nicht explizit festlegen, wenn Sie Konfigurationsoptionen über <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> aus der Einstellungsdatei laden.</span><span class="sxs-lookup"><span data-stu-id="8a788-292">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="8a788-293">Konfigurieren von einfachen Optionen mit einem Delegaten</span><span class="sxs-lookup"><span data-stu-id="8a788-293">Configure simple options with a delegate</span></span>

<span data-ttu-id="8a788-294">Das Konfigurieren von einfachen Optionen mit einem Delegaten wird als Beispiel &num;2 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="8a788-294">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="8a788-295">Verwenden Sie einen Delegaten zum Festlegen von Optionswerten.</span><span class="sxs-lookup"><span data-stu-id="8a788-295">Use a delegate to set options values.</span></span> <span data-ttu-id="8a788-296">Die Beispielanwendung verwendet die `MyOptionsWithDelegateConfig`-Klasse (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="8a788-296">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="8a788-297">Im folgenden Code wird ein zweiter <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Dienst zum Dienstcontainer hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="8a788-297">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="8a788-298">Er verwendet einen Delegaten zum Konfigurieren der Bindung mit `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="8a788-298">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="8a788-299">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="8a788-299">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="8a788-300">Sie können mehrere Konfigurationsanbieter hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="8a788-300">You can add multiple configuration providers.</span></span> <span data-ttu-id="8a788-301">Konfigurationsanbieter sind über NuGet-Pakete verfügbar und werden in der Reihenfolge ihrer Registrierung angewendet.</span><span class="sxs-lookup"><span data-stu-id="8a788-301">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="8a788-302">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="8a788-302">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="8a788-303">Jeder Aufruf von <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> fügt einen <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Dienst zum Dienstcontainer hinzu.</span><span class="sxs-lookup"><span data-stu-id="8a788-303">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="8a788-304">Im vorherigen Beispiel wurden die Werte von `Option1` und `Option2` beide in *appsettings.json* angegeben, aber die Werte von `Option1` und `Option2` werden vom konfigurierten Delegaten überschrieben.</span><span class="sxs-lookup"><span data-stu-id="8a788-304">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="8a788-305">Wenn mehr als ein Konfigurationsdienst aktiviert ist, *gewinnt* die letzte angegebene Konfigurationsquelle und legt den Konfigurationswert fest.</span><span class="sxs-lookup"><span data-stu-id="8a788-305">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="8a788-306">Wenn die Anwendung ausgeführt wird, gibt die `OnGet`-Methode des Seitenmodells eine Zeichenfolge zurück, die die Werte der Optionsklasse anzeigt:</span><span class="sxs-lookup"><span data-stu-id="8a788-306">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="8a788-307">Konfigurieren von Unteroptionen</span><span class="sxs-lookup"><span data-stu-id="8a788-307">Suboptions configuration</span></span>

<span data-ttu-id="8a788-308">Das Konfigurieren von Unteroptionen wird als Beispiel &num;3 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="8a788-308">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="8a788-309">Anwendungen sollten Optionsklassen erstellen, die für bestimmte Szenariogruppen (Klassen) in der Anwendung gelten.</span><span class="sxs-lookup"><span data-stu-id="8a788-309">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="8a788-310">Die Komponenten der Anwendung, die Konfigurationswerte erfordern, sollten nur über Zugriff auf die Konfigurationswerte verfügen, die sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="8a788-310">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="8a788-311">Beim Binden von Optionen zur Konfiguration ist jede Eigenschaft im Optionstyp an einen Konfigurationsschlüssel des `property[:sub-property:]`-Formulars gebunden.</span><span class="sxs-lookup"><span data-stu-id="8a788-311">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="8a788-312">Die `MyOptions.Option1`-Eigenschaft ist beispielsweise an den Schlüssel `Option1` gebunden, der von der `option1`-Eigenschaft in *appsettings.json* gelesen wird.</span><span class="sxs-lookup"><span data-stu-id="8a788-312">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="8a788-313">Im folgenden Code wird ein dritter <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Dienst zum Dienstcontainer hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="8a788-313">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="8a788-314">Er verbindet `MySubOptions` mit dem `subsection`-Abschnitt der *appSettings.json*-Datei:</span><span class="sxs-lookup"><span data-stu-id="8a788-314">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="8a788-315">Die `GetSection`-Erweiterungsmethode erfordert das [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/)-NuGet-Paket.</span><span class="sxs-lookup"><span data-stu-id="8a788-315">The `GetSection` extension method requires the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet package.</span></span> <span data-ttu-id="8a788-316">Wenn die Anwendung das [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app)-Metapaket (ASP.NET Core 2.1 oder höher) verwendet, ist das Paket automatisch eingeschlossen.</span><span class="sxs-lookup"><span data-stu-id="8a788-316">If the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later), the package is automatically included.</span></span>

<span data-ttu-id="8a788-317">Die *appsettings.json*-Datei des Beispiels definiert ein `subsection`-Element mit Schlüsseln für `suboption1` und `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="8a788-317">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="8a788-318">Die `MySubOptions`-Klasse definiert die Eigenschaften `SubOption1` und `SubOption2` zur Aufnahme der Optionswerte (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="8a788-318">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="8a788-319">Die `OnGet`-Methode des Seitenmodells gibt eine Zeichenfolge mit den Optionswerten (*Pages/Index.cshtml.cs*) zurück:</span><span class="sxs-lookup"><span data-stu-id="8a788-319">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="8a788-320">Wenn die App ausgeführt wird, gibt die `OnGet`-Methode eine Zeichenfolge zurück, die die Werte der untergeordneten Optionsklasse anzeigt:</span><span class="sxs-lookup"><span data-stu-id="8a788-320">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="8a788-321">Optionen, die durch ein Ansichtsmodell oder mit direkter View Injection bereitgestellt werden</span><span class="sxs-lookup"><span data-stu-id="8a788-321">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="8a788-322">Optionen, die durch ein Ansichtsmodell oder mit direkter View Injection bereitgestellt werden, werden im Beispiel &num;4 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="8a788-322">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="8a788-323">Optionen können in einem Ansichtsmodell oder durch direkte Injection von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in eine Ansicht (*Pages/Index.cshtml.cs*) angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="8a788-323">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="8a788-324">Die Beispiel-App zeigt das Einfügen von `IOptionsMonitor<MyOptions>` mit einer `@inject`-Anweisung:</span><span class="sxs-lookup"><span data-stu-id="8a788-324">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="8a788-325">Wenn die App ausgeführt wird, werden die Optionswerte in der gerenderten Seite angezeigt:</span><span class="sxs-lookup"><span data-stu-id="8a788-325">When the app is run, the options values are shown in the rendered page:</span></span>

![Optionswerte Option1: value1_from_json und Option2: -1 werden aus dem Modell geladen und in die Ansicht eingefügt.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="8a788-327">Neuladen der Konfigurationsdaten mit IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="8a788-327">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="8a788-328">Das Neuladen der Konfigurationsdaten mit <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> wird im Beispiel &num;5 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="8a788-328">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="8a788-329"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> unterstützt das Neuladen von Optionen mit minimalem Verarbeitungsaufwand.</span><span class="sxs-lookup"><span data-stu-id="8a788-329"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="8a788-330">Optionen werden einmal pro Anforderung berechnet. Dies geschieht, wenn auf sie zugegriffen wird und sie für die Dauer der Anforderung zwischengespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="8a788-330">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="8a788-331">Im folgenden Beispiel wird veranschaulicht, wie eine neue <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> nach den *appsettings.json*-Veränderungen erstellt wird (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="8a788-331">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="8a788-332">Mehrere Anforderungen an den Server geben konstante Werte zurück, die von der *appsettings.json*-Datei bereitgestellt werden, bis die Datei geändert wird und die Konfiguration neu lädt.</span><span class="sxs-lookup"><span data-stu-id="8a788-332">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="8a788-333">Die folgende Abbildung zeigt die anfänglichen `option1`- und `option2`-Werte, die aus der *appsettings.json*-Datei geladen werden:</span><span class="sxs-lookup"><span data-stu-id="8a788-333">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="8a788-334">Ändern Sie die Werte in der *appsettings.json*-Datei auf `value1_from_json UPDATED` und `200`.</span><span class="sxs-lookup"><span data-stu-id="8a788-334">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="8a788-335">Speichern Sie die *appsettings.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="8a788-335">Save the *appsettings.json* file.</span></span> <span data-ttu-id="8a788-336">Aktualisieren Sie den Browser, um festzustellen, ob die Optionswerte aktualisiert wurden:</span><span class="sxs-lookup"><span data-stu-id="8a788-336">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="8a788-337">Unterstützung für benannte Optionen mit IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="8a788-337">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="8a788-338">Unterstützung für benannte Optionen mit <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> wird als Beispiel &num;6 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="8a788-338">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="8a788-339">Die Unterstützung für *benannte Optionen* ermöglicht es der Anwendung, zwischen den Konfigurationen benannter Optionen zu unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="8a788-339">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="8a788-340">In der Beispiel-App werden benannte Optionen mit [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) deklariert. Dadurch wird wiederum die Erweiterungsmethode [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="8a788-340">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="8a788-341">Die Beispielanwendung greift mit <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> auf die benannten Optionen zu (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="8a788-341">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="8a788-342">Bei der Ausführung der Beispielanwendung werden die benannten Optionen zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="8a788-342">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="8a788-343">`named_options_1`-Werte werden von der Konfiguration bereitgestellt. Sie werden aus der *appsettings.json*-Datei geladen.</span><span class="sxs-lookup"><span data-stu-id="8a788-343">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="8a788-344">`named_options_2`-Werte werden bereitgestellt vom:</span><span class="sxs-lookup"><span data-stu-id="8a788-344">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="8a788-345">`named_options_2`-Delegat in `ConfigureServices` für `Option1`.</span><span class="sxs-lookup"><span data-stu-id="8a788-345">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="8a788-346">Der Standardwert für `Option2` wird von der `MyOptions`-Klasse bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="8a788-346">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="8a788-347">Konfigurieren aller Optionen mit der ConfigureAll-Methode</span><span class="sxs-lookup"><span data-stu-id="8a788-347">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="8a788-348">Konfigurieren Sie alle Optionsinstanzen mit der <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>-Methode.</span><span class="sxs-lookup"><span data-stu-id="8a788-348">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="8a788-349">Der folgende Code konfiguriert `Option1` für alle Konfigurationsinstanzen mit einem gemeinsamen Wert.</span><span class="sxs-lookup"><span data-stu-id="8a788-349">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="8a788-350">Fügen Sie der `Startup.ConfigureServices`-Methode manuell den folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="8a788-350">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="8a788-351">Das Ausführen der Beispielanwendung nach dem Hinzufügen des Codes führt zu folgendem Ergebnis:</span><span class="sxs-lookup"><span data-stu-id="8a788-351">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="8a788-352">Alle Optionen sind benannte Instanzen.</span><span class="sxs-lookup"><span data-stu-id="8a788-352">All options are named instances.</span></span> <span data-ttu-id="8a788-353">Vorhandene <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Instanzen werden behandelt, als ob sie auf die `Options.DefaultName`-Instanz abzielen. Diese ist `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="8a788-353">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="8a788-354"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementiert auch <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="8a788-354"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="8a788-355">Die standardmäßige Implementierung von <xref:Microsoft.Extensions.Options.IOptionsFactory%601> verfügt über eine Logik, um jede einzelne entsprechend zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="8a788-355">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="8a788-356">Die benannte Option `null` wird verwendet, um auf alle benannten Instanzen anstelle einer bestimmten benannten Instanz abzuzielen (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> und <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> verwenden diese Konvention).</span><span class="sxs-lookup"><span data-stu-id="8a788-356">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="8a788-357">OptionsBuilder-API</span><span class="sxs-lookup"><span data-stu-id="8a788-357">OptionsBuilder API</span></span>

<span data-ttu-id="8a788-358"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> dient zum Konfigurieren von `TOptions`-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="8a788-358"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="8a788-359">`OptionsBuilder` optimiert die Erstellung von benannten Optionen, da es sich nur um einen einzelnen Parameter für den ersten `AddOptions<TOptions>(string optionsName)`-Aufruf handelt statt um alle nachfolgenden Aufrufe.</span><span class="sxs-lookup"><span data-stu-id="8a788-359">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="8a788-360">Die Optionsvalidierung und die `ConfigureOptions`-Überladungen, die Dienstabhängigkeiten akzeptieren, sind nur über `OptionsBuilder` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="8a788-360">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="8a788-361">Verwenden von DI-Diensten zum Konfigurieren von Optionen</span><span class="sxs-lookup"><span data-stu-id="8a788-361">Use DI services to configure options</span></span>

<span data-ttu-id="8a788-362">Beim Konfigurieren von Optionen haben Sie zwei Möglichkeiten, auf andere Dienste aus Dependency Injections zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="8a788-362">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="8a788-363">Übergeben eines Konfigurationsdelegaten an [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) auf [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="8a788-363">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="8a788-364">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) stellt Überladungen von [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) zur Verfügung, die es Ihnen ermöglichen, bis zu fünf Dienste zu verwenden, um Optionen zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="8a788-364">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="8a788-365">Erstellen Ihres eigenen Typs, der <xref:Microsoft.Extensions.Options.IConfigureOptions%601> oder <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementiert, und Registrieren des Typs als Dienst.</span><span class="sxs-lookup"><span data-stu-id="8a788-365">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="8a788-366">Es empfiehlt sich das Übergeben eines Konfigurationsdelegaten an [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), da das Erstellen eines Diensts etwas komplexer ist.</span><span class="sxs-lookup"><span data-stu-id="8a788-366">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="8a788-367">Selbst einen eigenen Typ zu erstellen ist gleichbedeutend mit dem, was das Framework für Sie übernimmt, wenn Sie [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) verwenden.</span><span class="sxs-lookup"><span data-stu-id="8a788-367">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="8a788-368">Wenn [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) aufgerufen wird, wird eine vorübergehende, generische <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> registriert, die über einen Konstruktor verfügt, der die angegeben generischen Diensttypen akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="8a788-368">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="8a788-369">Überprüfung von Optionen</span><span class="sxs-lookup"><span data-stu-id="8a788-369">Options validation</span></span>

<span data-ttu-id="8a788-370">Sie können Optionen überprüfen, wenn diese konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="8a788-370">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="8a788-371">Rufen Sie `Validate` mit einer Überprüfungsmethode auf, die `true` zurückgibt, wenn die Optionen gültig sind, und `false` zurückgibt, wenn sie es nicht sind:</span><span class="sxs-lookup"><span data-stu-id="8a788-371">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="8a788-372">Im vorherigen Beispiel wurde die benannte Optionsinstanz auf `optionalOptionsName` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="8a788-372">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="8a788-373">Die Standardoptionsinstanz ist `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="8a788-373">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="8a788-374">Überprüfungen werden ausgeführt, wenn die Optionsinstanz erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="8a788-374">Validation runs when the options instance is created.</span></span> <span data-ttu-id="8a788-375">Ihre Optionsinstanz besteht die Überprüfung beim ersten Zugriff garantiert.</span><span class="sxs-lookup"><span data-stu-id="8a788-375">Your options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="8a788-376">Die Überprüfung von Optionen schützt nicht davor, dass Optionen nach der ersten Konfiguration und Überprüfung geändert werden.</span><span class="sxs-lookup"><span data-stu-id="8a788-376">Options validation doesn't guard against options modifications after the options are initially configured and validated.</span></span>

<span data-ttu-id="8a788-377">Die `Validate`-Methode akzeptiert ein `Func<TOptions, bool>`-Element.</span><span class="sxs-lookup"><span data-stu-id="8a788-377">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="8a788-378">Um die Überprüfung vollständig anzupassen, implementieren Sie `IValidateOptions<TOptions>`. Dies ermöglicht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="8a788-378">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="8a788-379">Eine Überprüfung von mehreren Optionstypen: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="8a788-379">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="8a788-380">Eine Überprüfung, die von einem anderen Optionstyp abhängig ist: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="8a788-380">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="8a788-381">`IValidateOptions` validiert Folgendes:</span><span class="sxs-lookup"><span data-stu-id="8a788-381">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="8a788-382">Eine bestimmte benannte Optionsinstanz.</span><span class="sxs-lookup"><span data-stu-id="8a788-382">A specific named options instance.</span></span>
* <span data-ttu-id="8a788-383">Alle Optionen, wenn `name` den Wert `null` aufweist.</span><span class="sxs-lookup"><span data-stu-id="8a788-383">All options when `name` is `null`.</span></span>

<span data-ttu-id="8a788-384">Geben Sie ein `ValidateOptionsResult` aus Ihrer Implementierung der Schnittstelle zurück:</span><span class="sxs-lookup"><span data-stu-id="8a788-384">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="8a788-385">Die Überprüfung auf Basis von Datenanmerkungen ist über das Paket [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) durch Aufrufen der <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*>-Methode in `OptionsBuilder<TOptions>` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="8a788-385">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="8a788-386">`Microsoft.Extensions.Options.DataAnnotations` ist im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="8a788-386">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

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
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="8a788-387">Die vorzeitige Überprüfung (Fail-fast beim Start) wird für ein späteres Release in Erwägung.</span><span class="sxs-lookup"><span data-stu-id="8a788-387">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="8a788-388">Optionen für die Postkonfiguration</span><span class="sxs-lookup"><span data-stu-id="8a788-388">Options post-configuration</span></span>

<span data-ttu-id="8a788-389">Legen Sie die Postkonfiguration mit <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> fest.</span><span class="sxs-lookup"><span data-stu-id="8a788-389">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="8a788-390">Die Postkonfiguration erfolgt, nachdem die gesamte <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Konfiguration abgeschlossen ist:</span><span class="sxs-lookup"><span data-stu-id="8a788-390">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="8a788-391"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> ist nach dem Konfigurieren der benannten Optionen verfügbar:</span><span class="sxs-lookup"><span data-stu-id="8a788-391"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="8a788-392">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> zur Postkonfiguration aller Konfigurationsinstanzen:</span><span class="sxs-lookup"><span data-stu-id="8a788-392">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="8a788-393">Zugreifen auf Optionen während des Starts</span><span class="sxs-lookup"><span data-stu-id="8a788-393">Accessing options during startup</span></span>

<span data-ttu-id="8a788-394"><xref:Microsoft.Extensions.Options.IOptions%601> und <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> können in `Startup.Configure` verwendet werden, da Dienste erstellt werden, bevor die `Configure`-Methode ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="8a788-394"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="8a788-395">Verwenden Sie <xref:Microsoft.Extensions.Options.IOptions%601> oder <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> nicht in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8a788-395">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8a788-396">Es kann einen inkonsistenten Optionszustand geben. Dies liegt an der Reihenfolge der Dienstregistrierungen.</span><span class="sxs-lookup"><span data-stu-id="8a788-396">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="8a788-397">Das Optionsmuster verwendet Klassen, um Gruppen von zusammengehörigen Einstellungen darzustellen.</span><span class="sxs-lookup"><span data-stu-id="8a788-397">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="8a788-398">Wenn [Konfigurationseinstellungen](xref:fundamentals/configuration/index) nach Szenario in separate Klassen isoliert werden, entspricht die Anwendung zwei wichtigen Prinzipien der Softwareentwicklung:</span><span class="sxs-lookup"><span data-stu-id="8a788-398">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="8a788-399">[Schnittstellentrennungsprinzip (ISP) oder Kapselung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): &ndash;-Szenarios (Klassen), die von Konfigurationseinstellungen abhängen, sind nur von den Konfigurationseinstellungen abhängig, die sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="8a788-399">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) &ndash; Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="8a788-400">[Trennung von Bereichen](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash;: Einstellungen für die verschiedenen Teile der Anwendung hängen nicht voneinander ab und sind nicht gekoppelt.</span><span class="sxs-lookup"><span data-stu-id="8a788-400">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) &ndash; Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="8a788-401">Optionen bieten auch einen Mechanismus, um Konfigurationsdaten zu validieren.</span><span class="sxs-lookup"><span data-stu-id="8a788-401">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="8a788-402">Weitere Informationen finden Sie im Abschnitt [Optionsvalidierung](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="8a788-402">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="8a788-403">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8a788-403">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8a788-404">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="8a788-404">Prerequisites</span></span>

<span data-ttu-id="8a788-405">Verweisen Sie auf das [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app), oder fügen Sie einen Paketverweis auf das Paket [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) hinzu.</span><span class="sxs-lookup"><span data-stu-id="8a788-405">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="8a788-406">Optionenschnittstellen</span><span class="sxs-lookup"><span data-stu-id="8a788-406">Options interfaces</span></span>

<span data-ttu-id="8a788-407"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> wird verwendet, um Optionen abzurufen und Benachrichtigungen über Optionen für `TOptions`-Instanzen zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="8a788-407"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="8a788-408"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> unterstützt die folgenden Szenarios:</span><span class="sxs-lookup"><span data-stu-id="8a788-408"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="8a788-409">Änderungsbenachrichtigungen</span><span class="sxs-lookup"><span data-stu-id="8a788-409">Change notifications</span></span>
* [<span data-ttu-id="8a788-410">Benannte Optionen</span><span class="sxs-lookup"><span data-stu-id="8a788-410">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="8a788-411">Erneut ladbare Konfiguration</span><span class="sxs-lookup"><span data-stu-id="8a788-411">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="8a788-412">Selektive Optionsvalidierung (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="8a788-412">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="8a788-413">Mit [Postkonfigurationsszenarien](#options-post-configuration) können Sie Optionen festlegen oder ändern, nachdem alle <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Konfigurationen durchgeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="8a788-413">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="8a788-414"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> ist für das Erstellen neuer Optionsinstanzen zuständig.</span><span class="sxs-lookup"><span data-stu-id="8a788-414"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="8a788-415">Es verfügt über eine einzelne <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>-Methode.</span><span class="sxs-lookup"><span data-stu-id="8a788-415">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="8a788-416">Die Standardimplementierung akzeptiert alle registrierten <xref:Microsoft.Extensions.Options.IConfigureOptions%601> und <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> und führt alle Konfigurationen zuerst und die Postkonfigurationen danach aus.</span><span class="sxs-lookup"><span data-stu-id="8a788-416">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="8a788-417">Es wird zwischen <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> und <xref:Microsoft.Extensions.Options.IConfigureOptions%601> unterschieden, und es werden nur die entsprechenden Schnittstellen aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="8a788-417">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="8a788-418"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> wird von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> zum Zwischenspeichern der `TOptions`-Instanzen verwendet.</span><span class="sxs-lookup"><span data-stu-id="8a788-418"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="8a788-419"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> erklärt Optionsinstanzen im Monitor für ungültig, sodass der Wert neu berechnet wird (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="8a788-419">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="8a788-420">Werte können manuell mit <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> eingeführt werden.</span><span class="sxs-lookup"><span data-stu-id="8a788-420">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="8a788-421">Die <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>-Methode wird verwendet, wenn alle benannten Instanzen bei Bedarf neu erstellt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="8a788-421">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="8a788-422"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> ist nützlich in Szenarien, in denen Optionen bei jeder Anforderung neu berechnet werden sollten.</span><span class="sxs-lookup"><span data-stu-id="8a788-422"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="8a788-423">Weitere Informationen finden Sie im Abschnitt [Neuladen der Konfigurationsdaten mit IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="8a788-423">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="8a788-424"><xref:Microsoft.Extensions.Options.IOptions%601> kann zum Unterstützen von Optionen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="8a788-424"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="8a788-425">Allerdings unterstützt <xref:Microsoft.Extensions.Options.IOptions%601> nicht die oben beschriebenen Szenarios von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="8a788-425">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="8a788-426">Sie können <xref:Microsoft.Extensions.Options.IOptions%601> weiterhin in bestehenden Frameworks und Bibliotheken verwenden, die bereits die <xref:Microsoft.Extensions.Options.IOptions%601>-Schnittstelle verwenden und nicht die von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> bereitgestellten Szenarios benötigen.</span><span class="sxs-lookup"><span data-stu-id="8a788-426">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="8a788-427">Allgemeine Optionskonfiguration</span><span class="sxs-lookup"><span data-stu-id="8a788-427">General options configuration</span></span>

<span data-ttu-id="8a788-428">Die allgemeine Optionskonfiguration wird als Beispiel &num;1 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="8a788-428">General options configuration is demonstrated as Example &num;1 in the sample app.</span></span>

<span data-ttu-id="8a788-429">Eine Optionsklasse muss nicht abstrakt sein und über einen öffentlichen parameterlosen Konstruktor verfügen.</span><span class="sxs-lookup"><span data-stu-id="8a788-429">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="8a788-430">Die folgende Klasse `MyOptions` verfügt über die zwei Eigenschaften: `Option1` und `Option2`.</span><span class="sxs-lookup"><span data-stu-id="8a788-430">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="8a788-431">Das Festlegen von Standardwerten ist optional, aber der Klassenkonstruktor im folgenden Beispiel legt den Standardwert von `Option1` fest.</span><span class="sxs-lookup"><span data-stu-id="8a788-431">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="8a788-432">`Option2` hat den Standardwert festgelegt, indem die Eigenschaft direkt initialisiert wurde (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="8a788-432">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="8a788-433">Die `MyOptions`-Klasse wird zum Dienstcontainer mit <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> hinzugefügt und an die Konfiguration gebunden:</span><span class="sxs-lookup"><span data-stu-id="8a788-433">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="8a788-434">Das folgende Seitenmodel verwendet [konstruktorbasierte Dependency Injection](xref:mvc/controllers/dependency-injection) mit <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>, um auf die Einstellungen zugreifen zu können (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="8a788-434">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="8a788-435">Die *appsettings.json*-Datei des Beispiels gibt Werte für `option1` und `option2` an:</span><span class="sxs-lookup"><span data-stu-id="8a788-435">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="8a788-436">Wenn die Anwendung ausgeführt wird, gibt die `OnGet`-Methode des Seitenmodells eine Zeichenfolge zurück, die die Werte der Optionsklasse anzeigt:</span><span class="sxs-lookup"><span data-stu-id="8a788-436">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="8a788-437">Wenn Sie eine benutzerdefinierte <xref:System.Configuration.ConfigurationBuilder>-Klasse verwenden, um Konfigurationsoptionen aus einer Einstellungsdatei zu laden, bestätigen Sie, dass der Basispfad ordnungsgemäß festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="8a788-437">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="8a788-438">Sie müssen den Basispfad nicht explizit festlegen, wenn Sie Konfigurationsoptionen über <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> aus der Einstellungsdatei laden.</span><span class="sxs-lookup"><span data-stu-id="8a788-438">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="8a788-439">Konfigurieren von einfachen Optionen mit einem Delegaten</span><span class="sxs-lookup"><span data-stu-id="8a788-439">Configure simple options with a delegate</span></span>

<span data-ttu-id="8a788-440">Das Konfigurieren von einfachen Optionen mit einem Delegaten wird als Beispiel &num;2 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="8a788-440">Configuring simple options with a delegate is demonstrated as Example &num;2 in the sample app.</span></span>

<span data-ttu-id="8a788-441">Verwenden Sie einen Delegaten zum Festlegen von Optionswerten.</span><span class="sxs-lookup"><span data-stu-id="8a788-441">Use a delegate to set options values.</span></span> <span data-ttu-id="8a788-442">Die Beispielanwendung verwendet die `MyOptionsWithDelegateConfig`-Klasse (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="8a788-442">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="8a788-443">Im folgenden Code wird ein zweiter <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Dienst zum Dienstcontainer hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="8a788-443">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="8a788-444">Er verwendet einen Delegaten zum Konfigurieren der Bindung mit `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="8a788-444">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="8a788-445">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="8a788-445">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="8a788-446">Sie können mehrere Konfigurationsanbieter hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="8a788-446">You can add multiple configuration providers.</span></span> <span data-ttu-id="8a788-447">Konfigurationsanbieter sind über NuGet-Pakete verfügbar und werden in der Reihenfolge ihrer Registrierung angewendet.</span><span class="sxs-lookup"><span data-stu-id="8a788-447">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="8a788-448">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="8a788-448">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="8a788-449">Jeder Aufruf von <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> fügt einen <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Dienst zum Dienstcontainer hinzu.</span><span class="sxs-lookup"><span data-stu-id="8a788-449">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="8a788-450">Im vorherigen Beispiel wurden die Werte von `Option1` und `Option2` beide in *appsettings.json* angegeben, aber die Werte von `Option1` und `Option2` werden vom konfigurierten Delegaten überschrieben.</span><span class="sxs-lookup"><span data-stu-id="8a788-450">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="8a788-451">Wenn mehr als ein Konfigurationsdienst aktiviert ist, *gewinnt* die letzte angegebene Konfigurationsquelle und legt den Konfigurationswert fest.</span><span class="sxs-lookup"><span data-stu-id="8a788-451">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="8a788-452">Wenn die Anwendung ausgeführt wird, gibt die `OnGet`-Methode des Seitenmodells eine Zeichenfolge zurück, die die Werte der Optionsklasse anzeigt:</span><span class="sxs-lookup"><span data-stu-id="8a788-452">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="8a788-453">Konfigurieren von Unteroptionen</span><span class="sxs-lookup"><span data-stu-id="8a788-453">Suboptions configuration</span></span>

<span data-ttu-id="8a788-454">Das Konfigurieren von Unteroptionen wird als Beispiel &num;3 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="8a788-454">Suboptions configuration is demonstrated as Example &num;3 in the sample app.</span></span>

<span data-ttu-id="8a788-455">Anwendungen sollten Optionsklassen erstellen, die für bestimmte Szenariogruppen (Klassen) in der Anwendung gelten.</span><span class="sxs-lookup"><span data-stu-id="8a788-455">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="8a788-456">Die Komponenten der Anwendung, die Konfigurationswerte erfordern, sollten nur über Zugriff auf die Konfigurationswerte verfügen, die sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="8a788-456">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="8a788-457">Beim Binden von Optionen zur Konfiguration ist jede Eigenschaft im Optionstyp an einen Konfigurationsschlüssel des `property[:sub-property:]`-Formulars gebunden.</span><span class="sxs-lookup"><span data-stu-id="8a788-457">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="8a788-458">Die `MyOptions.Option1`-Eigenschaft ist beispielsweise an den Schlüssel `Option1` gebunden, der von der `option1`-Eigenschaft in *appsettings.json* gelesen wird.</span><span class="sxs-lookup"><span data-stu-id="8a788-458">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="8a788-459">Im folgenden Code wird ein dritter <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Dienst zum Dienstcontainer hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="8a788-459">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="8a788-460">Er verbindet `MySubOptions` mit dem `subsection`-Abschnitt der *appSettings.json*-Datei:</span><span class="sxs-lookup"><span data-stu-id="8a788-460">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="8a788-461">Die `GetSection`-Erweiterungsmethode erfordert das [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/)-NuGet-Paket.</span><span class="sxs-lookup"><span data-stu-id="8a788-461">The `GetSection` extension method requires the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) NuGet package.</span></span> <span data-ttu-id="8a788-462">Wenn die Anwendung das [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app)-Metapaket (ASP.NET Core 2.1 oder höher) verwendet, ist das Paket automatisch eingeschlossen.</span><span class="sxs-lookup"><span data-stu-id="8a788-462">If the app uses the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later), the package is automatically included.</span></span>

<span data-ttu-id="8a788-463">Die *appsettings.json*-Datei des Beispiels definiert ein `subsection`-Element mit Schlüsseln für `suboption1` und `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="8a788-463">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="8a788-464">Die `MySubOptions`-Klasse definiert die Eigenschaften `SubOption1` und `SubOption2` zur Aufnahme der Optionswerte (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="8a788-464">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="8a788-465">Die `OnGet`-Methode des Seitenmodells gibt eine Zeichenfolge mit den Optionswerten (*Pages/Index.cshtml.cs*) zurück:</span><span class="sxs-lookup"><span data-stu-id="8a788-465">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="8a788-466">Wenn die App ausgeführt wird, gibt die `OnGet`-Methode eine Zeichenfolge zurück, die die Werte der untergeordneten Optionsklasse anzeigt:</span><span class="sxs-lookup"><span data-stu-id="8a788-466">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="8a788-467">Optionen, die durch ein Ansichtsmodell oder mit direkter View Injection bereitgestellt werden</span><span class="sxs-lookup"><span data-stu-id="8a788-467">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="8a788-468">Optionen, die durch ein Ansichtsmodell oder mit direkter View Injection bereitgestellt werden, werden im Beispiel &num;4 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="8a788-468">Options provided by a view model or with direct view injection is demonstrated as Example &num;4 in the sample app.</span></span>

<span data-ttu-id="8a788-469">Optionen können in einem Ansichtsmodell oder durch direkte Injection von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in eine Ansicht (*Pages/Index.cshtml.cs*) angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="8a788-469">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="8a788-470">Die Beispiel-App zeigt das Einfügen von `IOptionsMonitor<MyOptions>` mit einer `@inject`-Anweisung:</span><span class="sxs-lookup"><span data-stu-id="8a788-470">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="8a788-471">Wenn die App ausgeführt wird, werden die Optionswerte in der gerenderten Seite angezeigt:</span><span class="sxs-lookup"><span data-stu-id="8a788-471">When the app is run, the options values are shown in the rendered page:</span></span>

![Optionswerte Option1: value1_from_json und Option2: -1 werden aus dem Modell geladen und in die Ansicht eingefügt.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="8a788-473">Neuladen der Konfigurationsdaten mit IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="8a788-473">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="8a788-474">Das Neuladen der Konfigurationsdaten mit <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> wird im Beispiel &num;5 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="8a788-474">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example &num;5 in the sample app.</span></span>

<span data-ttu-id="8a788-475"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> unterstützt das Neuladen von Optionen mit minimalem Verarbeitungsaufwand.</span><span class="sxs-lookup"><span data-stu-id="8a788-475"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="8a788-476">Optionen werden einmal pro Anforderung berechnet. Dies geschieht, wenn auf sie zugegriffen wird und sie für die Dauer der Anforderung zwischengespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="8a788-476">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="8a788-477">Im folgenden Beispiel wird veranschaulicht, wie eine neue <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> nach den *appsettings.json*-Veränderungen erstellt wird (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="8a788-477">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="8a788-478">Mehrere Anforderungen an den Server geben konstante Werte zurück, die von der *appsettings.json*-Datei bereitgestellt werden, bis die Datei geändert wird und die Konfiguration neu lädt.</span><span class="sxs-lookup"><span data-stu-id="8a788-478">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="8a788-479">Die folgende Abbildung zeigt die anfänglichen `option1`- und `option2`-Werte, die aus der *appsettings.json*-Datei geladen werden:</span><span class="sxs-lookup"><span data-stu-id="8a788-479">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="8a788-480">Ändern Sie die Werte in der *appsettings.json*-Datei auf `value1_from_json UPDATED` und `200`.</span><span class="sxs-lookup"><span data-stu-id="8a788-480">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="8a788-481">Speichern Sie die *appsettings.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="8a788-481">Save the *appsettings.json* file.</span></span> <span data-ttu-id="8a788-482">Aktualisieren Sie den Browser, um festzustellen, ob die Optionswerte aktualisiert wurden:</span><span class="sxs-lookup"><span data-stu-id="8a788-482">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="8a788-483">Unterstützung für benannte Optionen mit IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="8a788-483">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="8a788-484">Unterstützung für benannte Optionen mit <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> wird als Beispiel &num;6 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="8a788-484">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example &num;6 in the sample app.</span></span>

<span data-ttu-id="8a788-485">Die Unterstützung für *benannte Optionen* ermöglicht es der Anwendung, zwischen den Konfigurationen benannter Optionen zu unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="8a788-485">*Named options* support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="8a788-486">In der Beispiel-App werden benannte Optionen mit [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) deklariert. Dadurch wird wiederum die Erweiterungsmethode [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="8a788-486">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="8a788-487">Die Beispielanwendung greift mit <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> auf die benannten Optionen zu (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="8a788-487">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="8a788-488">Bei der Ausführung der Beispielanwendung werden die benannten Optionen zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="8a788-488">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="8a788-489">`named_options_1`-Werte werden von der Konfiguration bereitgestellt. Sie werden aus der *appsettings.json*-Datei geladen.</span><span class="sxs-lookup"><span data-stu-id="8a788-489">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="8a788-490">`named_options_2`-Werte werden bereitgestellt vom:</span><span class="sxs-lookup"><span data-stu-id="8a788-490">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="8a788-491">`named_options_2`-Delegat in `ConfigureServices` für `Option1`.</span><span class="sxs-lookup"><span data-stu-id="8a788-491">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="8a788-492">Der Standardwert für `Option2` wird von der `MyOptions`-Klasse bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="8a788-492">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="8a788-493">Konfigurieren aller Optionen mit der ConfigureAll-Methode</span><span class="sxs-lookup"><span data-stu-id="8a788-493">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="8a788-494">Konfigurieren Sie alle Optionsinstanzen mit der <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>-Methode.</span><span class="sxs-lookup"><span data-stu-id="8a788-494">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="8a788-495">Der folgende Code konfiguriert `Option1` für alle Konfigurationsinstanzen mit einem gemeinsamen Wert.</span><span class="sxs-lookup"><span data-stu-id="8a788-495">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="8a788-496">Fügen Sie der `Startup.ConfigureServices`-Methode manuell den folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="8a788-496">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="8a788-497">Das Ausführen der Beispielanwendung nach dem Hinzufügen des Codes führt zu folgendem Ergebnis:</span><span class="sxs-lookup"><span data-stu-id="8a788-497">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="8a788-498">Alle Optionen sind benannte Instanzen.</span><span class="sxs-lookup"><span data-stu-id="8a788-498">All options are named instances.</span></span> <span data-ttu-id="8a788-499">Vorhandene <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Instanzen werden behandelt, als ob sie auf die `Options.DefaultName`-Instanz abzielen. Diese ist `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="8a788-499">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="8a788-500"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementiert auch <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="8a788-500"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="8a788-501">Die standardmäßige Implementierung von <xref:Microsoft.Extensions.Options.IOptionsFactory%601> verfügt über eine Logik, um jede einzelne entsprechend zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="8a788-501">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="8a788-502">Die benannte Option `null` wird verwendet, um auf alle benannten Instanzen anstelle einer bestimmten benannten Instanz abzuzielen (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> und <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> verwenden diese Konvention).</span><span class="sxs-lookup"><span data-stu-id="8a788-502">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="8a788-503">OptionsBuilder-API</span><span class="sxs-lookup"><span data-stu-id="8a788-503">OptionsBuilder API</span></span>

<span data-ttu-id="8a788-504"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> dient zum Konfigurieren von `TOptions`-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="8a788-504"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="8a788-505">`OptionsBuilder` optimiert die Erstellung von benannten Optionen, da es sich nur um einen einzelnen Parameter für den ersten `AddOptions<TOptions>(string optionsName)`-Aufruf handelt statt um alle nachfolgenden Aufrufe.</span><span class="sxs-lookup"><span data-stu-id="8a788-505">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="8a788-506">Die Optionsvalidierung und die `ConfigureOptions`-Überladungen, die Dienstabhängigkeiten akzeptieren, sind nur über `OptionsBuilder` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="8a788-506">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="8a788-507">Verwenden von DI-Diensten zum Konfigurieren von Optionen</span><span class="sxs-lookup"><span data-stu-id="8a788-507">Use DI services to configure options</span></span>

<span data-ttu-id="8a788-508">Beim Konfigurieren von Optionen haben Sie zwei Möglichkeiten, auf andere Dienste aus Dependency Injections zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="8a788-508">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="8a788-509">Übergeben eines Konfigurationsdelegaten an [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) auf [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="8a788-509">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="8a788-510">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) stellt Überladungen von [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) zur Verfügung, die es Ihnen ermöglichen, bis zu fünf Dienste zu verwenden, um Optionen zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="8a788-510">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="8a788-511">Erstellen Ihres eigenen Typs, der <xref:Microsoft.Extensions.Options.IConfigureOptions%601> oder <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementiert, und Registrieren des Typs als Dienst.</span><span class="sxs-lookup"><span data-stu-id="8a788-511">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="8a788-512">Es empfiehlt sich das Übergeben eines Konfigurationsdelegaten an [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), da das Erstellen eines Diensts etwas komplexer ist.</span><span class="sxs-lookup"><span data-stu-id="8a788-512">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="8a788-513">Selbst einen eigenen Typ zu erstellen ist gleichbedeutend mit dem, was das Framework für Sie übernimmt, wenn Sie [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) verwenden.</span><span class="sxs-lookup"><span data-stu-id="8a788-513">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="8a788-514">Wenn [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) aufgerufen wird, wird eine vorübergehende, generische <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> registriert, die über einen Konstruktor verfügt, der die angegeben generischen Diensttypen akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="8a788-514">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="8a788-515">Optionen für die Postkonfiguration</span><span class="sxs-lookup"><span data-stu-id="8a788-515">Options post-configuration</span></span>

<span data-ttu-id="8a788-516">Legen Sie die Postkonfiguration mit <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> fest.</span><span class="sxs-lookup"><span data-stu-id="8a788-516">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="8a788-517">Die Postkonfiguration erfolgt, nachdem die gesamte <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Konfiguration abgeschlossen ist:</span><span class="sxs-lookup"><span data-stu-id="8a788-517">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="8a788-518"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> ist nach dem Konfigurieren der benannten Optionen verfügbar:</span><span class="sxs-lookup"><span data-stu-id="8a788-518"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="8a788-519">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> zur Postkonfiguration aller Konfigurationsinstanzen:</span><span class="sxs-lookup"><span data-stu-id="8a788-519">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="8a788-520">Zugreifen auf Optionen während des Starts</span><span class="sxs-lookup"><span data-stu-id="8a788-520">Accessing options during startup</span></span>

<span data-ttu-id="8a788-521"><xref:Microsoft.Extensions.Options.IOptions%601> und <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> können in `Startup.Configure` verwendet werden, da Dienste erstellt werden, bevor die `Configure`-Methode ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="8a788-521"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="8a788-522">Verwenden Sie <xref:Microsoft.Extensions.Options.IOptions%601> oder <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> nicht in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="8a788-522">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8a788-523">Es kann einen inkonsistenten Optionszustand geben. Dies liegt an der Reihenfolge der Dienstregistrierungen.</span><span class="sxs-lookup"><span data-stu-id="8a788-523">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="8a788-524">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="8a788-524">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
