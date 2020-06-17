---
title: Optionsmuster in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie Optionsmuster verwenden, um Gruppen von zusammengehörigen Einstellungen in ASP.NET Core-Anwendungen darzustellen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/20/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/configuration/options
ms.openlocfilehash: 9a9febba060cca591f2cbcdc03cb4c35edcfdda7
ms.sourcegitcommit: 74d80a36103fdbd54baba0118535a4647f511913
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/08/2020
ms.locfileid: "84529662"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="1a893-103">Optionsmuster in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1a893-103">Options pattern in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1a893-104">Von [Kirk Larkin](https://twitter.com/serpent5) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1a893-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT).</span></span>

<span data-ttu-id="1a893-105">Das Optionsmuster verwendet Klassen, um stark typisierten Zugriff auf zusammengehörige Einstellungsgruppen zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="1a893-105">The options pattern uses classes to provide strongly typed access to groups of related settings.</span></span> <span data-ttu-id="1a893-106">Wenn [Konfigurationseinstellungen](xref:fundamentals/configuration/index) nach Szenario in separate Klassen isoliert werden, entspricht die Anwendung zwei wichtigen Prinzipien der Softwareentwicklung:</span><span class="sxs-lookup"><span data-stu-id="1a893-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="1a893-107">Das [Schnittstellentrennungsprinzip oder die Kapselung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Szenarios (Klassen), die von Konfigurationseinstellungen abhängen, sind nur von den Konfigurationseinstellungen abhängig, die sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="1a893-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="1a893-108">[Trennung von Zuständigkeiten:](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) Einstellungen für verschiedene App-Komponenten sind nicht voneinander abhängig und nicht aneinander gekoppelt.</span><span class="sxs-lookup"><span data-stu-id="1a893-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="1a893-109">Optionen bieten auch einen Mechanismus, um Konfigurationsdaten zu validieren.</span><span class="sxs-lookup"><span data-stu-id="1a893-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="1a893-110">Weitere Informationen finden Sie im Abschnitt [Optionsvalidierung](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="1a893-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="1a893-111">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1a893-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="optpat"></a>

## <a name="bind-hierarchical-configuration"></a><span data-ttu-id="1a893-112">Binden von hierarchischen Konfigurationsdaten</span><span class="sxs-lookup"><span data-stu-id="1a893-112">Bind hierarchical configuration</span></span>

[!INCLUDE[](~/includes/bind.md)]

<a name="oi"></a>

## <a name="options-interfaces"></a><span data-ttu-id="1a893-113">Optionenschnittstellen</span><span class="sxs-lookup"><span data-stu-id="1a893-113">Options interfaces</span></span>

<span data-ttu-id="1a893-114"><xref:Microsoft.Extensions.Options.IOptions%601>:</span><span class="sxs-lookup"><span data-stu-id="1a893-114"><xref:Microsoft.Extensions.Options.IOptions%601>:</span></span>

* <span data-ttu-id="1a893-115">Unterstützt ***nicht***:</span><span class="sxs-lookup"><span data-stu-id="1a893-115">Does ***not*** support:</span></span>
  * <span data-ttu-id="1a893-116">Das Lesen von Konfigurationsdaten, nachdem die App gestartet wurde</span><span class="sxs-lookup"><span data-stu-id="1a893-116">Reading of configuration data after the app has started.</span></span>
  * [<span data-ttu-id="1a893-117">Benannte Optionen</span><span class="sxs-lookup"><span data-stu-id="1a893-117">Named options</span></span>](#named)
* <span data-ttu-id="1a893-118">Ist als [Singleton](xref:fundamentals/dependency-injection#singleton) registriert und kann in eine beliebige [Dienstlebensdauer](xref:fundamentals/dependency-injection#service-lifetimes) eingefügt werden</span><span class="sxs-lookup"><span data-stu-id="1a893-118">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="1a893-119"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span><span class="sxs-lookup"><span data-stu-id="1a893-119"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span></span>

* <span data-ttu-id="1a893-120">Ist in Szenarios nützlich, in denen Optionen bei jeder Anforderung neu berechnet werden sollten.</span><span class="sxs-lookup"><span data-stu-id="1a893-120">Is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="1a893-121">Weitere Informationen finden Sie unter [Verwenden von IOptionsSnapshot zum Lesen aktualisierter Daten](#ios).</span><span class="sxs-lookup"><span data-stu-id="1a893-121">For more information, see [Use IOptionsSnapshot to read updated data](#ios).</span></span>
* <span data-ttu-id="1a893-122">Ist als [Bereichsbezogen](xref:fundamentals/dependency-injection#scoped) registriert und kann deshalb nicht in einen Singleton-Dienst eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="1a893-122">Is registered as [Scoped](xref:fundamentals/dependency-injection#scoped) and therefore cannot be injected into a Singleton service.</span></span>
* <span data-ttu-id="1a893-123">Unterstützt [benannten Optionen](#named)</span><span class="sxs-lookup"><span data-stu-id="1a893-123">Supports [named options](#named)</span></span>

<span data-ttu-id="1a893-124"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span><span class="sxs-lookup"><span data-stu-id="1a893-124"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

* <span data-ttu-id="1a893-125">Wird verwendet, um Optionen abzurufen und Benachrichtigungen über Optionen für `TOptions`-Instanzen zu verwalten</span><span class="sxs-lookup"><span data-stu-id="1a893-125">Is used to retrieve options and manage options notifications for `TOptions` instances.</span></span>
* <span data-ttu-id="1a893-126">Ist als [Singleton](xref:fundamentals/dependency-injection#singleton) registriert und kann in eine beliebige [Dienstlebensdauer](xref:fundamentals/dependency-injection#service-lifetimes) eingefügt werden</span><span class="sxs-lookup"><span data-stu-id="1a893-126">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>
* <span data-ttu-id="1a893-127">Unterstützt:</span><span class="sxs-lookup"><span data-stu-id="1a893-127">Supports:</span></span>
  * <span data-ttu-id="1a893-128">Änderungsbenachrichtigungen</span><span class="sxs-lookup"><span data-stu-id="1a893-128">Change notifications</span></span>
  * [<span data-ttu-id="1a893-129">Benannte Optionen</span><span class="sxs-lookup"><span data-stu-id="1a893-129">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
  * [<span data-ttu-id="1a893-130">Erneut ladbare Konfiguration</span><span class="sxs-lookup"><span data-stu-id="1a893-130">Reloadable configuration</span></span>](#ios)
  * <span data-ttu-id="1a893-131">Selektive Optionsvalidierung (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="1a893-131">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>
  
<span data-ttu-id="1a893-132">Mit [Postkonfigurationsszenarios](#options-post-configuration) können Sie Optionen festlegen oder ändern, nachdem alle <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Konfigurationen durchgeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="1a893-132">[Post-configuration](#options-post-configuration) scenarios enable setting or changing options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="1a893-133"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> ist für das Erstellen neuer Optionsinstanzen zuständig.</span><span class="sxs-lookup"><span data-stu-id="1a893-133"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="1a893-134">Es verfügt über eine einzelne <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>-Methode.</span><span class="sxs-lookup"><span data-stu-id="1a893-134">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="1a893-135">Die Standardimplementierung akzeptiert alle registrierten <xref:Microsoft.Extensions.Options.IConfigureOptions%601> und <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> und führt alle Konfigurationen zuerst und die Postkonfigurationen danach aus.</span><span class="sxs-lookup"><span data-stu-id="1a893-135">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="1a893-136">Es wird zwischen <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> und <xref:Microsoft.Extensions.Options.IConfigureOptions%601> unterschieden, und es werden nur die entsprechenden Schnittstellen aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="1a893-136">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="1a893-137"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> wird von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> zum Zwischenspeichern der `TOptions`-Instanzen verwendet.</span><span class="sxs-lookup"><span data-stu-id="1a893-137"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="1a893-138"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> erklärt Optionsinstanzen im Monitor für ungültig, sodass der Wert neu berechnet wird (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="1a893-138">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="1a893-139">Werte können manuell mit <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> eingeführt werden.</span><span class="sxs-lookup"><span data-stu-id="1a893-139">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="1a893-140">Die <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>-Methode wird verwendet, wenn alle benannten Instanzen bei Bedarf neu erstellt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="1a893-140">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<a name="ios"></a>

## <a name="use-ioptionssnapshot-to-read-updated-data"></a><span data-ttu-id="1a893-141">Verwenden von IOptionsSnapshot zum Lesen aktualisierter Daten</span><span class="sxs-lookup"><span data-stu-id="1a893-141">Use IOptionsSnapshot to read updated data</span></span>

<span data-ttu-id="1a893-142">Mithilfe von <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> werden Optionen einmal pro Anforderung berechnet. Dies geschieht, wenn auf sie zugegriffen wird und sie für die Dauer der Anforderung zwischengespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="1a893-142">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span> <span data-ttu-id="1a893-143">Änderungen an der Konfiguration werden gelesen, nachdem die App gestartet wurde, wenn Konfigurationsanbieter verwendet werden, die das Lesen aktualisierter Konfigurationswerte unterstützen.</span><span class="sxs-lookup"><span data-stu-id="1a893-143">Changes to the configuration are read after the app starts when using configuration providers that support reading updated configuration values.</span></span>

<span data-ttu-id="1a893-144">Der Unterschied zwischen `IOptionsMonitor` und `IOptionsSnapshot` ist folgender:</span><span class="sxs-lookup"><span data-stu-id="1a893-144">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="1a893-145">`IOptionsMonitor` ist ein [Singleton-Dienst](xref:fundamentals/dependency-injection#singleton), der zu jeder Zeit aktuelle Optionswerte empfängt, was insbesondere bei Singleton-Abhängigkeiten nützlich ist.</span><span class="sxs-lookup"><span data-stu-id="1a893-145">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="1a893-146">`IOptionsSnapshot` ist ein [bereichsbezogener Dienst](xref:fundamentals/dependency-injection#scoped) und bietet eine Momentaufnahme der Optionen zu dem Zeitpunkt, da das `IOptionsSnapshot<T>`-Objekt konstruiert wird.</span><span class="sxs-lookup"><span data-stu-id="1a893-146">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="1a893-147">Momentaufnahmen von Optionen sind für die Verwendung mit vorübergehenden und bereichsbezogenen Abhängigkeiten bestimmt.</span><span class="sxs-lookup"><span data-stu-id="1a893-147">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="1a893-148">Der folgende Code verwendet <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>.</span><span class="sxs-lookup"><span data-stu-id="1a893-148">The following code uses <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestSnap.cshtml.cs?name=snippet)]

<span data-ttu-id="1a893-149">Der folgende Code registriert eine Konfigurationsinstanz, mit der `MyOptions` eine Bindung herstellt:</span><span class="sxs-lookup"><span data-stu-id="1a893-149">The following code registers a configuration instance which `MyOptions` binds against:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="1a893-150">Im vorangehenden Code werden Änderungen an der JSON-Konfigurationsdatei nach dem Start der App gelesen.</span><span class="sxs-lookup"><span data-stu-id="1a893-150">In the preceding code, changes to the JSON configuration file after the app has started are read.</span></span>

## <a name="ioptionsmonitor"></a><span data-ttu-id="1a893-151">IOptionsMonitor</span><span class="sxs-lookup"><span data-stu-id="1a893-151">IOptionsMonitor</span></span>

<span data-ttu-id="1a893-152">Der folgende Code registriert eine Konfigurationsinstanz, mit der `MyOptions` eine Bindung herstellt.</span><span class="sxs-lookup"><span data-stu-id="1a893-152">The following code registers a configuration instance which `MyOptions` binds against.</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="1a893-153">Im folgenden Beispiel wird <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> verwendet:</span><span class="sxs-lookup"><span data-stu-id="1a893-153">The following example uses <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestMonitor.cshtml.cs?name=snippet)]

<span data-ttu-id="1a893-154">Im vorangehenden Code werden standardmäßig Änderungen an der JSON-Konfigurationsdatei gelesen, nachdem die App gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="1a893-154">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<a name="named"></a>

## <a name="named-options-support-using-iconfigurenamedoptions"></a><span data-ttu-id="1a893-155">Unterstützung für benannte Optionen mit IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="1a893-155">Named options support using IConfigureNamedOptions</span></span>

<span data-ttu-id="1a893-156">Benannte Optionen...</span><span class="sxs-lookup"><span data-stu-id="1a893-156">Named options:</span></span>

* <span data-ttu-id="1a893-157">sind nützlich, wenn mehrere Konfigurationsabschnitte an die gleichen Eigenschaften gebunden werden.</span><span class="sxs-lookup"><span data-stu-id="1a893-157">Are useful when multiple configuration sections bind to the same properties.</span></span>
* <span data-ttu-id="1a893-158">unterscheiden zwischen Groß-/Kleinschreibung.</span><span class="sxs-lookup"><span data-stu-id="1a893-158">Are case sensitive.</span></span>

<span data-ttu-id="1a893-159">Sehen Sie sich die nachfolgende Datei *appsettings.json* an:</span><span class="sxs-lookup"><span data-stu-id="1a893-159">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/appsettings.NO.json)]

<span data-ttu-id="1a893-160">Anstatt zwei Klassen für die Bindung von `TopItem:Month` und `TopItem:Year` zu erstellen, wird folgende Klasse für jeden Abschnitt verwendet:</span><span class="sxs-lookup"><span data-stu-id="1a893-160">Rather than creating two classes to bind `TopItem:Month` and `TopItem:Year`, the following class is used for each section:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Models/TopItemSettings.cs)]

<span data-ttu-id="1a893-161">Der folgende Code dient zum Konfigurieren der benannten Optionen:</span><span class="sxs-lookup"><span data-stu-id="1a893-161">The following code configures the named options:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/StartupNO.cs?name=snippet_Example2)]

<span data-ttu-id="1a893-162">Der folgende Code zeigt die benannten Optionen an:</span><span class="sxs-lookup"><span data-stu-id="1a893-162">The following code displays the named options:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestNO.cshtml.cs?name=snippet)]

<span data-ttu-id="1a893-163">Alle Optionen sind benannte Instanzen.</span><span class="sxs-lookup"><span data-stu-id="1a893-163">All options are named instances.</span></span> <span data-ttu-id="1a893-164"><xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Instanzen werden behandelt, als würden sie die `Options.DefaultName`-Instanz anzielen. Diese ist `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="1a893-164"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="1a893-165"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementiert auch <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="1a893-165"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="1a893-166">Die standardmäßige Implementierung von <xref:Microsoft.Extensions.Options.IOptionsFactory%601> verfügt über eine Logik, um jede einzelne entsprechend zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="1a893-166">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="1a893-167">Die benannte Option `null` wird verwendet, um auf alle benannten Instanzen anstelle einer bestimmten benannten Instanz abzuzielen.</span><span class="sxs-lookup"><span data-stu-id="1a893-167">The `null` named option is used to target all of the named instances instead of a specific named instance.</span></span> <span data-ttu-id="1a893-168"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> und <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> verwenden diese Konvention.</span><span class="sxs-lookup"><span data-stu-id="1a893-168"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention.</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="1a893-169">OptionsBuilder-API</span><span class="sxs-lookup"><span data-stu-id="1a893-169">OptionsBuilder API</span></span>

<span data-ttu-id="1a893-170"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> dient zum Konfigurieren von `TOptions`-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="1a893-170"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="1a893-171">`OptionsBuilder` optimiert die Erstellung von benannten Optionen, da es sich nur um einen einzelnen Parameter für den ersten `AddOptions<TOptions>(string optionsName)`-Aufruf handelt statt um alle nachfolgenden Aufrufe.</span><span class="sxs-lookup"><span data-stu-id="1a893-171">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="1a893-172">Die Optionsvalidierung und die `ConfigureOptions`-Überladungen, die Dienstabhängigkeiten akzeptieren, sind nur über `OptionsBuilder` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="1a893-172">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

<span data-ttu-id="1a893-173">`OptionsBuilder` wird im Abschnitt [Überprüfung von Optionen](#val) verwendet.</span><span class="sxs-lookup"><span data-stu-id="1a893-173">`OptionsBuilder` is used in the [Options validation](#val) section.</span></span>

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="1a893-174">Verwenden von DI-Diensten zum Konfigurieren von Optionen</span><span class="sxs-lookup"><span data-stu-id="1a893-174">Use DI services to configure options</span></span>

<span data-ttu-id="1a893-175">Der Zugriff auf Dienste ist über die Dependency Injection möglich, während Optionen auf zwei Arten konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="1a893-175">Services can be accessed from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="1a893-176">Übergeben Sie einen Konfigurationsdelegaten an [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) in [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="1a893-176">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="1a893-177">`OptionsBuilder<TOptions>` stellt Überladungen von [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) zur Verfügung, die es ermöglichen, bis zu fünf Dienste zu verwenden, um Optionen zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="1a893-177">`OptionsBuilder<TOptions>` provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow use of up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="1a893-178">Erstellen Sie einen Typ, der <xref:Microsoft.Extensions.Options.IConfigureOptions%601> oder <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementiert, und registrieren Sie den Typ als Dienst.</span><span class="sxs-lookup"><span data-stu-id="1a893-178">Create a type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="1a893-179">Es empfiehlt sich das Übergeben eines Konfigurationsdelegaten an [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), da das Erstellen eines Diensts etwas komplexer ist.</span><span class="sxs-lookup"><span data-stu-id="1a893-179">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="1a893-180">Das Erstellen eines Typs entspricht den Aktionen des Frameworks beim Aufruf von [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="1a893-180">Creating a type is equivalent to what the framework does when calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="1a893-181">Wenn [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) aufgerufen wird, wird eine vorübergehende, generische <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> registriert, die über einen Konstruktor verfügt, der die angegeben generischen Diensttypen akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="1a893-181">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

<a name="val"></a>

## <a name="options-validation"></a><span data-ttu-id="1a893-182">Überprüfung von Optionen</span><span class="sxs-lookup"><span data-stu-id="1a893-182">Options validation</span></span>

<span data-ttu-id="1a893-183">Bei der Überprüfung der Optionen können Optionswerte überprüft werden.</span><span class="sxs-lookup"><span data-stu-id="1a893-183">Options validation enables option values to be validated.</span></span>

<span data-ttu-id="1a893-184">Sehen Sie sich die nachfolgende Datei *appsettings.json* an:</span><span class="sxs-lookup"><span data-stu-id="1a893-184">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsValidationSample/appsettings.Dev2.json)]

<span data-ttu-id="1a893-185">Die folgende Klasse erstellt eine Bindung zum `"MyConfig"`-Konfigurationsabschnitt und wendet einige `DataAnnotations`-Regeln an:</span><span class="sxs-lookup"><span data-stu-id="1a893-185">The following class binds to the `"MyConfig"` configuration section and applies a couple of `DataAnnotations` rules:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigOptions.cs?name=snippet)]

<span data-ttu-id="1a893-186">Der folgende Code</span><span class="sxs-lookup"><span data-stu-id="1a893-186">The following code:</span></span>

* <span data-ttu-id="1a893-187">Mit dem Code wird die <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A>-Methode abgerufen, um eine [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)-Klasse zu erhalten, die an die `MyConfigOptions`-Klasse gebunden wird.</span><span class="sxs-lookup"><span data-stu-id="1a893-187">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> to get an [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) that binds to the `MyConfigOptions` class.</span></span>
* <span data-ttu-id="1a893-188">Mit dem Code wird <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> abgerufen, um die Validierung mithilfe von `DataAnnotations` zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="1a893-188">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> to enable validation using `DataAnnotations`.</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup.cs?name=snippet)]

<span data-ttu-id="1a893-189">Die `ValidateDataAnnotations`-Erweiterungsmethode wird im NuGet-Paket [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) definiert.</span><span class="sxs-lookup"><span data-stu-id="1a893-189">The `ValidateDataAnnotations` extension method is defined in the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) NuGet package.</span></span> <span data-ttu-id="1a893-190">Für Web-Apps, die das `Microsoft.NET.Sdk.Web` SDK verwenden, wird auf dieses Paket implizit über das geteilte Framework verwiesen.</span><span class="sxs-lookup"><span data-stu-id="1a893-190">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, this package is referenced implicitly from the shared framework.</span></span>

<span data-ttu-id="1a893-191">Der folgende Code zeigt die Konfigurationswerte oder die Überprüfungsfehler an:</span><span class="sxs-lookup"><span data-stu-id="1a893-191">The following code displays the configuration values or the validation errors:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="1a893-192">Der folgende Code wendet mithilfe eines Delegaten eine komplexere Überprüfungsregel an:</span><span class="sxs-lookup"><span data-stu-id="1a893-192">The following code applies a more complex validation rule using a delegate:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup2.cs?name=snippet)]

### <a name="ivalidateoptions-for-complex-validation"></a><span data-ttu-id="1a893-193">IValidateOptions für die komplexe Überprüfung</span><span class="sxs-lookup"><span data-stu-id="1a893-193">IValidateOptions for complex validation</span></span>

<span data-ttu-id="1a893-194">Die folgende Klasse implementiert <xref:Microsoft.Extensions.Options.IValidateOptions`1>:</span><span class="sxs-lookup"><span data-stu-id="1a893-194">The following class implements <xref:Microsoft.Extensions.Options.IValidateOptions`1>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigValidation.cs?name=snippet)]

<span data-ttu-id="1a893-195">`IValidateOptions` ermöglicht das Verschieben des Überprüfungscodes aus `StartUp` und in eine Klasse.</span><span class="sxs-lookup"><span data-stu-id="1a893-195">`IValidateOptions` enables moving the validation code out of `StartUp` and into a class.</span></span>

<span data-ttu-id="1a893-196">Mit dem vorangehenden Code wird die Überprüfung in `Startup.ConfigureServices` aktiviert:</span><span class="sxs-lookup"><span data-stu-id="1a893-196">Using the preceding code, validation is enabled in `Startup.ConfigureServices` with the following code:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/StartupValidation.cs?name=snippet)]

<!-- The following comment doesn't seem that useful 
Options validation doesn't guard against options modifications after the options instance is created. For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed. The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.

The `Validate` method accepts a `Func<TOptions, bool>`. To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:

* Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions` validates:

* A specific named options instance.
* All options when `name` is `null`.

Return a `ValidateOptionsResult` from your implementation of the interface:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`. `Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.

-->

## <a name="options-post-configuration"></a><span data-ttu-id="1a893-197">Optionen für die Postkonfiguration</span><span class="sxs-lookup"><span data-stu-id="1a893-197">Options post-configuration</span></span>

<span data-ttu-id="1a893-198">Legen Sie die Postkonfiguration mit <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> fest.</span><span class="sxs-lookup"><span data-stu-id="1a893-198">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="1a893-199">Die Postkonfiguration erfolgt, nachdem die gesamte <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Konfiguration abgeschlossen ist:</span><span class="sxs-lookup"><span data-stu-id="1a893-199">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="1a893-200"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> ist nach dem Konfigurieren der benannten Optionen verfügbar:</span><span class="sxs-lookup"><span data-stu-id="1a893-200"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="1a893-201">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> zur Postkonfiguration aller Konfigurationsinstanzen:</span><span class="sxs-lookup"><span data-stu-id="1a893-201">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="1a893-202">Zugreifen auf Optionen während des Starts</span><span class="sxs-lookup"><span data-stu-id="1a893-202">Accessing options during startup</span></span>

<span data-ttu-id="1a893-203"><xref:Microsoft.Extensions.Options.IOptions%601> und <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> können in `Startup.Configure` verwendet werden, da Dienste erstellt werden, bevor die `Configure`-Methode ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="1a893-203"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="1a893-204">Verwenden Sie <xref:Microsoft.Extensions.Options.IOptions%601> oder <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> nicht in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="1a893-204">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="1a893-205">Es kann einen inkonsistenten Optionszustand geben. Dies liegt an der Reihenfolge der Dienstregistrierungen.</span><span class="sxs-lookup"><span data-stu-id="1a893-205">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

## <a name="optionsconfigurationextensions-nuget-package"></a><span data-ttu-id="1a893-206">NuGet-Paket Options.ConfigurationExtensions</span><span class="sxs-lookup"><span data-stu-id="1a893-206">Options.ConfigurationExtensions NuGet package</span></span>

<span data-ttu-id="1a893-207">Auf das Paket [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) wird implizit in ASP.NET Core-Apps verwiesen.</span><span class="sxs-lookup"><span data-stu-id="1a893-207">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="1a893-208">Das Optionsmuster verwendet Klassen, um Gruppen von zusammengehörigen Einstellungen darzustellen.</span><span class="sxs-lookup"><span data-stu-id="1a893-208">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="1a893-209">Wenn [Konfigurationseinstellungen](xref:fundamentals/configuration/index) nach Szenario in separate Klassen isoliert werden, entspricht die Anwendung zwei wichtigen Prinzipien der Softwareentwicklung:</span><span class="sxs-lookup"><span data-stu-id="1a893-209">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="1a893-210">Das [Schnittstellentrennungsprinzip oder die Kapselung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Szenarios (Klassen), die von Konfigurationseinstellungen abhängen, sind nur von den Konfigurationseinstellungen abhängig, die sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="1a893-210">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="1a893-211">[Trennung von Zuständigkeiten:](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) Einstellungen für verschiedene App-Komponenten sind nicht voneinander abhängig und nicht aneinander gekoppelt.</span><span class="sxs-lookup"><span data-stu-id="1a893-211">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="1a893-212">Optionen bieten auch einen Mechanismus, um Konfigurationsdaten zu validieren.</span><span class="sxs-lookup"><span data-stu-id="1a893-212">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="1a893-213">Weitere Informationen finden Sie im Abschnitt [Optionsvalidierung](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="1a893-213">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="1a893-214">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1a893-214">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1a893-215">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="1a893-215">Prerequisites</span></span>

<span data-ttu-id="1a893-216">Verweisen Sie auf das [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app), oder fügen Sie einen Paketverweis auf das Paket [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) hinzu.</span><span class="sxs-lookup"><span data-stu-id="1a893-216">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="1a893-217">Optionenschnittstellen</span><span class="sxs-lookup"><span data-stu-id="1a893-217">Options interfaces</span></span>

<span data-ttu-id="1a893-218"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> wird verwendet, um Optionen abzurufen und Benachrichtigungen über Optionen für `TOptions`-Instanzen zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="1a893-218"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="1a893-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> unterstützt die folgenden Szenarios:</span><span class="sxs-lookup"><span data-stu-id="1a893-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="1a893-220">Änderungsbenachrichtigungen</span><span class="sxs-lookup"><span data-stu-id="1a893-220">Change notifications</span></span>
* [<span data-ttu-id="1a893-221">Benannte Optionen</span><span class="sxs-lookup"><span data-stu-id="1a893-221">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="1a893-222">Erneut ladbare Konfiguration</span><span class="sxs-lookup"><span data-stu-id="1a893-222">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="1a893-223">Selektive Optionsvalidierung (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="1a893-223">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="1a893-224">Mit [Postkonfigurationsszenarien](#options-post-configuration) können Sie Optionen festlegen oder ändern, nachdem alle <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Konfigurationen durchgeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="1a893-224">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="1a893-225"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> ist für das Erstellen neuer Optionsinstanzen zuständig.</span><span class="sxs-lookup"><span data-stu-id="1a893-225"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="1a893-226">Es verfügt über eine einzelne <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>-Methode.</span><span class="sxs-lookup"><span data-stu-id="1a893-226">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="1a893-227">Die Standardimplementierung akzeptiert alle registrierten <xref:Microsoft.Extensions.Options.IConfigureOptions%601> und <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> und führt alle Konfigurationen zuerst und die Postkonfigurationen danach aus.</span><span class="sxs-lookup"><span data-stu-id="1a893-227">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="1a893-228">Es wird zwischen <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> und <xref:Microsoft.Extensions.Options.IConfigureOptions%601> unterschieden, und es werden nur die entsprechenden Schnittstellen aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="1a893-228">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="1a893-229"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> wird von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> zum Zwischenspeichern der `TOptions`-Instanzen verwendet.</span><span class="sxs-lookup"><span data-stu-id="1a893-229"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="1a893-230"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> erklärt Optionsinstanzen im Monitor für ungültig, sodass der Wert neu berechnet wird (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="1a893-230">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="1a893-231">Werte können manuell mit <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> eingeführt werden.</span><span class="sxs-lookup"><span data-stu-id="1a893-231">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="1a893-232">Die <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>-Methode wird verwendet, wenn alle benannten Instanzen bei Bedarf neu erstellt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="1a893-232">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="1a893-233"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> ist nützlich in Szenarien, in denen Optionen bei jeder Anforderung neu berechnet werden sollten.</span><span class="sxs-lookup"><span data-stu-id="1a893-233"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="1a893-234">Weitere Informationen finden Sie im Abschnitt [Neuladen der Konfigurationsdaten mit IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="1a893-234">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="1a893-235"><xref:Microsoft.Extensions.Options.IOptions%601> kann zum Unterstützen von Optionen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="1a893-235"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="1a893-236">Allerdings unterstützt <xref:Microsoft.Extensions.Options.IOptions%601> nicht die oben beschriebenen Szenarios von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="1a893-236">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="1a893-237">Sie können <xref:Microsoft.Extensions.Options.IOptions%601> weiterhin in bestehenden Frameworks und Bibliotheken verwenden, die bereits die <xref:Microsoft.Extensions.Options.IOptions%601>-Schnittstelle verwenden und nicht die von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> bereitgestellten Szenarios benötigen.</span><span class="sxs-lookup"><span data-stu-id="1a893-237">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="1a893-238">Allgemeine Optionskonfiguration</span><span class="sxs-lookup"><span data-stu-id="1a893-238">General options configuration</span></span>

<span data-ttu-id="1a893-239">Die allgemeine Optionskonfiguration wird als Beispiel 1 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="1a893-239">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="1a893-240">Eine Optionsklasse muss nicht abstrakt sein und über einen öffentlichen parameterlosen Konstruktor verfügen.</span><span class="sxs-lookup"><span data-stu-id="1a893-240">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="1a893-241">Die folgende Klasse `MyOptions` verfügt über die zwei Eigenschaften: `Option1` und `Option2`.</span><span class="sxs-lookup"><span data-stu-id="1a893-241">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="1a893-242">Das Festlegen von Standardwerten ist optional, aber der Klassenkonstruktor im folgenden Beispiel legt den Standardwert von `Option1` fest.</span><span class="sxs-lookup"><span data-stu-id="1a893-242">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="1a893-243">`Option2` hat den Standardwert festgelegt, indem die Eigenschaft direkt initialisiert wurde (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="1a893-243">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="1a893-244">Die `MyOptions`-Klasse wird zum Dienstcontainer mit <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> hinzugefügt und an die Konfiguration gebunden:</span><span class="sxs-lookup"><span data-stu-id="1a893-244">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="1a893-245">Das folgende Seitenmodel verwendet [konstruktorbasierte Dependency Injection](xref:mvc/controllers/dependency-injection) mit <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>, um auf die Einstellungen zugreifen zu können (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="1a893-245">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="1a893-246">Die *appsettings.json*-Datei des Beispiels gibt Werte für `option1` und `option2` an:</span><span class="sxs-lookup"><span data-stu-id="1a893-246">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="1a893-247">Wenn die Anwendung ausgeführt wird, gibt die `OnGet`-Methode des Seitenmodells eine Zeichenfolge zurück, die die Werte der Optionsklasse anzeigt:</span><span class="sxs-lookup"><span data-stu-id="1a893-247">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="1a893-248">Wenn Sie eine benutzerdefinierte <xref:System.Configuration.ConfigurationBuilder>-Klasse verwenden, um Konfigurationsoptionen aus einer Einstellungsdatei zu laden, bestätigen Sie, dass der Basispfad ordnungsgemäß festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="1a893-248">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="1a893-249">Sie müssen den Basispfad nicht explizit festlegen, wenn Sie Konfigurationsoptionen über <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> aus der Einstellungsdatei laden.</span><span class="sxs-lookup"><span data-stu-id="1a893-249">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="1a893-250">Konfigurieren von einfachen Optionen mit einem Delegaten</span><span class="sxs-lookup"><span data-stu-id="1a893-250">Configure simple options with a delegate</span></span>

<span data-ttu-id="1a893-251">Das Konfigurieren von einfachen Optionen mit einem Delegaten wird als Beispiel 2 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="1a893-251">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="1a893-252">Verwenden Sie einen Delegaten zum Festlegen von Optionswerten.</span><span class="sxs-lookup"><span data-stu-id="1a893-252">Use a delegate to set options values.</span></span> <span data-ttu-id="1a893-253">Die Beispielanwendung verwendet die `MyOptionsWithDelegateConfig`-Klasse (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="1a893-253">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="1a893-254">Im folgenden Code wird ein zweiter <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Dienst zum Dienstcontainer hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="1a893-254">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="1a893-255">Er verwendet einen Delegaten zum Konfigurieren der Bindung mit `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="1a893-255">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="1a893-256">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="1a893-256">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="1a893-257">Sie können mehrere Konfigurationsanbieter hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="1a893-257">You can add multiple configuration providers.</span></span> <span data-ttu-id="1a893-258">Konfigurationsanbieter sind über NuGet-Pakete verfügbar und werden in der Reihenfolge ihrer Registrierung angewendet.</span><span class="sxs-lookup"><span data-stu-id="1a893-258">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="1a893-259">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="1a893-259">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="1a893-260">Jeder Aufruf von <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> fügt einen <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Dienst zum Dienstcontainer hinzu.</span><span class="sxs-lookup"><span data-stu-id="1a893-260">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="1a893-261">Im vorherigen Beispiel wurden die Werte von `Option1` und `Option2` beide in *appsettings.json* angegeben, aber die Werte von `Option1` und `Option2` werden vom konfigurierten Delegaten überschrieben.</span><span class="sxs-lookup"><span data-stu-id="1a893-261">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="1a893-262">Wenn mehr als ein Konfigurationsdienst aktiviert ist, *gewinnt* die letzte angegebene Konfigurationsquelle und legt den Konfigurationswert fest.</span><span class="sxs-lookup"><span data-stu-id="1a893-262">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="1a893-263">Wenn die Anwendung ausgeführt wird, gibt die `OnGet`-Methode des Seitenmodells eine Zeichenfolge zurück, die die Werte der Optionsklasse anzeigt:</span><span class="sxs-lookup"><span data-stu-id="1a893-263">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="1a893-264">Konfigurieren von Unteroptionen</span><span class="sxs-lookup"><span data-stu-id="1a893-264">Suboptions configuration</span></span>

<span data-ttu-id="1a893-265">Das Konfigurieren von Unteroptionen wird als Beispiel 3 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="1a893-265">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="1a893-266">Anwendungen sollten Optionsklassen erstellen, die für bestimmte Szenariogruppen (Klassen) in der Anwendung gelten.</span><span class="sxs-lookup"><span data-stu-id="1a893-266">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="1a893-267">Die Komponenten der Anwendung, die Konfigurationswerte erfordern, sollten nur über Zugriff auf die Konfigurationswerte verfügen, die sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="1a893-267">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="1a893-268">Beim Binden von Optionen zur Konfiguration ist jede Eigenschaft im Optionstyp an einen Konfigurationsschlüssel des `property[:sub-property:]`-Formulars gebunden.</span><span class="sxs-lookup"><span data-stu-id="1a893-268">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="1a893-269">Die `MyOptions.Option1`-Eigenschaft ist beispielsweise an den Schlüssel `Option1` gebunden, der von der `option1`-Eigenschaft in *appsettings.json* gelesen wird.</span><span class="sxs-lookup"><span data-stu-id="1a893-269">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="1a893-270">Im folgenden Code wird ein dritter <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Dienst zum Dienstcontainer hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="1a893-270">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="1a893-271">Er verbindet `MySubOptions` mit dem `subsection`-Abschnitt der *appSettings.json*-Datei:</span><span class="sxs-lookup"><span data-stu-id="1a893-271">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="1a893-272">Die `GetSection`-Methode erfordert den Namespace <xref:Microsoft.Extensions.Configuration?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="1a893-272">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="1a893-273">Die *appsettings.json*-Datei des Beispiels definiert ein `subsection`-Element mit Schlüsseln für `suboption1` und `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="1a893-273">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="1a893-274">Die `MySubOptions`-Klasse definiert die Eigenschaften `SubOption1` und `SubOption2` zur Aufnahme der Optionswerte (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="1a893-274">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="1a893-275">Die `OnGet`-Methode des Seitenmodells gibt eine Zeichenfolge mit den Optionswerten (*Pages/Index.cshtml.cs*) zurück:</span><span class="sxs-lookup"><span data-stu-id="1a893-275">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="1a893-276">Wenn die App ausgeführt wird, gibt die `OnGet`-Methode eine Zeichenfolge zurück, die die Werte der untergeordneten Optionsklasse anzeigt:</span><span class="sxs-lookup"><span data-stu-id="1a893-276">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="1a893-277">Einfügung von Optionen</span><span class="sxs-lookup"><span data-stu-id="1a893-277">Options injection</span></span>

<span data-ttu-id="1a893-278">Die Einfügung von Optionen ist als Beispiel 4 in der Beispiel-App dargestellt.</span><span class="sxs-lookup"><span data-stu-id="1a893-278">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="1a893-279">Fügen Sie <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> ein in:</span><span class="sxs-lookup"><span data-stu-id="1a893-279">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="1a893-280">Eine Razor-Seite oder MVC-Ansicht mithilfe der [`@inject`](xref:mvc/views/razor#inject) Razor-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="1a893-280">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="1a893-281">Ein Seiten- oder Ansichtsmodell.</span><span class="sxs-lookup"><span data-stu-id="1a893-281">A page or view model.</span></span>

<span data-ttu-id="1a893-282">Im folgenden Beispiel aus der Beispiel-App wird <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in ein Seitenmodell eingefügt (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="1a893-282">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="1a893-283">Die Beispiel-App zeigt das Einfügen von `IOptionsMonitor<MyOptions>` mit einer `@inject`-Anweisung:</span><span class="sxs-lookup"><span data-stu-id="1a893-283">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="1a893-284">Wenn die App ausgeführt wird, werden die Optionswerte in der gerenderten Seite angezeigt:</span><span class="sxs-lookup"><span data-stu-id="1a893-284">When the app is run, the options values are shown in the rendered page:</span></span>

![Optionswerte Option1: value1_from_json und Option2: -1 werden aus dem Modell geladen und in die Ansicht eingefügt.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="1a893-286">Neuladen der Konfigurationsdaten mit IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="1a893-286">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="1a893-287">Das Neuladen der Konfigurationsdaten mit <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> wird im Beispiel 5 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="1a893-287">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="1a893-288">Mithilfe von <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> werden Optionen einmal pro Anforderung berechnet. Dies geschieht, wenn auf sie zugegriffen wird und sie für die Dauer der Anforderung zwischengespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="1a893-288">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="1a893-289">Der Unterschied zwischen `IOptionsMonitor` und `IOptionsSnapshot` ist folgender:</span><span class="sxs-lookup"><span data-stu-id="1a893-289">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="1a893-290">`IOptionsMonitor` ist ein [Singleton-Dienst](xref:fundamentals/dependency-injection#singleton), der zu jeder Zeit aktuelle Optionswerte empfängt, was insbesondere bei Singleton-Abhängigkeiten nützlich ist.</span><span class="sxs-lookup"><span data-stu-id="1a893-290">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="1a893-291">`IOptionsSnapshot` ist ein [bereichsbezogener Dienst](xref:fundamentals/dependency-injection#scoped) und bietet eine Momentaufnahme der Optionen zu dem Zeitpunkt, da das `IOptionsSnapshot<T>`-Objekt konstruiert wird.</span><span class="sxs-lookup"><span data-stu-id="1a893-291">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="1a893-292">Momentaufnahmen von Optionen sind für die Verwendung mit vorübergehenden und bereichsbezogenen Abhängigkeiten bestimmt.</span><span class="sxs-lookup"><span data-stu-id="1a893-292">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="1a893-293">Im folgenden Beispiel wird veranschaulicht, wie eine neue <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> nach den *appsettings.json*-Veränderungen erstellt wird (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="1a893-293">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="1a893-294">Mehrere Anforderungen an den Server geben konstante Werte zurück, die von der *appsettings.json*-Datei bereitgestellt werden, bis die Datei geändert wird und die Konfiguration neu lädt.</span><span class="sxs-lookup"><span data-stu-id="1a893-294">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="1a893-295">Die folgende Abbildung zeigt die anfänglichen `option1`- und `option2`-Werte, die aus der *appsettings.json*-Datei geladen werden:</span><span class="sxs-lookup"><span data-stu-id="1a893-295">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="1a893-296">Ändern Sie die Werte in der *appsettings.json*-Datei auf `value1_from_json UPDATED` und `200`.</span><span class="sxs-lookup"><span data-stu-id="1a893-296">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="1a893-297">Speichern Sie die *appsettings.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="1a893-297">Save the *appsettings.json* file.</span></span> <span data-ttu-id="1a893-298">Aktualisieren Sie den Browser, um festzustellen, ob die Optionswerte aktualisiert wurden:</span><span class="sxs-lookup"><span data-stu-id="1a893-298">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="1a893-299">Unterstützung für benannte Optionen mit IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="1a893-299">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="1a893-300">Unterstützung für benannte Optionen mit <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> wird als Beispiel 6 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="1a893-300">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="1a893-301">Die Unterstützung für benannte Optionen ermöglicht es der Anwendung, zwischen den Konfigurationen benannter Optionen zu unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="1a893-301">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="1a893-302">In der Beispiel-App werden benannte Optionen mit [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) deklariert. Dadurch wird wiederum die Erweiterungsmethode [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="1a893-302">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="1a893-303">Benannte Optionen Unterscheidung nach Groß-/Kleinschreibung.</span><span class="sxs-lookup"><span data-stu-id="1a893-303">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="1a893-304">Die Beispielanwendung greift mit <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> auf die benannten Optionen zu (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="1a893-304">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="1a893-305">Bei der Ausführung der Beispielanwendung werden die benannten Optionen zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="1a893-305">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="1a893-306">`named_options_1`-Werte werden von der Konfiguration bereitgestellt. Sie werden aus der *appsettings.json*-Datei geladen.</span><span class="sxs-lookup"><span data-stu-id="1a893-306">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="1a893-307">`named_options_2`-Werte werden bereitgestellt vom:</span><span class="sxs-lookup"><span data-stu-id="1a893-307">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="1a893-308">`named_options_2`-Delegat in `ConfigureServices` für `Option1`.</span><span class="sxs-lookup"><span data-stu-id="1a893-308">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="1a893-309">Der Standardwert für `Option2` wird von der `MyOptions`-Klasse bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="1a893-309">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="1a893-310">Konfigurieren aller Optionen mit der ConfigureAll-Methode</span><span class="sxs-lookup"><span data-stu-id="1a893-310">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="1a893-311">Konfigurieren Sie alle Optionsinstanzen mit der <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>-Methode.</span><span class="sxs-lookup"><span data-stu-id="1a893-311">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="1a893-312">Der folgende Code konfiguriert `Option1` für alle Konfigurationsinstanzen mit einem gemeinsamen Wert.</span><span class="sxs-lookup"><span data-stu-id="1a893-312">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="1a893-313">Fügen Sie der `Startup.ConfigureServices`-Methode manuell den folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="1a893-313">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="1a893-314">Das Ausführen der Beispielanwendung nach dem Hinzufügen des Codes führt zu folgendem Ergebnis:</span><span class="sxs-lookup"><span data-stu-id="1a893-314">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="1a893-315">Alle Optionen sind benannte Instanzen.</span><span class="sxs-lookup"><span data-stu-id="1a893-315">All options are named instances.</span></span> <span data-ttu-id="1a893-316">Vorhandene <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Instanzen werden behandelt, als ob sie auf die `Options.DefaultName`-Instanz abzielen. Diese ist `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="1a893-316">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="1a893-317"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementiert auch <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="1a893-317"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="1a893-318">Die standardmäßige Implementierung von <xref:Microsoft.Extensions.Options.IOptionsFactory%601> verfügt über eine Logik, um jede einzelne entsprechend zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="1a893-318">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="1a893-319">Die benannte Option `null` wird verwendet, um auf alle benannten Instanzen anstelle einer bestimmten benannten Instanz abzuzielen (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> und <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> verwenden diese Konvention).</span><span class="sxs-lookup"><span data-stu-id="1a893-319">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="1a893-320">OptionsBuilder-API</span><span class="sxs-lookup"><span data-stu-id="1a893-320">OptionsBuilder API</span></span>

<span data-ttu-id="1a893-321"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> dient zum Konfigurieren von `TOptions`-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="1a893-321"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="1a893-322">`OptionsBuilder` optimiert die Erstellung von benannten Optionen, da es sich nur um einen einzelnen Parameter für den ersten `AddOptions<TOptions>(string optionsName)`-Aufruf handelt statt um alle nachfolgenden Aufrufe.</span><span class="sxs-lookup"><span data-stu-id="1a893-322">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="1a893-323">Die Optionsvalidierung und die `ConfigureOptions`-Überladungen, die Dienstabhängigkeiten akzeptieren, sind nur über `OptionsBuilder` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="1a893-323">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="1a893-324">Verwenden von DI-Diensten zum Konfigurieren von Optionen</span><span class="sxs-lookup"><span data-stu-id="1a893-324">Use DI services to configure options</span></span>

<span data-ttu-id="1a893-325">Beim Konfigurieren von Optionen haben Sie zwei Möglichkeiten, auf andere Dienste aus Dependency Injections zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="1a893-325">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="1a893-326">Übergeben Sie einen Konfigurationsdelegaten an [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) in [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="1a893-326">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="1a893-327">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) stellt Überladungen von [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) zur Verfügung, die es Ihnen ermöglichen, bis zu fünf Dienste zu verwenden, um Optionen zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="1a893-327">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="1a893-328">Erstellen Ihres eigenen Typs, der <xref:Microsoft.Extensions.Options.IConfigureOptions%601> oder <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementiert, und Registrieren des Typs als Dienst.</span><span class="sxs-lookup"><span data-stu-id="1a893-328">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="1a893-329">Es empfiehlt sich das Übergeben eines Konfigurationsdelegaten an [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), da das Erstellen eines Diensts etwas komplexer ist.</span><span class="sxs-lookup"><span data-stu-id="1a893-329">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="1a893-330">Selbst einen eigenen Typ zu erstellen ist gleichbedeutend mit dem, was das Framework für Sie übernimmt, wenn Sie [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) verwenden.</span><span class="sxs-lookup"><span data-stu-id="1a893-330">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="1a893-331">Wenn [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) aufgerufen wird, wird eine vorübergehende, generische <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> registriert, die über einen Konstruktor verfügt, der die angegeben generischen Diensttypen akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="1a893-331">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="1a893-332">Überprüfung von Optionen</span><span class="sxs-lookup"><span data-stu-id="1a893-332">Options validation</span></span>

<span data-ttu-id="1a893-333">Sie können Optionen überprüfen, wenn diese konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="1a893-333">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="1a893-334">Rufen Sie `Validate` mit einer Überprüfungsmethode auf, die `true` zurückgibt, wenn die Optionen gültig sind, und `false` zurückgibt, wenn sie es nicht sind:</span><span class="sxs-lookup"><span data-stu-id="1a893-334">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="1a893-335">Im vorherigen Beispiel wurde die benannte Optionsinstanz auf `optionalOptionsName` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="1a893-335">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="1a893-336">Die Standardoptionsinstanz ist `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="1a893-336">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="1a893-337">Überprüfungen werden ausgeführt, wenn die Optionsinstanz erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="1a893-337">Validation runs when the options instance is created.</span></span> <span data-ttu-id="1a893-338">Eine Optionsinstanz besteht die Überprüfung beim ersten Zugriff garantiert.</span><span class="sxs-lookup"><span data-stu-id="1a893-338">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="1a893-339">Die Überprüfung von Optionen schützt nicht vor Änderungen an Optionen nach der Erstellung der Optionsinstanz.</span><span class="sxs-lookup"><span data-stu-id="1a893-339">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="1a893-340">Beispielsweise werden `IOptionsSnapshot`-Optionen einmal pro Anforderung erstellt und überprüft, wenn zum ersten Mal auf die Optionen zugegriffen wird.</span><span class="sxs-lookup"><span data-stu-id="1a893-340">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="1a893-341">Die `IOptionsSnapshot`-Optionen werden bei nachfolgenden Zugriffsversuchen *für dieselbe Anforderung* nicht erneut überprüft.</span><span class="sxs-lookup"><span data-stu-id="1a893-341">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="1a893-342">Die `Validate`-Methode akzeptiert ein `Func<TOptions, bool>`-Element.</span><span class="sxs-lookup"><span data-stu-id="1a893-342">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="1a893-343">Um die Überprüfung vollständig anzupassen, implementieren Sie `IValidateOptions<TOptions>`. Dies ermöglicht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="1a893-343">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="1a893-344">Eine Überprüfung von mehreren Optionstypen: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="1a893-344">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="1a893-345">Eine Überprüfung, die von einem anderen Optionstyp abhängig ist: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="1a893-345">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="1a893-346">`IValidateOptions` validiert Folgendes:</span><span class="sxs-lookup"><span data-stu-id="1a893-346">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="1a893-347">Eine bestimmte benannte Optionsinstanz.</span><span class="sxs-lookup"><span data-stu-id="1a893-347">A specific named options instance.</span></span>
* <span data-ttu-id="1a893-348">Alle Optionen, wenn `name` den Wert `null` aufweist.</span><span class="sxs-lookup"><span data-stu-id="1a893-348">All options when `name` is `null`.</span></span>

<span data-ttu-id="1a893-349">Geben Sie ein `ValidateOptionsResult` aus Ihrer Implementierung der Schnittstelle zurück:</span><span class="sxs-lookup"><span data-stu-id="1a893-349">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="1a893-350">Die Überprüfung auf Basis von Datenanmerkungen ist über das Paket [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) durch Aufrufen der <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*>-Methode in `OptionsBuilder<TOptions>` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="1a893-350">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="1a893-351">`Microsoft.Extensions.Options.DataAnnotations` ist im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="1a893-351">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

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

<span data-ttu-id="1a893-352">Die vorzeitige Überprüfung (Fail-fast beim Start) wird für ein späteres Release in Erwägung.</span><span class="sxs-lookup"><span data-stu-id="1a893-352">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="1a893-353">Optionen für die Postkonfiguration</span><span class="sxs-lookup"><span data-stu-id="1a893-353">Options post-configuration</span></span>

<span data-ttu-id="1a893-354">Legen Sie die Postkonfiguration mit <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> fest.</span><span class="sxs-lookup"><span data-stu-id="1a893-354">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="1a893-355">Die Postkonfiguration erfolgt, nachdem die gesamte <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Konfiguration abgeschlossen ist:</span><span class="sxs-lookup"><span data-stu-id="1a893-355">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="1a893-356"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> ist nach dem Konfigurieren der benannten Optionen verfügbar:</span><span class="sxs-lookup"><span data-stu-id="1a893-356"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="1a893-357">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> zur Postkonfiguration aller Konfigurationsinstanzen:</span><span class="sxs-lookup"><span data-stu-id="1a893-357">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="1a893-358">Zugreifen auf Optionen während des Starts</span><span class="sxs-lookup"><span data-stu-id="1a893-358">Accessing options during startup</span></span>

<span data-ttu-id="1a893-359"><xref:Microsoft.Extensions.Options.IOptions%601> und <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> können in `Startup.Configure` verwendet werden, da Dienste erstellt werden, bevor die `Configure`-Methode ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="1a893-359"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="1a893-360">Verwenden Sie <xref:Microsoft.Extensions.Options.IOptions%601> oder <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> nicht in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="1a893-360">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="1a893-361">Es kann einen inkonsistenten Optionszustand geben. Dies liegt an der Reihenfolge der Dienstregistrierungen.</span><span class="sxs-lookup"><span data-stu-id="1a893-361">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="1a893-362">Das Optionsmuster verwendet Klassen, um Gruppen von zusammengehörigen Einstellungen darzustellen.</span><span class="sxs-lookup"><span data-stu-id="1a893-362">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="1a893-363">Wenn [Konfigurationseinstellungen](xref:fundamentals/configuration/index) nach Szenario in separate Klassen isoliert werden, entspricht die Anwendung zwei wichtigen Prinzipien der Softwareentwicklung:</span><span class="sxs-lookup"><span data-stu-id="1a893-363">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="1a893-364">Das [Schnittstellentrennungsprinzip oder die Kapselung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Szenarios (Klassen), die von Konfigurationseinstellungen abhängen, sind nur von den Konfigurationseinstellungen abhängig, die sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="1a893-364">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="1a893-365">[Trennung von Zuständigkeiten:](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) Einstellungen für verschiedene App-Komponenten sind nicht voneinander abhängig und nicht aneinander gekoppelt.</span><span class="sxs-lookup"><span data-stu-id="1a893-365">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="1a893-366">Optionen bieten auch einen Mechanismus, um Konfigurationsdaten zu validieren.</span><span class="sxs-lookup"><span data-stu-id="1a893-366">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="1a893-367">Weitere Informationen finden Sie im Abschnitt [Optionsvalidierung](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="1a893-367">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="1a893-368">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1a893-368">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1a893-369">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="1a893-369">Prerequisites</span></span>

<span data-ttu-id="1a893-370">Verweisen Sie auf das [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app), oder fügen Sie einen Paketverweis auf das Paket [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) hinzu.</span><span class="sxs-lookup"><span data-stu-id="1a893-370">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="1a893-371">Optionenschnittstellen</span><span class="sxs-lookup"><span data-stu-id="1a893-371">Options interfaces</span></span>

<span data-ttu-id="1a893-372"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> wird verwendet, um Optionen abzurufen und Benachrichtigungen über Optionen für `TOptions`-Instanzen zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="1a893-372"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="1a893-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> unterstützt die folgenden Szenarios:</span><span class="sxs-lookup"><span data-stu-id="1a893-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="1a893-374">Änderungsbenachrichtigungen</span><span class="sxs-lookup"><span data-stu-id="1a893-374">Change notifications</span></span>
* [<span data-ttu-id="1a893-375">Benannte Optionen</span><span class="sxs-lookup"><span data-stu-id="1a893-375">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="1a893-376">Erneut ladbare Konfiguration</span><span class="sxs-lookup"><span data-stu-id="1a893-376">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="1a893-377">Selektive Optionsvalidierung (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="1a893-377">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="1a893-378">Mit [Postkonfigurationsszenarien](#options-post-configuration) können Sie Optionen festlegen oder ändern, nachdem alle <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Konfigurationen durchgeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="1a893-378">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="1a893-379"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> ist für das Erstellen neuer Optionsinstanzen zuständig.</span><span class="sxs-lookup"><span data-stu-id="1a893-379"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="1a893-380">Es verfügt über eine einzelne <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>-Methode.</span><span class="sxs-lookup"><span data-stu-id="1a893-380">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="1a893-381">Die Standardimplementierung akzeptiert alle registrierten <xref:Microsoft.Extensions.Options.IConfigureOptions%601> und <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> und führt alle Konfigurationen zuerst und die Postkonfigurationen danach aus.</span><span class="sxs-lookup"><span data-stu-id="1a893-381">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="1a893-382">Es wird zwischen <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> und <xref:Microsoft.Extensions.Options.IConfigureOptions%601> unterschieden, und es werden nur die entsprechenden Schnittstellen aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="1a893-382">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="1a893-383"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> wird von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> zum Zwischenspeichern der `TOptions`-Instanzen verwendet.</span><span class="sxs-lookup"><span data-stu-id="1a893-383"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="1a893-384"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> erklärt Optionsinstanzen im Monitor für ungültig, sodass der Wert neu berechnet wird (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="1a893-384">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="1a893-385">Werte können manuell mit <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> eingeführt werden.</span><span class="sxs-lookup"><span data-stu-id="1a893-385">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="1a893-386">Die <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>-Methode wird verwendet, wenn alle benannten Instanzen bei Bedarf neu erstellt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="1a893-386">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="1a893-387"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> ist nützlich in Szenarien, in denen Optionen bei jeder Anforderung neu berechnet werden sollten.</span><span class="sxs-lookup"><span data-stu-id="1a893-387"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="1a893-388">Weitere Informationen finden Sie im Abschnitt [Neuladen der Konfigurationsdaten mit IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="1a893-388">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="1a893-389"><xref:Microsoft.Extensions.Options.IOptions%601> kann zum Unterstützen von Optionen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="1a893-389"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="1a893-390">Allerdings unterstützt <xref:Microsoft.Extensions.Options.IOptions%601> nicht die oben beschriebenen Szenarios von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="1a893-390">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="1a893-391">Sie können <xref:Microsoft.Extensions.Options.IOptions%601> weiterhin in bestehenden Frameworks und Bibliotheken verwenden, die bereits die <xref:Microsoft.Extensions.Options.IOptions%601>-Schnittstelle verwenden und nicht die von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> bereitgestellten Szenarios benötigen.</span><span class="sxs-lookup"><span data-stu-id="1a893-391">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="1a893-392">Allgemeine Optionskonfiguration</span><span class="sxs-lookup"><span data-stu-id="1a893-392">General options configuration</span></span>

<span data-ttu-id="1a893-393">Die allgemeine Optionskonfiguration wird als Beispiel 1 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="1a893-393">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="1a893-394">Eine Optionsklasse muss nicht abstrakt sein und über einen öffentlichen parameterlosen Konstruktor verfügen.</span><span class="sxs-lookup"><span data-stu-id="1a893-394">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="1a893-395">Die folgende Klasse `MyOptions` verfügt über die zwei Eigenschaften: `Option1` und `Option2`.</span><span class="sxs-lookup"><span data-stu-id="1a893-395">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="1a893-396">Das Festlegen von Standardwerten ist optional, aber der Klassenkonstruktor im folgenden Beispiel legt den Standardwert von `Option1` fest.</span><span class="sxs-lookup"><span data-stu-id="1a893-396">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="1a893-397">`Option2` hat den Standardwert festgelegt, indem die Eigenschaft direkt initialisiert wurde (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="1a893-397">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="1a893-398">Die `MyOptions`-Klasse wird zum Dienstcontainer mit <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> hinzugefügt und an die Konfiguration gebunden:</span><span class="sxs-lookup"><span data-stu-id="1a893-398">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="1a893-399">Das folgende Seitenmodel verwendet [konstruktorbasierte Dependency Injection](xref:mvc/controllers/dependency-injection) mit <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>, um auf die Einstellungen zugreifen zu können (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="1a893-399">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="1a893-400">Die *appsettings.json*-Datei des Beispiels gibt Werte für `option1` und `option2` an:</span><span class="sxs-lookup"><span data-stu-id="1a893-400">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="1a893-401">Wenn die Anwendung ausgeführt wird, gibt die `OnGet`-Methode des Seitenmodells eine Zeichenfolge zurück, die die Werte der Optionsklasse anzeigt:</span><span class="sxs-lookup"><span data-stu-id="1a893-401">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="1a893-402">Wenn Sie eine benutzerdefinierte <xref:System.Configuration.ConfigurationBuilder>-Klasse verwenden, um Konfigurationsoptionen aus einer Einstellungsdatei zu laden, bestätigen Sie, dass der Basispfad ordnungsgemäß festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="1a893-402">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="1a893-403">Sie müssen den Basispfad nicht explizit festlegen, wenn Sie Konfigurationsoptionen über <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> aus der Einstellungsdatei laden.</span><span class="sxs-lookup"><span data-stu-id="1a893-403">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="1a893-404">Konfigurieren von einfachen Optionen mit einem Delegaten</span><span class="sxs-lookup"><span data-stu-id="1a893-404">Configure simple options with a delegate</span></span>

<span data-ttu-id="1a893-405">Das Konfigurieren von einfachen Optionen mit einem Delegaten wird als Beispiel 2 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="1a893-405">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="1a893-406">Verwenden Sie einen Delegaten zum Festlegen von Optionswerten.</span><span class="sxs-lookup"><span data-stu-id="1a893-406">Use a delegate to set options values.</span></span> <span data-ttu-id="1a893-407">Die Beispielanwendung verwendet die `MyOptionsWithDelegateConfig`-Klasse (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="1a893-407">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="1a893-408">Im folgenden Code wird ein zweiter <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Dienst zum Dienstcontainer hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="1a893-408">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="1a893-409">Er verwendet einen Delegaten zum Konfigurieren der Bindung mit `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="1a893-409">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="1a893-410">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="1a893-410">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="1a893-411">Sie können mehrere Konfigurationsanbieter hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="1a893-411">You can add multiple configuration providers.</span></span> <span data-ttu-id="1a893-412">Konfigurationsanbieter sind über NuGet-Pakete verfügbar und werden in der Reihenfolge ihrer Registrierung angewendet.</span><span class="sxs-lookup"><span data-stu-id="1a893-412">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="1a893-413">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="1a893-413">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="1a893-414">Jeder Aufruf von <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> fügt einen <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Dienst zum Dienstcontainer hinzu.</span><span class="sxs-lookup"><span data-stu-id="1a893-414">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="1a893-415">Im vorherigen Beispiel wurden die Werte von `Option1` und `Option2` beide in *appsettings.json* angegeben, aber die Werte von `Option1` und `Option2` werden vom konfigurierten Delegaten überschrieben.</span><span class="sxs-lookup"><span data-stu-id="1a893-415">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="1a893-416">Wenn mehr als ein Konfigurationsdienst aktiviert ist, *gewinnt* die letzte angegebene Konfigurationsquelle und legt den Konfigurationswert fest.</span><span class="sxs-lookup"><span data-stu-id="1a893-416">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="1a893-417">Wenn die Anwendung ausgeführt wird, gibt die `OnGet`-Methode des Seitenmodells eine Zeichenfolge zurück, die die Werte der Optionsklasse anzeigt:</span><span class="sxs-lookup"><span data-stu-id="1a893-417">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="1a893-418">Konfigurieren von Unteroptionen</span><span class="sxs-lookup"><span data-stu-id="1a893-418">Suboptions configuration</span></span>

<span data-ttu-id="1a893-419">Das Konfigurieren von Unteroptionen wird als Beispiel 3 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="1a893-419">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="1a893-420">Anwendungen sollten Optionsklassen erstellen, die für bestimmte Szenariogruppen (Klassen) in der Anwendung gelten.</span><span class="sxs-lookup"><span data-stu-id="1a893-420">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="1a893-421">Die Komponenten der Anwendung, die Konfigurationswerte erfordern, sollten nur über Zugriff auf die Konfigurationswerte verfügen, die sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="1a893-421">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="1a893-422">Beim Binden von Optionen zur Konfiguration ist jede Eigenschaft im Optionstyp an einen Konfigurationsschlüssel des `property[:sub-property:]`-Formulars gebunden.</span><span class="sxs-lookup"><span data-stu-id="1a893-422">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="1a893-423">Die `MyOptions.Option1`-Eigenschaft ist beispielsweise an den Schlüssel `Option1` gebunden, der von der `option1`-Eigenschaft in *appsettings.json* gelesen wird.</span><span class="sxs-lookup"><span data-stu-id="1a893-423">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="1a893-424">Im folgenden Code wird ein dritter <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Dienst zum Dienstcontainer hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="1a893-424">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="1a893-425">Er verbindet `MySubOptions` mit dem `subsection`-Abschnitt der *appSettings.json*-Datei:</span><span class="sxs-lookup"><span data-stu-id="1a893-425">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="1a893-426">Die `GetSection`-Methode erfordert den Namespace <xref:Microsoft.Extensions.Configuration?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="1a893-426">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="1a893-427">Die *appsettings.json*-Datei des Beispiels definiert ein `subsection`-Element mit Schlüsseln für `suboption1` und `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="1a893-427">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="1a893-428">Die `MySubOptions`-Klasse definiert die Eigenschaften `SubOption1` und `SubOption2` zur Aufnahme der Optionswerte (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="1a893-428">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="1a893-429">Die `OnGet`-Methode des Seitenmodells gibt eine Zeichenfolge mit den Optionswerten (*Pages/Index.cshtml.cs*) zurück:</span><span class="sxs-lookup"><span data-stu-id="1a893-429">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="1a893-430">Wenn die App ausgeführt wird, gibt die `OnGet`-Methode eine Zeichenfolge zurück, die die Werte der untergeordneten Optionsklasse anzeigt:</span><span class="sxs-lookup"><span data-stu-id="1a893-430">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="1a893-431">Optionen, die durch ein Ansichtsmodell oder mit direkter View Injection bereitgestellt werden</span><span class="sxs-lookup"><span data-stu-id="1a893-431">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="1a893-432">Optionen, die durch ein Ansichtsmodell oder mit direkter View Injection bereitgestellt werden, werden im Beispiel 4 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="1a893-432">Options provided by a view model or with direct view injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="1a893-433">Optionen können in einem Ansichtsmodell oder durch direkte Injection von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in eine Ansicht (*Pages/Index.cshtml.cs*) angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="1a893-433">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="1a893-434">Die Beispiel-App zeigt das Einfügen von `IOptionsMonitor<MyOptions>` mit einer `@inject`-Anweisung:</span><span class="sxs-lookup"><span data-stu-id="1a893-434">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="1a893-435">Wenn die App ausgeführt wird, werden die Optionswerte in der gerenderten Seite angezeigt:</span><span class="sxs-lookup"><span data-stu-id="1a893-435">When the app is run, the options values are shown in the rendered page:</span></span>

![Optionswerte Option1: value1_from_json und Option2: -1 werden aus dem Modell geladen und in die Ansicht eingefügt.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="1a893-437">Neuladen der Konfigurationsdaten mit IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="1a893-437">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="1a893-438">Das Neuladen der Konfigurationsdaten mit <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> wird im Beispiel 5 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="1a893-438">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="1a893-439"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> unterstützt das Neuladen von Optionen mit minimalem Verarbeitungsaufwand.</span><span class="sxs-lookup"><span data-stu-id="1a893-439"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="1a893-440">Optionen werden einmal pro Anforderung berechnet. Dies geschieht, wenn auf sie zugegriffen wird und sie für die Dauer der Anforderung zwischengespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="1a893-440">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="1a893-441">Im folgenden Beispiel wird veranschaulicht, wie eine neue <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> nach den *appsettings.json*-Veränderungen erstellt wird (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="1a893-441">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="1a893-442">Mehrere Anforderungen an den Server geben konstante Werte zurück, die von der *appsettings.json*-Datei bereitgestellt werden, bis die Datei geändert wird und die Konfiguration neu lädt.</span><span class="sxs-lookup"><span data-stu-id="1a893-442">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="1a893-443">Die folgende Abbildung zeigt die anfänglichen `option1`- und `option2`-Werte, die aus der *appsettings.json*-Datei geladen werden:</span><span class="sxs-lookup"><span data-stu-id="1a893-443">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="1a893-444">Ändern Sie die Werte in der *appsettings.json*-Datei auf `value1_from_json UPDATED` und `200`.</span><span class="sxs-lookup"><span data-stu-id="1a893-444">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="1a893-445">Speichern Sie die *appsettings.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="1a893-445">Save the *appsettings.json* file.</span></span> <span data-ttu-id="1a893-446">Aktualisieren Sie den Browser, um festzustellen, ob die Optionswerte aktualisiert wurden:</span><span class="sxs-lookup"><span data-stu-id="1a893-446">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="1a893-447">Unterstützung für benannte Optionen mit IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="1a893-447">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="1a893-448">Unterstützung für benannte Optionen mit <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> wird als Beispiel 6 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="1a893-448">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="1a893-449">Die Unterstützung für benannte Optionen ermöglicht es der Anwendung, zwischen den Konfigurationen benannter Optionen zu unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="1a893-449">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="1a893-450">In der Beispiel-App werden benannte Optionen mit [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) deklariert. Dadurch wird wiederum die Erweiterungsmethode [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="1a893-450">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="1a893-451">Benannte Optionen Unterscheidung nach Groß-/Kleinschreibung.</span><span class="sxs-lookup"><span data-stu-id="1a893-451">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="1a893-452">Die Beispielanwendung greift mit <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> auf die benannten Optionen zu (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="1a893-452">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="1a893-453">Bei der Ausführung der Beispielanwendung werden die benannten Optionen zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="1a893-453">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="1a893-454">`named_options_1`-Werte werden von der Konfiguration bereitgestellt. Sie werden aus der *appsettings.json*-Datei geladen.</span><span class="sxs-lookup"><span data-stu-id="1a893-454">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="1a893-455">`named_options_2`-Werte werden bereitgestellt vom:</span><span class="sxs-lookup"><span data-stu-id="1a893-455">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="1a893-456">`named_options_2`-Delegat in `ConfigureServices` für `Option1`.</span><span class="sxs-lookup"><span data-stu-id="1a893-456">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="1a893-457">Der Standardwert für `Option2` wird von der `MyOptions`-Klasse bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="1a893-457">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="1a893-458">Konfigurieren aller Optionen mit der ConfigureAll-Methode</span><span class="sxs-lookup"><span data-stu-id="1a893-458">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="1a893-459">Konfigurieren Sie alle Optionsinstanzen mit der <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>-Methode.</span><span class="sxs-lookup"><span data-stu-id="1a893-459">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="1a893-460">Der folgende Code konfiguriert `Option1` für alle Konfigurationsinstanzen mit einem gemeinsamen Wert.</span><span class="sxs-lookup"><span data-stu-id="1a893-460">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="1a893-461">Fügen Sie der `Startup.ConfigureServices`-Methode manuell den folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="1a893-461">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="1a893-462">Das Ausführen der Beispielanwendung nach dem Hinzufügen des Codes führt zu folgendem Ergebnis:</span><span class="sxs-lookup"><span data-stu-id="1a893-462">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="1a893-463">Alle Optionen sind benannte Instanzen.</span><span class="sxs-lookup"><span data-stu-id="1a893-463">All options are named instances.</span></span> <span data-ttu-id="1a893-464">Vorhandene <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Instanzen werden behandelt, als ob sie auf die `Options.DefaultName`-Instanz abzielen. Diese ist `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="1a893-464">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="1a893-465"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementiert auch <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="1a893-465"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="1a893-466">Die standardmäßige Implementierung von <xref:Microsoft.Extensions.Options.IOptionsFactory%601> verfügt über eine Logik, um jede einzelne entsprechend zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="1a893-466">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="1a893-467">Die benannte Option `null` wird verwendet, um auf alle benannten Instanzen anstelle einer bestimmten benannten Instanz abzuzielen (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> und <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> verwenden diese Konvention).</span><span class="sxs-lookup"><span data-stu-id="1a893-467">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="1a893-468">OptionsBuilder-API</span><span class="sxs-lookup"><span data-stu-id="1a893-468">OptionsBuilder API</span></span>

<span data-ttu-id="1a893-469"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> dient zum Konfigurieren von `TOptions`-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="1a893-469"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="1a893-470">`OptionsBuilder` optimiert die Erstellung von benannten Optionen, da es sich nur um einen einzelnen Parameter für den ersten `AddOptions<TOptions>(string optionsName)`-Aufruf handelt statt um alle nachfolgenden Aufrufe.</span><span class="sxs-lookup"><span data-stu-id="1a893-470">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="1a893-471">Die Optionsvalidierung und die `ConfigureOptions`-Überladungen, die Dienstabhängigkeiten akzeptieren, sind nur über `OptionsBuilder` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="1a893-471">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="1a893-472">Verwenden von DI-Diensten zum Konfigurieren von Optionen</span><span class="sxs-lookup"><span data-stu-id="1a893-472">Use DI services to configure options</span></span>

<span data-ttu-id="1a893-473">Beim Konfigurieren von Optionen haben Sie zwei Möglichkeiten, auf andere Dienste aus Dependency Injections zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="1a893-473">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="1a893-474">Übergeben Sie einen Konfigurationsdelegaten an [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) in [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="1a893-474">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="1a893-475">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) stellt Überladungen von [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) zur Verfügung, die es Ihnen ermöglichen, bis zu fünf Dienste zu verwenden, um Optionen zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="1a893-475">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="1a893-476">Erstellen Ihres eigenen Typs, der <xref:Microsoft.Extensions.Options.IConfigureOptions%601> oder <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementiert, und Registrieren des Typs als Dienst.</span><span class="sxs-lookup"><span data-stu-id="1a893-476">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="1a893-477">Es empfiehlt sich das Übergeben eines Konfigurationsdelegaten an [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), da das Erstellen eines Diensts etwas komplexer ist.</span><span class="sxs-lookup"><span data-stu-id="1a893-477">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="1a893-478">Selbst einen eigenen Typ zu erstellen ist gleichbedeutend mit dem, was das Framework für Sie übernimmt, wenn Sie [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) verwenden.</span><span class="sxs-lookup"><span data-stu-id="1a893-478">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="1a893-479">Wenn [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) aufgerufen wird, wird eine vorübergehende, generische <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> registriert, die über einen Konstruktor verfügt, der die angegeben generischen Diensttypen akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="1a893-479">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="1a893-480">Optionen für die Postkonfiguration</span><span class="sxs-lookup"><span data-stu-id="1a893-480">Options post-configuration</span></span>

<span data-ttu-id="1a893-481">Legen Sie die Postkonfiguration mit <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> fest.</span><span class="sxs-lookup"><span data-stu-id="1a893-481">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="1a893-482">Die Postkonfiguration erfolgt, nachdem die gesamte <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Konfiguration abgeschlossen ist:</span><span class="sxs-lookup"><span data-stu-id="1a893-482">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="1a893-483"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> ist nach dem Konfigurieren der benannten Optionen verfügbar:</span><span class="sxs-lookup"><span data-stu-id="1a893-483"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="1a893-484">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> zur Postkonfiguration aller Konfigurationsinstanzen:</span><span class="sxs-lookup"><span data-stu-id="1a893-484">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="1a893-485">Zugreifen auf Optionen während des Starts</span><span class="sxs-lookup"><span data-stu-id="1a893-485">Accessing options during startup</span></span>

<span data-ttu-id="1a893-486"><xref:Microsoft.Extensions.Options.IOptions%601> und <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> können in `Startup.Configure` verwendet werden, da Dienste erstellt werden, bevor die `Configure`-Methode ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="1a893-486"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="1a893-487">Verwenden Sie <xref:Microsoft.Extensions.Options.IOptions%601> oder <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> nicht in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="1a893-487">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="1a893-488">Es kann einen inkonsistenten Optionszustand geben. Dies liegt an der Reihenfolge der Dienstregistrierungen.</span><span class="sxs-lookup"><span data-stu-id="1a893-488">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="1a893-489">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="1a893-489">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
