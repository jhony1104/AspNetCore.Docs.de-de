---
title: Konfiguration in ASP.NET Core
author: guardrex
description: 'In diesem Artikel erfahren Sie, wie Sie mit der Konfigurations-API eine ASP.NET Core-App konfigurieren.'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/11/2019
uid: fundamentals/configuration/index
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="24d1f-103">Konfiguration in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="24d1f-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="24d1f-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="24d1f-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="24d1f-105">Die App-Konfiguration in ASP.NET Core basiert auf Schlüssel-Wert-Paaren, die von *Konfigurationsanbietern* erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="24d1f-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="24d1f-106">Konfigurationsanbieter lesen Konfigurationsdaten in Schlüssel-Wert-Paare aus verschiedenen Konfigurationsquellen:</span><span class="sxs-lookup"><span data-stu-id="24d1f-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="24d1f-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="24d1f-107">Azure Key Vault</span></span>
* <span data-ttu-id="24d1f-108">Befehlszeilenargumente</span><span class="sxs-lookup"><span data-stu-id="24d1f-108">Command-line arguments</span></span>
* <span data-ttu-id="24d1f-109">Benutzerdefinierte Anbieter (installiert oder erstellt)</span><span class="sxs-lookup"><span data-stu-id="24d1f-109">Custom providers (installed or created)</span></span>
* <span data-ttu-id="24d1f-110">Verzeichnisdateien</span><span class="sxs-lookup"><span data-stu-id="24d1f-110">Directory files</span></span>
* <span data-ttu-id="24d1f-111">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="24d1f-111">Environment variables</span></span>
* <span data-ttu-id="24d1f-112">Speicherinterne .NET Objekte</span><span class="sxs-lookup"><span data-stu-id="24d1f-112">In-memory .NET objects</span></span>
* <span data-ttu-id="24d1f-113">Einstellungsdateien</span><span class="sxs-lookup"><span data-stu-id="24d1f-113">Settings files</span></span>

<span data-ttu-id="24d1f-114">Das *Optionsmuster* ist eine Erweiterung der in diesem Thema beschriebenen Konfigurationskonzepte.</span><span class="sxs-lookup"><span data-stu-id="24d1f-114">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="24d1f-115">Optionsmuster verwenden Klassen, um Gruppen von zusammengehörigen Einstellungen darzustellen.</span><span class="sxs-lookup"><span data-stu-id="24d1f-115">Options uses classes to represent groups of related settings.</span></span> <span data-ttu-id="24d1f-116">Weitere Informationen zum Verwenden von Optionsmustern finden Sie unter <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="24d1f-116">For more information on using the options pattern, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="24d1f-117">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="24d1f-117">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="24d1f-118">Diese drei Pakete sind im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="24d1f-118">These three packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

## <a name="host-vs-app-configuration"></a><span data-ttu-id="24d1f-119">Hostkonfiguration vs. App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="24d1f-119">Host vs. app configuration</span></span>

<span data-ttu-id="24d1f-120">Bevor die App konfiguriert und gestartet wird, wird ein *Host* konfiguriert und gestartet.</span><span class="sxs-lookup"><span data-stu-id="24d1f-120">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="24d1f-121">Der Host ist verantwortlich für das Starten der App und das Verwalten der Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="24d1f-121">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="24d1f-122">Die App und der Host werden mit den in diesem Thema beschriebenen Konfigurationsanbietern konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="24d1f-122">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="24d1f-123">Schlüssel-Wert-Paare der Hostkonfiguration werden Teil der globalen App-Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="24d1f-123">Host configuration key-value pairs become part of the app's global configuration.</span></span> <span data-ttu-id="24d1f-124">Weitere Informationen dazu, wie Konfigurationsanbieter beim Erstellen des Hosts verwendet werden, und wie sich Konfigurationsquellen auf die Hostkonfiguration auswirken, finden Sie unter [Hosting](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="24d1f-124">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see [The host](xref:fundamentals/index#host).</span></span>

## <a name="default-configuration"></a><span data-ttu-id="24d1f-125">Standardkonfiguration</span><span class="sxs-lookup"><span data-stu-id="24d1f-125">Default configuration</span></span>

<span data-ttu-id="24d1f-126">Web-Apps, die auf den [dotnet new](/dotnet/core/tools/dotnet-new)-Vorlagen von ASP.NET Core basieren, rufen beim Erstellen eines Hosts <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> auf.</span><span class="sxs-lookup"><span data-stu-id="24d1f-126">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="24d1f-127"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> legt die Standardkonfiguration für die App in der folgenden Reihenfolge fest:</span><span class="sxs-lookup"><span data-stu-id="24d1f-127"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

* <span data-ttu-id="24d1f-128">Die Konfiguration des Hosts wird durch Folgendes festgelegt:</span><span class="sxs-lookup"><span data-stu-id="24d1f-128">Host configuration is provided from:</span></span>
  * <span data-ttu-id="24d1f-129">Umgebungsvariablen mit dem Präfix `ASPNETCORE_` (zum Beispiel `ASPNETCORE_ENVIRONMENT`) mithilfe des [Umgebungsvariablen-Konfigurationsanbieters](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="24d1f-129">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="24d1f-130">Das Präfix (`ASPNETCORE_`) wird beim Laden der Schlüssel-Wert-Paare der Konfiguration entfernt.</span><span class="sxs-lookup"><span data-stu-id="24d1f-130">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="24d1f-131">Befehlszeilenargumente mithilfe des [Befehlszeilen-Konfigurationsanbieters](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="24d1f-131">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="24d1f-132">Die App-Konfiguration wird durch Folgendes festgelegt:</span><span class="sxs-lookup"><span data-stu-id="24d1f-132">App configuration is provided from:</span></span>
  * <span data-ttu-id="24d1f-133">*appsettings.json* mithilfe des [Dateikonfigurationsanbieters](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="24d1f-133">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="24d1f-134">*appsettings.{Umgebung}.json* mithilfe des [Dateikonfigurationsanbieters](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="24d1f-134">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="24d1f-135">[Geheimnis-Manager](xref:security/app-secrets), wenn die App in der `Development`-Umgebung mit der Einstiegsassembly ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="24d1f-135">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="24d1f-136">Umgebungsvariablen mithilfe des [Umgebungsvariablen-Konfigurationsanbieters](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="24d1f-136">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="24d1f-137">Wird ein benutzerdefiniertes Präfix (etwa `PREFIX_` mit `.AddEnvironmentVariables(prefix: "PREFIX_")`) verwendet, wird es beim Laden der Schlüssel-Wert-Paare der Konfiguration entfernt.</span><span class="sxs-lookup"><span data-stu-id="24d1f-137">If a custom prefix is used (for example, `PREFIX_` with `.AddEnvironmentVariables(prefix: "PREFIX_")`), the prefix is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="24d1f-138">Befehlszeilenargumente mithilfe des [Befehlszeilen-Konfigurationsanbieters](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="24d1f-138">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="24d1f-139">Die Konfigurationsanbieter werden im Verlauf des Artikels erläutert.</span><span class="sxs-lookup"><span data-stu-id="24d1f-139">The configuration providers are explained later in this topic.</span></span> <span data-ttu-id="24d1f-140">Weitere Informationen zum Host und zu <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> finden Sie unter <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="24d1f-140">For more information on the host and <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, see <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="security"></a><span data-ttu-id="24d1f-141">Sicherheit</span><span class="sxs-lookup"><span data-stu-id="24d1f-141">Security</span></span>

<span data-ttu-id="24d1f-142">Verwenden Sie die folgenden Best Practices:</span><span class="sxs-lookup"><span data-stu-id="24d1f-142">Adopt the following best practices:</span></span>

* <span data-ttu-id="24d1f-143">Speichern Sie nie Kennwörter oder andere vertrauliche Daten im Konfigurationsanbietercode oder in Nur-Text-Konfigurationsdateien.</span><span class="sxs-lookup"><span data-stu-id="24d1f-143">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="24d1f-144">Verwenden Sie keine Produktionsgeheimnisse in Entwicklungs- oder Testumgebungen.</span><span class="sxs-lookup"><span data-stu-id="24d1f-144">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="24d1f-145">Geben Sie Geheimnisse außerhalb des Projekts an, damit sie nicht versehentlich in ein Quellcoderepository übernommen werden können.</span><span class="sxs-lookup"><span data-stu-id="24d1f-145">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="24d1f-146">Lesen Sie mehr über das [Verwenden von mehreren Umgebungen](xref:fundamentals/environments) und das Verwalten der [sicheren Speicherung von App-Geheimnissen bei der Entwicklung mit dem Geheimnis-Manager](xref:security/app-secrets) (mit Informationen zum Speichern vertraulicher Daten mit Umgebungsvariablen).</span><span class="sxs-lookup"><span data-stu-id="24d1f-146">Learn more about [how to use multiple environments](xref:fundamentals/environments) and managing the [safe storage of app secrets in development with the Secret Manager](xref:security/app-secrets) (includes advice on using environment variables to store sensitive data).</span></span> <span data-ttu-id="24d1f-147">Der Geheimnis-Manager verwendet den Dateikonfigurationsanbieter, um Benutzergeheimnisse in einer JSON-Datei im lokalen System zu speichern.</span><span class="sxs-lookup"><span data-stu-id="24d1f-147">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="24d1f-148">Der Dateikonfigurationsanbieter wird später in diesem Thema beschrieben.</span><span class="sxs-lookup"><span data-stu-id="24d1f-148">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="24d1f-149">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) stellt eine Option für die sichere Speicherung von App-Geheimnissen dar.</span><span class="sxs-lookup"><span data-stu-id="24d1f-149">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) is one option for the safe storage of app secrets.</span></span> <span data-ttu-id="24d1f-150">Weitere Informationen finden Sie unter <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="24d1f-150">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="24d1f-151">Hierarchische Konfigurationsdaten</span><span class="sxs-lookup"><span data-stu-id="24d1f-151">Hierarchical configuration data</span></span>

<span data-ttu-id="24d1f-152">Die Konfigurations-API kann hierarchische Konfigurationsdaten erhalten, indem sie die hierarchischen Daten mit einem Trennzeichen in den Konfigurationsschlüsseln vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="24d1f-152">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="24d1f-153">Die folgende JSON-Datei enthält vier Schlüssel in einer strukturierten Hierarchie von zwei Abschnitten:</span><span class="sxs-lookup"><span data-stu-id="24d1f-153">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

<span data-ttu-id="24d1f-154">Wenn die Datei in die Konfiguration gelesen wird, werden eindeutige Schlüssel erstellt, um die ursprüngliche hierarchische Datenstruktur der Konfigurationsquelle zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="24d1f-154">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="24d1f-155">Die Abschnitte und Schlüssel werden mithilfe eines Doppelpunkts (`:`) vereinfacht, um die Originalstruktur zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="24d1f-155">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="24d1f-156">section0:key0</span><span class="sxs-lookup"><span data-stu-id="24d1f-156">section0:key0</span></span>
* <span data-ttu-id="24d1f-157">section0:key1</span><span class="sxs-lookup"><span data-stu-id="24d1f-157">section0:key1</span></span>
* <span data-ttu-id="24d1f-158">section1:key0</span><span class="sxs-lookup"><span data-stu-id="24d1f-158">section1:key0</span></span>
* <span data-ttu-id="24d1f-159">section1:key1</span><span class="sxs-lookup"><span data-stu-id="24d1f-159">section1:key1</span></span>

<span data-ttu-id="24d1f-160">Mit den Methoden <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> und <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> können Abschnitte und untergeordnete Abschnittelemente in den Konfigurationsdaten isoliert werden.</span><span class="sxs-lookup"><span data-stu-id="24d1f-160"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="24d1f-161">Diese Methoden werden später im Abschnitt [GetSection, GetChildren und Exists](#getsection-getchildren-and-exists) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="24d1f-161">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span> <span data-ttu-id="24d1f-162">`GetSection` befindet sich im Paket [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.</span><span class="sxs-lookup"><span data-stu-id="24d1f-162">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

## <a name="conventions"></a><span data-ttu-id="24d1f-163">Konventionen</span><span class="sxs-lookup"><span data-stu-id="24d1f-163">Conventions</span></span>

<span data-ttu-id="24d1f-164">Beim Starten der Anwendung werden Konfigurationsquellen in der Reihenfolge gelesen, in der ihre Konfigurationsanbieter angegeben sind.</span><span class="sxs-lookup"><span data-stu-id="24d1f-164">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="24d1f-165">Dateikonfigurationsanbieter können Konfigurationen erneut laden, wenn eine zugrunde liegende Einstellungsdatei nach dem App-Start geändert wird.</span><span class="sxs-lookup"><span data-stu-id="24d1f-165">File Configuration Providers have the ability to reload configuration when an underlying settings file is changed after app startup.</span></span> <span data-ttu-id="24d1f-166">Der Dateikonfigurationsanbieter wird später in diesem Thema beschrieben.</span><span class="sxs-lookup"><span data-stu-id="24d1f-166">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="24d1f-167"><xref:Microsoft.Extensions.Configuration.IConfiguration> steht im App-Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="24d1f-167"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="24d1f-168"><xref:Microsoft.Extensions.Configuration.IConfiguration> kann in eine <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel>-Klasse von Razor Pages eingefügt werden, um die Konfiguration für die Klasse abzurufen:</span><span class="sxs-lookup"><span data-stu-id="24d1f-168"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> to obtain configuration for the class:</span></span>

```csharp
// using Microsoft.Extensions.Configuration;

public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    // The _config local variable is used to obtain configuration 
    // throughout the class.
}
```

<span data-ttu-id="24d1f-169">Konfigurationsanbieter können die Abhängigkeitsinjektion nicht verwenden, weil sie nicht verfügbar ist, wenn sie vom Host eingerichtet werden.</span><span class="sxs-lookup"><span data-stu-id="24d1f-169">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

<span data-ttu-id="24d1f-170">Konfigurationsschlüssel entsprechen den folgenden Konventionen:</span><span class="sxs-lookup"><span data-stu-id="24d1f-170">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="24d1f-171">Bei Schlüsseln wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="24d1f-171">Keys are case-insensitive.</span></span> <span data-ttu-id="24d1f-172">Beispielsweise verweisen `ConnectionString` und `connectionstring` auf denselben Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="24d1f-172">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="24d1f-173">Wenn ein Wert für denselben Schlüssel von denselben oder unterschiedlichen Konfigurationsanbietern festgelegt wird, wird der zuletzt für den Schlüssel bestimmte Wert verwendet.</span><span class="sxs-lookup"><span data-stu-id="24d1f-173">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="24d1f-174">Hierarchische Schlüssel</span><span class="sxs-lookup"><span data-stu-id="24d1f-174">Hierarchical keys</span></span>
  * <span data-ttu-id="24d1f-175">Innerhalb der Konfigurations-API funktioniert ein Doppelpunkt (`:`) als Trennzeichen auf allen Plattformen.</span><span class="sxs-lookup"><span data-stu-id="24d1f-175">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="24d1f-176">In Umgebungsvariablen funktioniert ein Doppelpunkt als Trennzeichen ggf. nicht auf allen Plattformen.</span><span class="sxs-lookup"><span data-stu-id="24d1f-176">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="24d1f-177">Ein doppelter Unterstrich (`__`) wird auf allen Plattformen unterstützt und in einen Doppelpunkt konvertiert.</span><span class="sxs-lookup"><span data-stu-id="24d1f-177">A double underscore (`__`) is supported by all platforms and is converted to a colon.</span></span>
  * <span data-ttu-id="24d1f-178">In Azure Key Vault verwenden hierarchische Schlüssel zwei Bindestriche (`--`) als Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="24d1f-178">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="24d1f-179">Sie müssen einen Code bereitstellen, um die Bindestriche durch einen Doppelpunkt zu ersetzen, wenn die Geheimnisse in die App-Konfiguration geladen werden.</span><span class="sxs-lookup"><span data-stu-id="24d1f-179">You must provide code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="24d1f-180"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> unterstützt das Binden von Arrays an Objekte mit Arrayindizes in Konfigurationsschlüsseln.</span><span class="sxs-lookup"><span data-stu-id="24d1f-180">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="24d1f-181">Die Arraybindung wird im Abschnitt [Binden eines Arrays an eine Klasse](#bind-an-array-to-a-class) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="24d1f-181">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

<span data-ttu-id="24d1f-182">Konfigurationswerte entsprechen den folgenden Konventionen:</span><span class="sxs-lookup"><span data-stu-id="24d1f-182">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="24d1f-183">Die Werte sind Zeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="24d1f-183">Values are strings.</span></span>
* <span data-ttu-id="24d1f-184">NULL-Werte können nicht in einer Konfiguration gespeichert oder an Objekte gebunden werden.</span><span class="sxs-lookup"><span data-stu-id="24d1f-184">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="24d1f-185">Anbieter</span><span class="sxs-lookup"><span data-stu-id="24d1f-185">Providers</span></span>

<span data-ttu-id="24d1f-186">Die folgende Tabelle zeigt die für ASP.NET Core-Apps verfügbaren Konfigurationsanbieter.</span><span class="sxs-lookup"><span data-stu-id="24d1f-186">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="24d1f-187">Anbieter</span><span class="sxs-lookup"><span data-stu-id="24d1f-187">Provider</span></span> | <span data-ttu-id="24d1f-188">Bereitstellung der Konfiguration über&hellip;</span><span class="sxs-lookup"><span data-stu-id="24d1f-188">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="24d1f-189">[Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration) (Thema *Sicherheit*)</span><span class="sxs-lookup"><span data-stu-id="24d1f-189">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="24d1f-190">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="24d1f-190">Azure Key Vault</span></span> |
| [<span data-ttu-id="24d1f-191">Befehlszeilen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="24d1f-191">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="24d1f-192">Befehlszeilenparameter</span><span class="sxs-lookup"><span data-stu-id="24d1f-192">Command-line parameters</span></span> |
| [<span data-ttu-id="24d1f-193">Benutzerdefinierter Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="24d1f-193">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="24d1f-194">Benutzerdefinierte Quelle</span><span class="sxs-lookup"><span data-stu-id="24d1f-194">Custom source</span></span> |
| [<span data-ttu-id="24d1f-195">Umgebungsvariablen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="24d1f-195">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="24d1f-196">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="24d1f-196">Environment variables</span></span> |
| [<span data-ttu-id="24d1f-197">Dateikonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="24d1f-197">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="24d1f-198">Dateien (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="24d1f-198">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="24d1f-199">Schlüssel-pro-Datei-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="24d1f-199">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="24d1f-200">Verzeichnisdateien</span><span class="sxs-lookup"><span data-stu-id="24d1f-200">Directory files</span></span> |
| [<span data-ttu-id="24d1f-201">Speicherkonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="24d1f-201">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="24d1f-202">In-Memory-Sammlungen</span><span class="sxs-lookup"><span data-stu-id="24d1f-202">In-memory collections</span></span> |
| <span data-ttu-id="24d1f-203">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) (Thema *Sicherheit*)</span><span class="sxs-lookup"><span data-stu-id="24d1f-203">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="24d1f-204">Datei im Benutzerprofilverzeichnis</span><span class="sxs-lookup"><span data-stu-id="24d1f-204">File in the user profile directory</span></span> |

<span data-ttu-id="24d1f-205">Konfigurationsquellen werden beim Start in der Reihenfolge gelesen, in der ihre Konfigurationsanbieter angegeben sind.</span><span class="sxs-lookup"><span data-stu-id="24d1f-205">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="24d1f-206">Die in diesem Thema beschriebenen Konfigurationsanbieter werden in alphabetischer Reihenfolge beschrieben, nicht in der Reihenfolge, in der Ihr Code sie anordnet.</span><span class="sxs-lookup"><span data-stu-id="24d1f-206">The configuration providers described in this topic are described in alphabetical order, not in the order that your code may arrange them.</span></span> <span data-ttu-id="24d1f-207">Ordnen Sie die Konfigurationsanbieter in Ihrem Code so an, dass sie den Prioritäten für die zugrunde liegenden Konfigurationsquellen entsprechen.</span><span class="sxs-lookup"><span data-stu-id="24d1f-207">Order configuration providers in your code to suit your priorities for the underlying configuration sources.</span></span>

<span data-ttu-id="24d1f-208">Eine typische Konfigurationsanbietersequenz ist:</span><span class="sxs-lookup"><span data-stu-id="24d1f-208">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="24d1f-209">Dateien (*appsettings.json*, *appsettings.{Environment}.json*, wobei `{Environment}` die aktuelle Hostingumgebung der App ist)</span><span class="sxs-lookup"><span data-stu-id="24d1f-209">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="24d1f-210">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="24d1f-210">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="24d1f-211">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) (nur in der Entwicklungsumgebung)</span><span class="sxs-lookup"><span data-stu-id="24d1f-211">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment only)</span></span>
1. <span data-ttu-id="24d1f-212">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="24d1f-212">Environment variables</span></span>
1. <span data-ttu-id="24d1f-213">Befehlszeilenargumente</span><span class="sxs-lookup"><span data-stu-id="24d1f-213">Command-line arguments</span></span>

<span data-ttu-id="24d1f-214">In der Regel werden Befehlszeilen-Konfigurationsanbieter zuletzt in einer Reihe von Anbietern positioniert, damit Befehlszeilenargumente die von anderen Anbietern festgelegte Konfiguration überschreiben können.</span><span class="sxs-lookup"><span data-stu-id="24d1f-214">It's a common practice to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="24d1f-215">Diese Anbieterreihenfolge wird beim Initialisieren eines neuen <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>-Elements mit <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>festgelegt.</span><span class="sxs-lookup"><span data-stu-id="24d1f-215">This sequence of providers is put into place when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="24d1f-216">Weitere Informationen finden Sie unter [Webhost: Einrichten eines Hosts](xref:fundamentals/host/web-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="24d1f-216">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

## <a name="configureappconfiguration"></a><span data-ttu-id="24d1f-217">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="24d1f-217">ConfigureAppConfiguration</span></span>

<span data-ttu-id="24d1f-218">Rufen Sie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> beim Erstellen des Hosts auf, um die Konfigurationsanbieter der App zusätzlich zu denen anzugeben, die automatisch von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="24d1f-218">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration providers in addition to those added automatically by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=19)]

<span data-ttu-id="24d1f-219">Die für die App in der <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>-Methode angegebene Konfiguration, einschließlich `Startup.ConfigureServices`, ist während des Starts der App verfügbar.</span><span class="sxs-lookup"><span data-stu-id="24d1f-219">Configuration supplied to the app in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="24d1f-220">Weitere Informationen finden Sie im Abschnitt [Hinzufügen von Konfigurationen aus einer externen Assembly](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="24d1f-220">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="24d1f-221">Befehlszeilen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="24d1f-221">Command-line Configuration Provider</span></span>

<span data-ttu-id="24d1f-222"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren des Befehlszeilenarguments.</span><span class="sxs-lookup"><span data-stu-id="24d1f-222">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="24d1f-223">Um die Befehlszeilenkonfiguration zu aktivieren, wird die <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>-Erweiterungsmethode für eine Instanz von <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="24d1f-223">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="24d1f-224">`AddCommandLine` wird automatisch aufgerufen, wenn Sie ein neues <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>-Element mit <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> initialisieren.</span><span class="sxs-lookup"><span data-stu-id="24d1f-224">`AddCommandLine` is automatically called when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="24d1f-225">Weitere Informationen finden Sie unter [Webhost: Einrichten eines Hosts](xref:fundamentals/host/web-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="24d1f-225">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="24d1f-226">`CreateDefaultBuilder` lädt außerdem:</span><span class="sxs-lookup"><span data-stu-id="24d1f-226">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="24d1f-227">Optionale Konfiguration aus *appsettings.json* und *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="24d1f-227">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>
* <span data-ttu-id="24d1f-228">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) (in der Entwicklungsumgebung)</span><span class="sxs-lookup"><span data-stu-id="24d1f-228">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="24d1f-229">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="24d1f-229">Environment variables.</span></span>

<span data-ttu-id="24d1f-230">`CreateDefaultBuilder` fügt den Befehlszeilen-Konfigurationsanbieter zuletzt hinzu.</span><span class="sxs-lookup"><span data-stu-id="24d1f-230">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="24d1f-231">Während der Laufzeit übergebene Befehlszeilenargumente überschreiben die von anderen Anbietern festgelegte Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="24d1f-231">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="24d1f-232">`CreateDefaultBuilder` wird aktiv, wenn der Host erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="24d1f-232">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="24d1f-233">Deswegen kann die durch `CreateDefaultBuilder` aktivierte Befehlszeilenkonfiguration die Konfiguration des Hosts beeinflussen.</span><span class="sxs-lookup"><span data-stu-id="24d1f-233">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="24d1f-234">Rufen Sie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben.</span><span class="sxs-lookup"><span data-stu-id="24d1f-234">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="24d1f-235">`AddCommandLine` wurde bereits von `CreateDefaultBuilder` aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="24d1f-235">`AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="24d1f-236">Wenn Sie die App-Konfiguration bereitstellen müssen und weiterhin in der Lage sein möchten, diese Konfiguration mit Befehlszeilenargumenten außer Kraft zu setzen, rufen Sie die zusätzlichen Anbieter der App in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> auf und rufen `AddCommandLine` zuletzt auf.</span><span class="sxs-lookup"><span data-stu-id="24d1f-236">If you need to provide app configuration and still be able to override that configuration with command-line arguments, call the app's additional providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> and call `AddCommandLine` last.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                // Call other providers here and call AddCommandLine last.
                config.AddCommandLine(args);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="24d1f-237">Rufen Sie beim direkten Erstellen von <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> mit der Konfiguration auf:</span><span class="sxs-lookup"><span data-stu-id="24d1f-237">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var config = new ConfigurationBuilder()
    // Call additional providers here as needed.
    // Call AddCommandLine last to allow arguments to override other configuration.
    .AddCommandLine(args)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="24d1f-238">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="24d1f-238">**Example**</span></span>

<span data-ttu-id="24d1f-239">Die Beispiel-App nutzt zum Erstellen des Hosts die statische Hilfsmethode `CreateDefaultBuilder`, die einen Aufruf von <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> enthält.</span><span class="sxs-lookup"><span data-stu-id="24d1f-239">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="24d1f-240">Öffnen Sie im Projektverzeichnis eine Eingabeaufforderung.</span><span class="sxs-lookup"><span data-stu-id="24d1f-240">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="24d1f-241">Geben Sie ein Befehlszeilenargument für den `dotnet run`-Befehl an, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="24d1f-241">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="24d1f-242">Wenn die App ausgeführt wird, öffnen Sie einen Browser für die App unter `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="24d1f-242">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="24d1f-243">Beachten Sie, dass die Ausgabe das Schlüssel-Wert-Paar für das an `dotnet run` übergebene Konfigurations-Befehlszeilenargument enthält.</span><span class="sxs-lookup"><span data-stu-id="24d1f-243">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="24d1f-244">Argumente</span><span class="sxs-lookup"><span data-stu-id="24d1f-244">Arguments</span></span>

<span data-ttu-id="24d1f-245">Der Wert muss einem Gleichheitszeichen (`=`) folgen, oder der Schlüssel muss ein Präfix (`--` oder `/`) haben, wenn der Wert einem Leerzeichen folgt.</span><span class="sxs-lookup"><span data-stu-id="24d1f-245">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="24d1f-246">Der Wert kann NULL sein, wenn ein Gleichheitszeichen verwendet wird (z.B. `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="24d1f-246">The value can be null if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="24d1f-247">Schlüsselpräfix</span><span class="sxs-lookup"><span data-stu-id="24d1f-247">Key prefix</span></span>               | <span data-ttu-id="24d1f-248">Beispiel</span><span class="sxs-lookup"><span data-stu-id="24d1f-248">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="24d1f-249">Ohne Präfix</span><span class="sxs-lookup"><span data-stu-id="24d1f-249">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="24d1f-250">Zwei Gedankenstriche (`--`)</span><span class="sxs-lookup"><span data-stu-id="24d1f-250">Two dashes (`--`)</span></span>        | <span data-ttu-id="24d1f-251">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="24d1f-251">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="24d1f-252">Schrägstrich (`/`)</span><span class="sxs-lookup"><span data-stu-id="24d1f-252">Forward slash (`/`)</span></span>      | <span data-ttu-id="24d1f-253">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="24d1f-253">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="24d1f-254">Kombinieren Sie in einem Befehl nicht Schlüssel-Wert-Paare, die ein Gleichheitszeichen verwenden, mit Schlüssel-Wert-Paaren, die ein Leerzeichen verwenden.</span><span class="sxs-lookup"><span data-stu-id="24d1f-254">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="24d1f-255">Beispielbefehle:</span><span class="sxs-lookup"><span data-stu-id="24d1f-255">Example commands:</span></span>

```console
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="24d1f-256">Switchmappings</span><span class="sxs-lookup"><span data-stu-id="24d1f-256">Switch mappings</span></span>

<span data-ttu-id="24d1f-257">Switchmappings erlauben das Angeben einer Logik zum Ersetzen von Schlüsselnamen.</span><span class="sxs-lookup"><span data-stu-id="24d1f-257">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="24d1f-258">Wenn Sie die Konfiguration manuell mit <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> erstellen, können Sie ein Wörterbuch der Switchersetzungen für die <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>-Methode bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="24d1f-258">When you manually build configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, you can provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="24d1f-259">Wenn das Switchmappingwörterbuch verwendet wird, wird das Wörterbuch für Schlüssel, die dem von einem Befehlszeilenargument angegebenen Schlüssel entsprechen, überprüft.</span><span class="sxs-lookup"><span data-stu-id="24d1f-259">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="24d1f-260">Wenn der Befehlszeilenschlüssel im Wörterbuch gefunden wird, wird der Wörterbuchwert (der Schlüsselersatz) zum Festlegen des Schlüssel-Wert-Paares in der App-Konfiguration zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="24d1f-260">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="24d1f-261">Ein Switchmapping ist für jeden Befehlszeilenschlüssel erforderlich, dem ein einzelner Gedankenstrich (`-`) vorangestellt ist.</span><span class="sxs-lookup"><span data-stu-id="24d1f-261">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="24d1f-262">Regeln für Schlüssel von Switchmappingwörterbüchern:</span><span class="sxs-lookup"><span data-stu-id="24d1f-262">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="24d1f-263">Switchmappings müssen mit einem Gedankenstrich (`-`) oder zwei Gedankenstrichen (`--`) beginnen.</span><span class="sxs-lookup"><span data-stu-id="24d1f-263">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="24d1f-264">Das Switchmappingwörterbuch darf keine doppelten Schlüssel enthalten.</span><span class="sxs-lookup"><span data-stu-id="24d1f-264">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="24d1f-265">Rufen Sie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="24d1f-265">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static readonly Dictionary<string, string> _switchMappings = 
        new Dictionary<string, string>
        {
            { "-CLKey1", "CommandLineKey1" },
            { "-CLKey2", "CommandLineKey2" }
        };

    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    // Do not pass the args to CreateDefaultBuilder
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder()
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.AddCommandLine(args, _switchMappings);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="24d1f-266">Wie im vorherigen Beispiel gezeigt, darf der Aufruf von `CreateDefaultBuilder` keine Argumente übergeben, wenn Switchmappings verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="24d1f-266">As shown in the preceding example, the call to `CreateDefaultBuilder` shouldn't pass arguments when switch mappings are used.</span></span> <span data-ttu-id="24d1f-267">Der Aufruf `AddCommandLine` der `CreateDefaultBuilder`-Methode umfasst keine zugeordneten Switches, und das Switchmappingwörterbuch kann nicht an `CreateDefaultBuilder` übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="24d1f-267">`CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="24d1f-268">Wenn die Argumente einen zugeordneten Switch enthalten und an `CreateDefaultBuilder` übergeben werden, kann der `AddCommandLine`-Anbieter nicht mit <xref:System.FormatException> initialisiert werden.</span><span class="sxs-lookup"><span data-stu-id="24d1f-268">If the arguments include a mapped switch and are passed to `CreateDefaultBuilder`, its `AddCommandLine` provider fails to initialize with a <xref:System.FormatException>.</span></span> <span data-ttu-id="24d1f-269">Die Lösung besteht nicht darin, die Argumente an `CreateDefaultBuilder` zu übergeben, sondern der Methode `AddCommandLine` der `ConfigurationBuilder`-Methode zu erlauben, sowohl die Argumente als auch das Switchmappingwörterbuch zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="24d1f-269">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="24d1f-270">Das erstellte Switchmappingwörterbuch enthält die in der folgenden Tabelle gezeigten Daten.</span><span class="sxs-lookup"><span data-stu-id="24d1f-270">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="24d1f-271">Key</span><span class="sxs-lookup"><span data-stu-id="24d1f-271">Key</span></span>       | <span data-ttu-id="24d1f-272">Wert</span><span class="sxs-lookup"><span data-stu-id="24d1f-272">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="24d1f-273">Wenn Switches zugeordnete Schlüssel beim Start der App verwendet werden, erhält die Konfiguration den Konfigurationswert auf dem vom Wörterbuch bereitgestellten Schlüssel:</span><span class="sxs-lookup"><span data-stu-id="24d1f-273">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```console
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="24d1f-274">Nach dem Ausführen des obigen Befehls enthält die Konfiguration die in der folgenden Tabelle angegebenen Werte.</span><span class="sxs-lookup"><span data-stu-id="24d1f-274">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="24d1f-275">Key</span><span class="sxs-lookup"><span data-stu-id="24d1f-275">Key</span></span>               | <span data-ttu-id="24d1f-276">Wert</span><span class="sxs-lookup"><span data-stu-id="24d1f-276">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="24d1f-277">Umgebungsvariablen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="24d1f-277">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="24d1f-278"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="24d1f-278">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="24d1f-279">Um die Umgebungsvariablenkonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="24d1f-279">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="24d1f-280">Beim Verwenden hierarchischer Schlüssel in Umgebungsvariablen funktioniert ein Doppelpunkt (`:`) als Trennzeichen ggf. nicht auf allen Plattformen.</span><span class="sxs-lookup"><span data-stu-id="24d1f-280">When working with hierarchical keys in environment variables, a colon separator (`:`) may not work on all platforms.</span></span> <span data-ttu-id="24d1f-281">Ein doppelter Unterstrich (`__`) wird auf allen Plattformen unterstützt und durch einen Doppelpunkt ersetzt.</span><span class="sxs-lookup"><span data-stu-id="24d1f-281">A double underscore (`__`) is supported by all platforms and is replaced by a colon.</span></span>

<span data-ttu-id="24d1f-282">[Azure App Service](https://azure.microsoft.com/services/app-service/) ermöglicht Ihnen Umgebungsvariablen im Azure-Portal festzulegen, die die App-Konfiguration mit dem Umgebungsvariablen-Konfigurationsanbieter überschreiben können.</span><span class="sxs-lookup"><span data-stu-id="24d1f-282">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits you to set environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="24d1f-283">Weitere Informationen finden Sie unter [Azure-Apps: Überschreiben der App-Konfiguration im Azure-Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="24d1f-283">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="24d1f-284">`AddEnvironmentVariables` wird für Umgebungsvariablen mit dem Präfix `ASPNETCORE_` automatisch aufgerufen, wenn Sie einen neuen <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> initialisieren.</span><span class="sxs-lookup"><span data-stu-id="24d1f-284">`AddEnvironmentVariables` is automatically called for environment variables prefixed with `ASPNETCORE_` when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="24d1f-285">Weitere Informationen finden Sie unter [Webhost: Einrichten eines Hosts](xref:fundamentals/host/web-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="24d1f-285">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="24d1f-286">`CreateDefaultBuilder` lädt außerdem:</span><span class="sxs-lookup"><span data-stu-id="24d1f-286">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="24d1f-287">App-Konfiguration über Umgebungsvariablen ohne Präfix durch Aufruf von `AddEnvironmentVariables` ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="24d1f-287">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="24d1f-288">Optionale Konfiguration aus *appsettings.json* und *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="24d1f-288">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>
* <span data-ttu-id="24d1f-289">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) (in der Entwicklungsumgebung)</span><span class="sxs-lookup"><span data-stu-id="24d1f-289">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="24d1f-290">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="24d1f-290">Command-line arguments.</span></span>

<span data-ttu-id="24d1f-291">Der Umgebungsvariablen-Konfigurationsanbieter wird aufgerufen, nachdem die Konfiguration aus Benutzergeheimnissen und *appsettings*-Dateien erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="24d1f-291">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="24d1f-292">Das Aufrufen des Anbieters an dieser Stelle ermöglicht den während der Laufzeit gelesenen Umgebungsvariablen, die von Benutzergeheimnissen und *appsettings*-Dateien festgelegte Konfiguration zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="24d1f-292">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="24d1f-293">Rufen Sie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben.</span><span class="sxs-lookup"><span data-stu-id="24d1f-293">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="24d1f-294">Wenn Sie App-Konfigurationen aus zusätzlichen Umgebungsvariablen bereitstellen müssen, rufen Sie die zusätzlichen Anbieter der App in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> auf, und rufen Sie `AddEnvironmentVariables` mit dem Präfix auf.</span><span class="sxs-lookup"><span data-stu-id="24d1f-294">If you need to provide app configuration from additional environment variables, call the app's additional providers in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> and call `AddEnvironmentVariables` with the prefix.</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                // Call additional providers here as needed.
                // Call AddEnvironmentVariables last if you need to allow environment
                // variables to override values from other providers.
                config.AddEnvironmentVariables(prefix: "PREFIX_");
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="24d1f-295">Rufen Sie beim direkten Erstellen von <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> mit der Konfiguration auf:</span><span class="sxs-lookup"><span data-stu-id="24d1f-295">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables()
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="24d1f-296">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="24d1f-296">**Example**</span></span>

<span data-ttu-id="24d1f-297">Die Beispiel-App nutzt zum Erstellen des Hosts die statische Hilfsmethode `CreateDefaultBuilder`, die einen Aufruf von `AddEnvironmentVariables` enthält.</span><span class="sxs-lookup"><span data-stu-id="24d1f-297">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="24d1f-298">Führen Sie die Beispiel-App aus.</span><span class="sxs-lookup"><span data-stu-id="24d1f-298">Run the sample app.</span></span> <span data-ttu-id="24d1f-299">Öffnen Sie einen Browser für die App unter `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="24d1f-299">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="24d1f-300">Beachten Sie, dass die Ausgabe das Schlüssel-Wert-Paar für die Umgebungsvariable `ENVIRONMENT` enthält.</span><span class="sxs-lookup"><span data-stu-id="24d1f-300">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="24d1f-301">Der Wert entspricht der Umgebung, in der die App ausgeführt wird. In der Regel ist das `Development` bei lokaler Ausführung.</span><span class="sxs-lookup"><span data-stu-id="24d1f-301">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="24d1f-302">Um die Liste der von der App gerenderten Umgebungsvariablen kurz zu halten, filtert die App Umgebungsvariablen, die folgendermaßen beginnen:</span><span class="sxs-lookup"><span data-stu-id="24d1f-302">To keep the list of environment variables rendered by the app short, the app filters environment variables to those that start with the following:</span></span>

* <span data-ttu-id="24d1f-303">ASPNETCORE_</span><span class="sxs-lookup"><span data-stu-id="24d1f-303">ASPNETCORE_</span></span>
* <span data-ttu-id="24d1f-304">urls</span><span class="sxs-lookup"><span data-stu-id="24d1f-304">urls</span></span>
* <span data-ttu-id="24d1f-305">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="24d1f-305">Logging</span></span>
* <span data-ttu-id="24d1f-306">ENVIRONMENT</span><span class="sxs-lookup"><span data-stu-id="24d1f-306">ENVIRONMENT</span></span>
* <span data-ttu-id="24d1f-307">contentRoot</span><span class="sxs-lookup"><span data-stu-id="24d1f-307">contentRoot</span></span>
* <span data-ttu-id="24d1f-308">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="24d1f-308">AllowedHosts</span></span>
* <span data-ttu-id="24d1f-309">applicationName</span><span class="sxs-lookup"><span data-stu-id="24d1f-309">applicationName</span></span>
* <span data-ttu-id="24d1f-310">COMMANDLINE</span><span class="sxs-lookup"><span data-stu-id="24d1f-310">CommandLine</span></span>

<span data-ttu-id="24d1f-311">Wenn Sie alle für die App verfügbaren Umgebungsvariablen verfügbar machen möchten, ändern Sie `FilteredConfiguration` in *Pages/Index.cshtml.cs* wie folgt:</span><span class="sxs-lookup"><span data-stu-id="24d1f-311">If you wish to expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="24d1f-312">Präfixe</span><span class="sxs-lookup"><span data-stu-id="24d1f-312">Prefixes</span></span>

<span data-ttu-id="24d1f-313">Umgebungsvariablen, die in die Konfiguration der App geladen werden, werden gefiltert, wenn Sie ein Präfix für die Methode `AddEnvironmentVariables` angeben.</span><span class="sxs-lookup"><span data-stu-id="24d1f-313">Environment variables loaded into the app's configuration are filtered when you supply a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="24d1f-314">Um beispielsweise Umgebungsvariablen nach dem Präfix `CUSTOM_` zu filtern, geben Sie das Präfix für den Konfigurationsanbieter an:</span><span class="sxs-lookup"><span data-stu-id="24d1f-314">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="24d1f-315">Das Präfix wird beim Erstellen der Schlüssel-Wert-Paare der Konfiguration entfernt.</span><span class="sxs-lookup"><span data-stu-id="24d1f-315">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="24d1f-316">Die statische Hilfsmethode `CreateDefaultBuilder` erstellt ein <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>-Element, um den Host der App einzurichten.</span><span class="sxs-lookup"><span data-stu-id="24d1f-316">The static convenience method `CreateDefaultBuilder` creates a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> to establish the app's host.</span></span> <span data-ttu-id="24d1f-317">Wenn `WebHostBuilder` erstellt wird, befindet sich die Hostkonfiguration in Umgebungsvariablen mit dem Präfix `ASPNETCORE_`.</span><span class="sxs-lookup"><span data-stu-id="24d1f-317">When `WebHostBuilder` is created, it finds its host configuration in environment variables prefixed with `ASPNETCORE_`.</span></span>

<span data-ttu-id="24d1f-318">**Präfixe für Verbindungszeichenfolgen**</span><span class="sxs-lookup"><span data-stu-id="24d1f-318">**Connection string prefixes**</span></span>

<span data-ttu-id="24d1f-319">Die Konfigurations-API verfügt über spezielle Verarbeitungsregeln für vier Umgebungsvariablen für Verbindungszeichenfolgen, die beim Konfigurieren von Azure-Verbindungszeichenfolgen für die App-Umgebung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="24d1f-319">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="24d1f-320">Umgebungsvariablen mit Präfixen, die in der Tabelle aufgeführt werden, werden in die App geladen, wenn für `AddEnvironmentVariables` kein Präfix angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="24d1f-320">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="24d1f-321">Präfix für Verbindungszeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="24d1f-321">Connection string prefix</span></span> | <span data-ttu-id="24d1f-322">Anbieter</span><span class="sxs-lookup"><span data-stu-id="24d1f-322">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="24d1f-323">Benutzerdefinierter Anbieter</span><span class="sxs-lookup"><span data-stu-id="24d1f-323">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="24d1f-324">MySQL</span><span class="sxs-lookup"><span data-stu-id="24d1f-324">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="24d1f-325">Azure SQL-Datenbank</span><span class="sxs-lookup"><span data-stu-id="24d1f-325">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="24d1f-326">SQL Server</span><span class="sxs-lookup"><span data-stu-id="24d1f-326">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="24d1f-327">Wenn eine Umgebungsvariable entdeckt und mit einem der vier Präfixe aus der Tabelle in die Konfiguration geladen wird, tritt Folgendes ein:</span><span class="sxs-lookup"><span data-stu-id="24d1f-327">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="24d1f-328">Der Konfigurationsschlüssel wird durch Entfernen des Umgebungsvariablenpräfixes und Hinzufügen eines Konfigurationsschlüsselabschnitts (`ConnectionStrings`) erstellt.</span><span class="sxs-lookup"><span data-stu-id="24d1f-328">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="24d1f-329">Ein neues Konfigurations-Schlüssel-Wert-Paar wird erstellt, das den Datenbankverbindungsanbieter repräsentiert (mit Ausnahme des Präfixes `CUSTOMCONNSTR_`, für das kein Anbieter angegeben ist).</span><span class="sxs-lookup"><span data-stu-id="24d1f-329">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="24d1f-330">Umgebungsvariablenschlüssel</span><span class="sxs-lookup"><span data-stu-id="24d1f-330">Environment variable key</span></span> | <span data-ttu-id="24d1f-331">Konvertierter Konfigurationsschlüssel</span><span class="sxs-lookup"><span data-stu-id="24d1f-331">Converted configuration key</span></span> | <span data-ttu-id="24d1f-332">Anbieterkonfigurationseintrag</span><span class="sxs-lookup"><span data-stu-id="24d1f-332">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | <span data-ttu-id="24d1f-333">Konfigurationseintrag wurde nicht erstellt.</span><span class="sxs-lookup"><span data-stu-id="24d1f-333">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | <span data-ttu-id="24d1f-334">Schlüssel: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="24d1f-334">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="24d1f-335">Wert: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="24d1f-335">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | <span data-ttu-id="24d1f-336">Schlüssel: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="24d1f-336">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="24d1f-337">Wert: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="24d1f-337">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | <span data-ttu-id="24d1f-338">Schlüssel: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="24d1f-338">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="24d1f-339">Wert: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="24d1f-339">Value: `System.Data.SqlClient`</span></span>  |

## <a name="file-configuration-provider"></a><span data-ttu-id="24d1f-340">Dateikonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="24d1f-340">File Configuration Provider</span></span>

<span data-ttu-id="24d1f-341"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> ist die Basisklasse für das Laden einer Konfiguration aus dem Dateisystem.</span><span class="sxs-lookup"><span data-stu-id="24d1f-341"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="24d1f-342">Die folgenden Konfigurationsanbieter sind für bestimmte Dateitypen vorgesehen:</span><span class="sxs-lookup"><span data-stu-id="24d1f-342">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="24d1f-343">INI-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="24d1f-343">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="24d1f-344">JSON-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="24d1f-344">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="24d1f-345">XML-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="24d1f-345">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="24d1f-346">INI-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="24d1f-346">INI Configuration Provider</span></span>

<span data-ttu-id="24d1f-347"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der INI-Datei.</span><span class="sxs-lookup"><span data-stu-id="24d1f-347">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="24d1f-348">Um die INI-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="24d1f-348">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="24d1f-349">Der Doppelpunkt kann in einer INI-Dateikonfiguration als Abschnittstrennzeichen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="24d1f-349">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="24d1f-350">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="24d1f-350">Overloads permit specifying:</span></span>

* <span data-ttu-id="24d1f-351">Ob die Datei optional ist</span><span class="sxs-lookup"><span data-stu-id="24d1f-351">Whether the file is optional.</span></span>
* <span data-ttu-id="24d1f-352">Ob die Konfiguration bei Dateiänderungen neu geladen wird</span><span class="sxs-lookup"><span data-stu-id="24d1f-352">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="24d1f-353">Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird</span><span class="sxs-lookup"><span data-stu-id="24d1f-353">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="24d1f-354">Rufen Sie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="24d1f-354">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                config.AddIniFile("config.ini", optional: true, reloadOnChange: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="24d1f-355">Der Basispfad wird mit <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> festgelegt.</span><span class="sxs-lookup"><span data-stu-id="24d1f-355">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="24d1f-356">`SetBasePath` befindet sich im Paket [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.</span><span class="sxs-lookup"><span data-stu-id="24d1f-356">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="24d1f-357">Rufen Sie beim direkten Erstellen von <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> mit der Konfiguration auf:</span><span class="sxs-lookup"><span data-stu-id="24d1f-357">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .SetBasePath(Directory.GetCurrentDirectory())
    .AddIniFile("config.ini", optional: true, reloadOnChange: true)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="24d1f-358">Der Basispfad wird mit <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> festgelegt.</span><span class="sxs-lookup"><span data-stu-id="24d1f-358">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="24d1f-359">`SetBasePath` befindet sich im Paket [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.</span><span class="sxs-lookup"><span data-stu-id="24d1f-359">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="24d1f-360">Ein allgemeines Beispiel einer INI-Konfigurationsdatei:</span><span class="sxs-lookup"><span data-stu-id="24d1f-360">A generic example of an INI configuration file:</span></span>

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

<span data-ttu-id="24d1f-361">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="24d1f-361">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="24d1f-362">section0:key0</span><span class="sxs-lookup"><span data-stu-id="24d1f-362">section0:key0</span></span>
* <span data-ttu-id="24d1f-363">section0:key1</span><span class="sxs-lookup"><span data-stu-id="24d1f-363">section0:key1</span></span>
* <span data-ttu-id="24d1f-364">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="24d1f-364">section1:subsection:key</span></span>
* <span data-ttu-id="24d1f-365">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="24d1f-365">section2:subsection0:key</span></span>
* <span data-ttu-id="24d1f-366">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="24d1f-366">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="24d1f-367">JSON-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="24d1f-367">JSON Configuration Provider</span></span>

<span data-ttu-id="24d1f-368"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der JSON-Datei.</span><span class="sxs-lookup"><span data-stu-id="24d1f-368">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="24d1f-369">Um die JSON-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="24d1f-369">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="24d1f-370">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="24d1f-370">Overloads permit specifying:</span></span>

* <span data-ttu-id="24d1f-371">Ob die Datei optional ist</span><span class="sxs-lookup"><span data-stu-id="24d1f-371">Whether the file is optional.</span></span>
* <span data-ttu-id="24d1f-372">Ob die Konfiguration bei Dateiänderungen neu geladen wird</span><span class="sxs-lookup"><span data-stu-id="24d1f-372">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="24d1f-373">Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird</span><span class="sxs-lookup"><span data-stu-id="24d1f-373">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>



<span data-ttu-id="24d1f-374">`AddJsonFile` wird automatisch zweimal aufgerufen, wenn Sie ein neues <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>-Element mit <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> initialisieren.</span><span class="sxs-lookup"><span data-stu-id="24d1f-374">`AddJsonFile` is automatically called twice when you initialize a new <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> with <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span> <span data-ttu-id="24d1f-375">Die Methode wird aufgerufen, um die Konfiguration aus folgenden Dateien zu laden:</span><span class="sxs-lookup"><span data-stu-id="24d1f-375">The method is called to load configuration from:</span></span>

* <span data-ttu-id="24d1f-376">*appsettings.json* &ndash; Diese Datei wird zuerst gelesen.</span><span class="sxs-lookup"><span data-stu-id="24d1f-376">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="24d1f-377">Die Umgebungsversion der Datei kann die Werte der Datei *appsettings.json* überschreiben.</span><span class="sxs-lookup"><span data-stu-id="24d1f-377">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="24d1f-378">*appsettings.{Environment}.json* &ndash; Die Umgebungsversion der Datei wird basierend auf [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) geladen.</span><span class="sxs-lookup"><span data-stu-id="24d1f-378">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="24d1f-379">Weitere Informationen finden Sie unter [Webhost: Einrichten eines Hosts](xref:fundamentals/host/web-host#set-up-a-host).</span><span class="sxs-lookup"><span data-stu-id="24d1f-379">For more information, see [Web Host: Set up a host](xref:fundamentals/host/web-host#set-up-a-host).</span></span>

<span data-ttu-id="24d1f-380">`CreateDefaultBuilder` lädt außerdem:</span><span class="sxs-lookup"><span data-stu-id="24d1f-380">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="24d1f-381">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="24d1f-381">Environment variables.</span></span>
* <span data-ttu-id="24d1f-382">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) (in der Entwicklungsumgebung)</span><span class="sxs-lookup"><span data-stu-id="24d1f-382">[User secrets (Secret Manager)](xref:security/app-secrets) (in the Development environment).</span></span>
* <span data-ttu-id="24d1f-383">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="24d1f-383">Command-line arguments.</span></span>

<span data-ttu-id="24d1f-384">Der JSON-Konfigurationsanbieter wird zuerst eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="24d1f-384">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="24d1f-385">Daher überschreiben Benutzergeheimnisse, Umgebungsvariablen und Befehlszeilenargumente die von *appsettings*-Dateien festgelegte Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="24d1f-385">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="24d1f-386">Rufen Sie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> beim Erstellen des Hosts auf, um Konfiguration der App für andere Dateien als *appsettings.json* und *appsettings.{Environment}.json* anzugeben:</span><span class="sxs-lookup"><span data-stu-id="24d1f-386">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                config.AddJsonFile("config.json", optional: true, reloadOnChange: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="24d1f-387">Der Basispfad wird mit <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> festgelegt.</span><span class="sxs-lookup"><span data-stu-id="24d1f-387">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="24d1f-388">`SetBasePath` befindet sich im Paket [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.</span><span class="sxs-lookup"><span data-stu-id="24d1f-388">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="24d1f-389">Rufen Sie beim direkten Erstellen von <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> mit der Konfiguration auf:</span><span class="sxs-lookup"><span data-stu-id="24d1f-389">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .SetBasePath(Directory.GetCurrentDirectory())
    .AddJsonFile("config.json", optional: true, reloadOnChange: true)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="24d1f-390">Der Basispfad wird mit <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> festgelegt.</span><span class="sxs-lookup"><span data-stu-id="24d1f-390">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="24d1f-391">`SetBasePath` befindet sich im Paket [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.</span><span class="sxs-lookup"><span data-stu-id="24d1f-391">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="24d1f-392">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="24d1f-392">**Example**</span></span>

<span data-ttu-id="24d1f-393">Die Beispiel-App nutzt zum Erstellen des Hosts die statische Hilfsmethode `CreateDefaultBuilder`, die zwei Aufrufe von `AddJsonFile` enthält.</span><span class="sxs-lookup"><span data-stu-id="24d1f-393">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`.</span></span> <span data-ttu-id="24d1f-394">Die Konfiguration wird aus *appsettings.json* und *appsettings.{Environment}.json* geladen.</span><span class="sxs-lookup"><span data-stu-id="24d1f-394">Configuration is loaded from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>

1. <span data-ttu-id="24d1f-395">Führen Sie die Beispiel-App aus.</span><span class="sxs-lookup"><span data-stu-id="24d1f-395">Run the sample app.</span></span> <span data-ttu-id="24d1f-396">Öffnen Sie einen Browser für die App unter `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="24d1f-396">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="24d1f-397">Die Ausgabe enthält je nach Umgebung Schlüssel-Wert-Paare für die in der Tabelle dargestellte Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="24d1f-397">Observe that the output contains key-value pairs for the configuration shown in the table depending on the environment.</span></span> <span data-ttu-id="24d1f-398">Protokollierungskonfigurationsschlüssel verwenden den Doppelpunkt (`:`) als hierarchisches Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="24d1f-398">Logging configuration keys use the colon (`:`) as a hierarchical separator.</span></span>

| <span data-ttu-id="24d1f-399">Key</span><span class="sxs-lookup"><span data-stu-id="24d1f-399">Key</span></span>                        | <span data-ttu-id="24d1f-400">Entwicklungswert</span><span class="sxs-lookup"><span data-stu-id="24d1f-400">Development Value</span></span> | <span data-ttu-id="24d1f-401">Produktionswert</span><span class="sxs-lookup"><span data-stu-id="24d1f-401">Production Value</span></span> |
| -------------------------- | :---------------: | :--------------: |
| <span data-ttu-id="24d1f-402">Logging:LogLevel:System</span><span class="sxs-lookup"><span data-stu-id="24d1f-402">Logging:LogLevel:System</span></span>    | <span data-ttu-id="24d1f-403">Information</span><span class="sxs-lookup"><span data-stu-id="24d1f-403">Information</span></span>       | <span data-ttu-id="24d1f-404">Information</span><span class="sxs-lookup"><span data-stu-id="24d1f-404">Information</span></span>      |
| <span data-ttu-id="24d1f-405">Logging:LogLevel:Microsoft</span><span class="sxs-lookup"><span data-stu-id="24d1f-405">Logging:LogLevel:Microsoft</span></span> | <span data-ttu-id="24d1f-406">Information</span><span class="sxs-lookup"><span data-stu-id="24d1f-406">Information</span></span>       | <span data-ttu-id="24d1f-407">Information</span><span class="sxs-lookup"><span data-stu-id="24d1f-407">Information</span></span>      |
| <span data-ttu-id="24d1f-408">Logging:LogLevel:Default</span><span class="sxs-lookup"><span data-stu-id="24d1f-408">Logging:LogLevel:Default</span></span>   | <span data-ttu-id="24d1f-409">Debug</span><span class="sxs-lookup"><span data-stu-id="24d1f-409">Debug</span></span>             | <span data-ttu-id="24d1f-410">Fehler</span><span class="sxs-lookup"><span data-stu-id="24d1f-410">Error</span></span>            |
| <span data-ttu-id="24d1f-411">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="24d1f-411">AllowedHosts</span></span>               | *                 | *                |

### <a name="xml-configuration-provider"></a><span data-ttu-id="24d1f-412">XML-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="24d1f-412">XML Configuration Provider</span></span>

<span data-ttu-id="24d1f-413"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der XML-Datei.</span><span class="sxs-lookup"><span data-stu-id="24d1f-413">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="24d1f-414">Um die XML-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="24d1f-414">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="24d1f-415">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="24d1f-415">Overloads permit specifying:</span></span>

* <span data-ttu-id="24d1f-416">Ob die Datei optional ist</span><span class="sxs-lookup"><span data-stu-id="24d1f-416">Whether the file is optional.</span></span>
* <span data-ttu-id="24d1f-417">Ob die Konfiguration bei Dateiänderungen neu geladen wird</span><span class="sxs-lookup"><span data-stu-id="24d1f-417">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="24d1f-418">Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird</span><span class="sxs-lookup"><span data-stu-id="24d1f-418">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="24d1f-419">Der Stammknoten der Konfigurationsdatei wird beim Erstellen der Konfigurations-Schlüssel-Wert-Paare ignoriert.</span><span class="sxs-lookup"><span data-stu-id="24d1f-419">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="24d1f-420">Geben Sie keine Dokumenttypdefinition (DTD) und keinen Namespace in der Datei an.</span><span class="sxs-lookup"><span data-stu-id="24d1f-420">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="24d1f-421">Rufen Sie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="24d1f-421">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                config.AddXmlFile("config.xml", optional: true, reloadOnChange: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="24d1f-422">Der Basispfad wird mit <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> festgelegt.</span><span class="sxs-lookup"><span data-stu-id="24d1f-422">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="24d1f-423">`SetBasePath` befindet sich im Paket [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.</span><span class="sxs-lookup"><span data-stu-id="24d1f-423">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="24d1f-424">Rufen Sie beim direkten Erstellen von <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> mit der Konfiguration auf:</span><span class="sxs-lookup"><span data-stu-id="24d1f-424">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .SetBasePath(Directory.GetCurrentDirectory())
    .AddXmlFile("config.xml", optional: true, reloadOnChange: true)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

<span data-ttu-id="24d1f-425">Der Basispfad wird mit <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> festgelegt.</span><span class="sxs-lookup"><span data-stu-id="24d1f-425">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="24d1f-426">`SetBasePath` befindet sich im Paket [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.</span><span class="sxs-lookup"><span data-stu-id="24d1f-426">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="24d1f-427">XML-Konfigurationsdateien können unterschiedliche Elementnamen für wiederholte Abschnitte verwenden:</span><span class="sxs-lookup"><span data-stu-id="24d1f-427">XML configuration files can use distinct element names for repeating sections:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

<span data-ttu-id="24d1f-428">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="24d1f-428">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="24d1f-429">section0:key0</span><span class="sxs-lookup"><span data-stu-id="24d1f-429">section0:key0</span></span>
* <span data-ttu-id="24d1f-430">section0:key1</span><span class="sxs-lookup"><span data-stu-id="24d1f-430">section0:key1</span></span>
* <span data-ttu-id="24d1f-431">section1:key0</span><span class="sxs-lookup"><span data-stu-id="24d1f-431">section1:key0</span></span>
* <span data-ttu-id="24d1f-432">section1:key1</span><span class="sxs-lookup"><span data-stu-id="24d1f-432">section1:key1</span></span>

<span data-ttu-id="24d1f-433">Wiederholte Elemente mit den gleichen Elementnamen funktionieren, wenn das `name`-Attribut zur Unterscheidung der Elemente verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="24d1f-433">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

<span data-ttu-id="24d1f-434">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="24d1f-434">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="24d1f-435">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="24d1f-435">section:section0:key:key0</span></span>
* <span data-ttu-id="24d1f-436">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="24d1f-436">section:section0:key:key1</span></span>
* <span data-ttu-id="24d1f-437">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="24d1f-437">section:section1:key:key0</span></span>
* <span data-ttu-id="24d1f-438">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="24d1f-438">section:section1:key:key1</span></span>

<span data-ttu-id="24d1f-439">Mit Attributen können Werte bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="24d1f-439">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="24d1f-440">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="24d1f-440">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="24d1f-441">key:attribute</span><span class="sxs-lookup"><span data-stu-id="24d1f-441">key:attribute</span></span>
* <span data-ttu-id="24d1f-442">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="24d1f-442">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="24d1f-443">Schlüssel-pro-Datei-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="24d1f-443">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="24d1f-444"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> verwendet Verzeichnisdateien als Konfigurations-Schlüssel-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="24d1f-444">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="24d1f-445">Der Schlüssel ist der Dateiname.</span><span class="sxs-lookup"><span data-stu-id="24d1f-445">The key is the file name.</span></span> <span data-ttu-id="24d1f-446">Der Wert enthält den Inhalt der Datei.</span><span class="sxs-lookup"><span data-stu-id="24d1f-446">The value contains the file's contents.</span></span> <span data-ttu-id="24d1f-447">Der Schlüssel-pro-Datei-Konfigurationsanbieter wird in Docker-Hostingszenarien verwendet.</span><span class="sxs-lookup"><span data-stu-id="24d1f-447">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="24d1f-448">Um die Schlüssel-pro-Datei-Konfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="24d1f-448">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="24d1f-449">Der `directoryPath` zu den Dateien muss ein absoluter Pfad sein.</span><span class="sxs-lookup"><span data-stu-id="24d1f-449">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="24d1f-450">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="24d1f-450">Overloads permit specifying:</span></span>

* <span data-ttu-id="24d1f-451">Einen `Action<KeyPerFileConfigurationSource>`-Delegat, der die Quelle konfiguriert</span><span class="sxs-lookup"><span data-stu-id="24d1f-451">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="24d1f-452">Ob das Verzeichnis optional ist; und den Pfad zum Verzeichnis</span><span class="sxs-lookup"><span data-stu-id="24d1f-452">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="24d1f-453">Der doppelte Unterstrich (`__`) wird als Trennzeichen für Konfigurationsschlüssel in Dateinamen verwendet.</span><span class="sxs-lookup"><span data-stu-id="24d1f-453">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="24d1f-454">Der Dateiname `Logging__LogLevel__System` erzeugt z.B. den Konfigurationsschlüssel `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="24d1f-454">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="24d1f-455">Rufen Sie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="24d1f-455">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.SetBasePath(Directory.GetCurrentDirectory());
                var path = Path.Combine(Directory.GetCurrentDirectory(), "path/to/files");
                config.AddKeyPerFile(directoryPath: path, optional: true);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="24d1f-456">Der Basispfad wird mit <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> festgelegt.</span><span class="sxs-lookup"><span data-stu-id="24d1f-456">The base path is set with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="24d1f-457">`SetBasePath` befindet sich im Paket [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.</span><span class="sxs-lookup"><span data-stu-id="24d1f-457">`SetBasePath` is in the [Microsoft.Extensions.Configuration.FileExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.FileExtensions/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="24d1f-458">Rufen Sie beim direkten Erstellen von <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> mit der Konfiguration auf:</span><span class="sxs-lookup"><span data-stu-id="24d1f-458">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var path = Path.Combine(Directory.GetCurrentDirectory(), "path/to/files");
var config = new ConfigurationBuilder()
    .AddKeyPerFile(directoryPath: path, optional: true)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="24d1f-459">Speicherkonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="24d1f-459">Memory Configuration Provider</span></span>

<span data-ttu-id="24d1f-460"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> verwendet eine In-Memory-Sammlung für Konfigurations-Schlüssel-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="24d1f-460">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="24d1f-461">Um die In-Memory-Sammlungskonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="24d1f-461">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="24d1f-462">Der Konfigurationsanbieter kann mit `IEnumerable<KeyValuePair<String,String>>` initialisiert werden.</span><span class="sxs-lookup"><span data-stu-id="24d1f-462">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="24d1f-463">Rufen Sie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="24d1f-463">Call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> when building the host to specify the app's configuration:</span></span>

```csharp
public class Program
{
    public static readonly Dictionary<string, string> _dict = 
        new Dictionary<string, string>
        {
            {"MemoryCollectionKey1", "value1"},
            {"MemoryCollectionKey2", "value2"}
        };

    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                config.AddInMemoryCollection(_dict);
            })
            .UseStartup<Startup>();
}
```

<span data-ttu-id="24d1f-464">Rufen Sie beim direkten Erstellen von <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> mit der Konfiguration auf:</span><span class="sxs-lookup"><span data-stu-id="24d1f-464">When creating a <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> directly, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> with the configuration:</span></span>

```csharp
var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

var config = new ConfigurationBuilder()
    .AddInMemoryCollection(dict)
    .Build();

var host = new WebHostBuilder()
    .UseConfiguration(config)
    .UseKestrel()
    .UseStartup<Startup>();
```

## <a name="getvalue"></a><span data-ttu-id="24d1f-465">GetValue</span><span class="sxs-lookup"><span data-stu-id="24d1f-465">GetValue</span></span>

<span data-ttu-id="24d1f-466">[ConfigurationBinder.GetValue&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahiert einen Wert aus der Konfiguration mit einem angegebenen Schlüssel, und konvertiert ihn in den angegebenen Typ.</span><span class="sxs-lookup"><span data-stu-id="24d1f-466">[ConfigurationBinder.GetValue&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a value from configuration with a specified key and converts it to the specified type.</span></span> <span data-ttu-id="24d1f-467">Eine Überladung erlaubt es Ihnen, einen Standardwert anzugeben, wenn der Schlüssel nicht gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="24d1f-467">An overload permits you to provide a default value if the key isn't found.</span></span>

<span data-ttu-id="24d1f-468">Im Beispiel unten geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="24d1f-468">The following example:</span></span>

* <span data-ttu-id="24d1f-469">Der Zeichenfolgenwert aus der Konfiguration wird mit dem Schlüssel `NumberKey` extrahiert.</span><span class="sxs-lookup"><span data-stu-id="24d1f-469">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="24d1f-470">Wenn `NumberKey` nicht in den Konfigurationsschlüsseln gefunden wird, wird der Standardwert `99` verwendet.</span><span class="sxs-lookup"><span data-stu-id="24d1f-470">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="24d1f-471">Der Typ für den Wert wird als `int` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="24d1f-471">Types the value as an `int`.</span></span>
* <span data-ttu-id="24d1f-472">Der Wert wird in der `NumberConfig`-Eigenschaft für die Verwendung durch die Seite gespeichert.</span><span class="sxs-lookup"><span data-stu-id="24d1f-472">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
// using Microsoft.Extensions.Configuration;

public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="24d1f-473">GetSection, GetChildren und Exists</span><span class="sxs-lookup"><span data-stu-id="24d1f-473">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="24d1f-474">Betrachten Sie für die folgenden Beispiele die JSON-Datei unten.</span><span class="sxs-lookup"><span data-stu-id="24d1f-474">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="24d1f-475">Vier Schlüssel befinden sich in zwei Abschnitten, von denen einer ein Paar Unterabschnitte enthält:</span><span class="sxs-lookup"><span data-stu-id="24d1f-475">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

<span data-ttu-id="24d1f-476">Wenn die Datei in die Konfiguration gelesen wird, werden die folgenden eindeutigen hierarchischen Schlüssel erstellt, um die Konfigurationswerte zu enthalten:</span><span class="sxs-lookup"><span data-stu-id="24d1f-476">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="24d1f-477">section0:key0</span><span class="sxs-lookup"><span data-stu-id="24d1f-477">section0:key0</span></span>
* <span data-ttu-id="24d1f-478">section0:key1</span><span class="sxs-lookup"><span data-stu-id="24d1f-478">section0:key1</span></span>
* <span data-ttu-id="24d1f-479">section1:key0</span><span class="sxs-lookup"><span data-stu-id="24d1f-479">section1:key0</span></span>
* <span data-ttu-id="24d1f-480">section1:key1</span><span class="sxs-lookup"><span data-stu-id="24d1f-480">section1:key1</span></span>
* <span data-ttu-id="24d1f-481">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="24d1f-481">section2:subsection0:key0</span></span>
* <span data-ttu-id="24d1f-482">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="24d1f-482">section2:subsection0:key1</span></span>
* <span data-ttu-id="24d1f-483">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="24d1f-483">section2:subsection1:key0</span></span>
* <span data-ttu-id="24d1f-484">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="24d1f-484">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="24d1f-485">GetSection</span><span class="sxs-lookup"><span data-stu-id="24d1f-485">GetSection</span></span>

<span data-ttu-id="24d1f-486">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrahiert einen Konfigurationsunterabschnitt mit dem angegebenen Unterabschnittsschlüssel.</span><span class="sxs-lookup"><span data-stu-id="24d1f-486">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span> <span data-ttu-id="24d1f-487">`GetSection` befindet sich im Paket [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.</span><span class="sxs-lookup"><span data-stu-id="24d1f-487">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="24d1f-488">Um ein <xref:Microsoft.Extensions.Configuration.IConfigurationSection>-Element zurückzugeben, das nur die Schlüssel-Wert-Paare in `section1` enthält, rufen Sie `GetSection` auf, und geben Sie den Abschnittsnamen an:</span><span class="sxs-lookup"><span data-stu-id="24d1f-488">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="24d1f-489">Das Element `configSection` weist keinen Wert auf, nur einen Schlüssel und einen Pfad.</span><span class="sxs-lookup"><span data-stu-id="24d1f-489">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="24d1f-490">Um die Werte für Schlüssel in `section2:subsection0` zu erhalten, rufen Sie `GetSection` auf, und geben Sie den Abschnittspfad an:</span><span class="sxs-lookup"><span data-stu-id="24d1f-490">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="24d1f-491">`GetSection` gibt nie `null` zurück.</span><span class="sxs-lookup"><span data-stu-id="24d1f-491">`GetSection` never returns `null`.</span></span> <span data-ttu-id="24d1f-492">Wenn kein entsprechender Abschnitt gefunden wird, wird ein leeres `IConfigurationSection`-Element zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="24d1f-492">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="24d1f-493">Wenn `GetSection` einen entsprechenden Abschnitt zurückgibt, wird <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> nicht aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="24d1f-493">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="24d1f-494">Eine Eigenschaft <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> und <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> werden zurückgegeben, wenn der Abschnitt vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="24d1f-494">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="24d1f-495">GetChildren</span><span class="sxs-lookup"><span data-stu-id="24d1f-495">GetChildren</span></span>

<span data-ttu-id="24d1f-496">Ein Aufruf von [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) auf `section2` erhält `IEnumerable<IConfigurationSection>` mit folgenden Elementen:</span><span class="sxs-lookup"><span data-stu-id="24d1f-496">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="24d1f-497">Vorhanden</span><span class="sxs-lookup"><span data-stu-id="24d1f-497">Exists</span></span>

<span data-ttu-id="24d1f-498">Verwenden Sie [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) um zu bestimmen, ob ein Konfigurationsabschnitt vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="24d1f-498">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="24d1f-499">Im Falle der Beispieldaten ist `sectionExists` `false`, weil in den Konfigurationsdaten kein Abschnitt `section2:subsection2` vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="24d1f-499">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="24d1f-500">Binden an eine Klasse</span><span class="sxs-lookup"><span data-stu-id="24d1f-500">Bind to a class</span></span>

<span data-ttu-id="24d1f-501">Die Konfiguration kann mit *Optionsmustern* an Klassen gebunden werden, die Gruppen von verwandten Einstellungen darstellen.</span><span class="sxs-lookup"><span data-stu-id="24d1f-501">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="24d1f-502">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="24d1f-502">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="24d1f-503">Konfigurationswerte werden als Zeichenfolgen zurückgegeben, doch das Aufrufen von <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> ermöglicht die Erstellung von [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object)-Objekten.</span><span class="sxs-lookup"><span data-stu-id="24d1f-503">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span> <span data-ttu-id="24d1f-504">`Bind` befindet sich im Paket [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.</span><span class="sxs-lookup"><span data-stu-id="24d1f-504">`Bind` is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="24d1f-505">Die Beispiel-App enthält ein `Starship`-Modell (*Models/Starship.cs*):</span><span class="sxs-lookup"><span data-stu-id="24d1f-505">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

<span data-ttu-id="24d1f-506">Der Abschnitt `starship` der Datei *starship.json* erstellt die Konfiguration, wenn die Beispiel-App den JSON-Konfigurationsanbieter zum Laden der Konfiguration verwendet:</span><span class="sxs-lookup"><span data-stu-id="24d1f-506">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

<span data-ttu-id="24d1f-507">Die folgenden Schlüssel-Wert-Paare werden für die Konfiguration erstellt:</span><span class="sxs-lookup"><span data-stu-id="24d1f-507">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="24d1f-508">Key</span><span class="sxs-lookup"><span data-stu-id="24d1f-508">Key</span></span>                   | <span data-ttu-id="24d1f-509">Wert</span><span class="sxs-lookup"><span data-stu-id="24d1f-509">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="24d1f-510">starship:name</span><span class="sxs-lookup"><span data-stu-id="24d1f-510">starship:name</span></span>         | <span data-ttu-id="24d1f-511">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="24d1f-511">USS Enterprise</span></span>                                    |
| <span data-ttu-id="24d1f-512">starship:registry</span><span class="sxs-lookup"><span data-stu-id="24d1f-512">starship:registry</span></span>     | <span data-ttu-id="24d1f-513">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="24d1f-513">NCC-1701</span></span>                                          |
| <span data-ttu-id="24d1f-514">starship:class</span><span class="sxs-lookup"><span data-stu-id="24d1f-514">starship:class</span></span>        | <span data-ttu-id="24d1f-515">Constitution</span><span class="sxs-lookup"><span data-stu-id="24d1f-515">Constitution</span></span>                                      |
| <span data-ttu-id="24d1f-516">starship:length</span><span class="sxs-lookup"><span data-stu-id="24d1f-516">starship:length</span></span>       | <span data-ttu-id="24d1f-517">304,8</span><span class="sxs-lookup"><span data-stu-id="24d1f-517">304.8</span></span>                                             |
| <span data-ttu-id="24d1f-518">starship:commissioned</span><span class="sxs-lookup"><span data-stu-id="24d1f-518">starship:commissioned</span></span> | <span data-ttu-id="24d1f-519">False</span><span class="sxs-lookup"><span data-stu-id="24d1f-519">False</span></span>                                             |
| <span data-ttu-id="24d1f-520">trademark</span><span class="sxs-lookup"><span data-stu-id="24d1f-520">trademark</span></span>             | <span data-ttu-id="24d1f-521">Paramount Pictures Corp. http://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="24d1f-521">Paramount Pictures Corp. http://www.paramount.com</span></span> |

<span data-ttu-id="24d1f-522">Die Beispiel-App ruft `GetSection` mit dem `starship`-Schlüssel auf.</span><span class="sxs-lookup"><span data-stu-id="24d1f-522">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="24d1f-523">Die `starship`-Schlüssel-Wert-Paaren sind isoliert.</span><span class="sxs-lookup"><span data-stu-id="24d1f-523">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="24d1f-524">Die `Bind`-Methode wird auf dem Unterabschnitt aufgerufen, der in einer Instanz der `Starship`-Klasse übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="24d1f-524">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="24d1f-525">Nach dem Binden der Instanzwerte wird die Instanz einer Eigenschaft zum Rendern zugewiesen:</span><span class="sxs-lookup"><span data-stu-id="24d1f-525">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

<span data-ttu-id="24d1f-526">`GetSection` befindet sich im Paket [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.</span><span class="sxs-lookup"><span data-stu-id="24d1f-526">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="24d1f-527">Binden an ein Objektdiagramm</span><span class="sxs-lookup"><span data-stu-id="24d1f-527">Bind to an object graph</span></span>

<span data-ttu-id="24d1f-528"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> kann ein ganzes POCO-Objektdiagramm binden.</span><span class="sxs-lookup"><span data-stu-id="24d1f-528"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="24d1f-529">`Bind` befindet sich im Paket [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.</span><span class="sxs-lookup"><span data-stu-id="24d1f-529">`Bind` is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="24d1f-530">Das Beispiel enthält ein `TvShow`-Modell, dessen Objektdiagramm die Klassen `Metadata` und `Actors` enthält (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="24d1f-530">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="24d1f-531">Die Beispiel-App verfügt über eine *tvshow.xml*-Datei mit den Konfigurationsdaten:</span><span class="sxs-lookup"><span data-stu-id="24d1f-531">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="24d1f-532">Die Konfiguration wird mit der `Bind`-Methode an das gesamte `TvShow`-Objektdiagramm gebunden.</span><span class="sxs-lookup"><span data-stu-id="24d1f-532">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="24d1f-533">Die gebundene Instanz ist einer Eigenschaft zum Rendern zugewiesen:</span><span class="sxs-lookup"><span data-stu-id="24d1f-533">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="24d1f-534">[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) bindet den angegebenen Typ und gibt ihn zurück.</span><span class="sxs-lookup"><span data-stu-id="24d1f-534">[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="24d1f-535">`Get<T>` ist praktischer als `Bind`.</span><span class="sxs-lookup"><span data-stu-id="24d1f-535">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="24d1f-536">Der folgende Code zeigt die Verwendung von `Get<T>` mit dem vorherigen Beispiel, sodass die gebundene Instanz direkt der Eigenschaft zum Rendern zugewiesen werden kann:</span><span class="sxs-lookup"><span data-stu-id="24d1f-536">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

<span data-ttu-id="24d1f-537"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*> befindet sich im Paket [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.</span><span class="sxs-lookup"><span data-stu-id="24d1f-537"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*> is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="24d1f-538">`Get<T>` ist in ASP.NET Core 1.1 und höher verfügbar.</span><span class="sxs-lookup"><span data-stu-id="24d1f-538">`Get<T>` is available in ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="24d1f-539">`GetSection` befindet sich im Paket [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.</span><span class="sxs-lookup"><span data-stu-id="24d1f-539">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="24d1f-540">Binden eines Arrays an eine Klasse</span><span class="sxs-lookup"><span data-stu-id="24d1f-540">Bind an array to a class</span></span>

<span data-ttu-id="24d1f-541">*Die Beispiel-App veranschaulicht die in diesem Abschnitt erläuterten Konzepte.*</span><span class="sxs-lookup"><span data-stu-id="24d1f-541">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="24d1f-542"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> unterstützt das Binden von Arrays an Objekte mit Arrayindizes in Konfigurationsschlüsseln.</span><span class="sxs-lookup"><span data-stu-id="24d1f-542">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="24d1f-543">Jedes Arrayformat, das ein numerisches Schlüsselsegment (`:0:`, `:1:`, &hellip; `:{n}:`) verfügbar macht, kann ein Array an ein POCO-Klassenarray binden.</span><span class="sxs-lookup"><span data-stu-id="24d1f-543">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span> <span data-ttu-id="24d1f-544">„Bind“ befindet sich im Paket [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.</span><span class="sxs-lookup"><span data-stu-id="24d1f-544">\`Bind\`\` is in the [Microsoft.Extensions.Configuration.Binder](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

> [!NOTE]
> <span data-ttu-id="24d1f-545">Das Binden ist standardmäßig möglich.</span><span class="sxs-lookup"><span data-stu-id="24d1f-545">Binding is provided by convention.</span></span> <span data-ttu-id="24d1f-546">Benutzerdefinierte Konfigurationsanbieter sind nicht erforderlich, um Arraybindung zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="24d1f-546">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="24d1f-547">**In-Memory-Arrayverarbeitung**</span><span class="sxs-lookup"><span data-stu-id="24d1f-547">**In-memory array processing**</span></span>

<span data-ttu-id="24d1f-548">Betrachten Sie die Konfigurationsschlüssel und -werte in der folgenden Tabelle.</span><span class="sxs-lookup"><span data-stu-id="24d1f-548">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="24d1f-549">Key</span><span class="sxs-lookup"><span data-stu-id="24d1f-549">Key</span></span>             | <span data-ttu-id="24d1f-550">Wert</span><span class="sxs-lookup"><span data-stu-id="24d1f-550">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="24d1f-551">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="24d1f-551">array:entries:0</span></span> | <span data-ttu-id="24d1f-552">value0</span><span class="sxs-lookup"><span data-stu-id="24d1f-552">value0</span></span> |
| <span data-ttu-id="24d1f-553">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="24d1f-553">array:entries:1</span></span> | <span data-ttu-id="24d1f-554">value1</span><span class="sxs-lookup"><span data-stu-id="24d1f-554">value1</span></span> |
| <span data-ttu-id="24d1f-555">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="24d1f-555">array:entries:2</span></span> | <span data-ttu-id="24d1f-556">value2</span><span class="sxs-lookup"><span data-stu-id="24d1f-556">value2</span></span> |
| <span data-ttu-id="24d1f-557">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="24d1f-557">array:entries:4</span></span> | <span data-ttu-id="24d1f-558">value4</span><span class="sxs-lookup"><span data-stu-id="24d1f-558">value4</span></span> |
| <span data-ttu-id="24d1f-559">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="24d1f-559">array:entries:5</span></span> | <span data-ttu-id="24d1f-560">value5</span><span class="sxs-lookup"><span data-stu-id="24d1f-560">value5</span></span> |

<span data-ttu-id="24d1f-561">Diese Schlüssel und Werte werden mit dem Speicherkonfigurationsanbieter in die Beispiel-App geladen:</span><span class="sxs-lookup"><span data-stu-id="24d1f-561">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=3-10,22)]

<span data-ttu-id="24d1f-562">Das Array überspringt einen Wert für Index &num;3.</span><span class="sxs-lookup"><span data-stu-id="24d1f-562">The array skips a value for index &num;3.</span></span> <span data-ttu-id="24d1f-563">Der Konfigurationsbinder kann weder NULL-Werte binden noch NULL-Einträge in gebundenen Objekten erstellen. Dies wird deutlich, wenn das Ergebnis der Bindung dieses Arrays an ein Objekt demonstriert wird.</span><span class="sxs-lookup"><span data-stu-id="24d1f-563">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="24d1f-564">In der Beispiel-App ist eine POCO-Klasse verfügbar, um die gebundenen Konfigurationsdaten zu enthalten:</span><span class="sxs-lookup"><span data-stu-id="24d1f-564">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="24d1f-565">Die Konfigurationsdaten werden an das Objekt gebunden:</span><span class="sxs-lookup"><span data-stu-id="24d1f-565">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="24d1f-566">`GetSection` befindet sich im Paket [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/), das sich wiederum im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) befindet.</span><span class="sxs-lookup"><span data-stu-id="24d1f-566">`GetSection` is in the [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="24d1f-567">Die [ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)-Syntax kann auch verwendet werden, wodurch ein kompakterer Code entsteht:</span><span class="sxs-lookup"><span data-stu-id="24d1f-567">[ConfigurationBinder.Get&lt;T&gt;](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="24d1f-568">Das gebundene Objekt, eine Instanz von `ArrayExample`, empfängt die Arraydaten aus der Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="24d1f-568">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="24d1f-569">`ArrayExample.Entries`-Index</span><span class="sxs-lookup"><span data-stu-id="24d1f-569">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="24d1f-570">`ArrayExample.Entries`-Wert</span><span class="sxs-lookup"><span data-stu-id="24d1f-570">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="24d1f-571">0</span><span class="sxs-lookup"><span data-stu-id="24d1f-571">0</span></span>                            | <span data-ttu-id="24d1f-572">value0</span><span class="sxs-lookup"><span data-stu-id="24d1f-572">value0</span></span>                       |
| <span data-ttu-id="24d1f-573">1</span><span class="sxs-lookup"><span data-stu-id="24d1f-573">1</span></span>                            | <span data-ttu-id="24d1f-574">value1</span><span class="sxs-lookup"><span data-stu-id="24d1f-574">value1</span></span>                       |
| <span data-ttu-id="24d1f-575">2</span><span class="sxs-lookup"><span data-stu-id="24d1f-575">2</span></span>                            | <span data-ttu-id="24d1f-576">value2</span><span class="sxs-lookup"><span data-stu-id="24d1f-576">value2</span></span>                       |
| <span data-ttu-id="24d1f-577">3</span><span class="sxs-lookup"><span data-stu-id="24d1f-577">3</span></span>                            | <span data-ttu-id="24d1f-578">value4</span><span class="sxs-lookup"><span data-stu-id="24d1f-578">value4</span></span>                       |
| <span data-ttu-id="24d1f-579">4</span><span class="sxs-lookup"><span data-stu-id="24d1f-579">4</span></span>                            | <span data-ttu-id="24d1f-580">value5</span><span class="sxs-lookup"><span data-stu-id="24d1f-580">value5</span></span>                       |

<span data-ttu-id="24d1f-581">Index &num;3 im gebundenen Objekt enthält die Konfigurationsdaten für den `array:4`-Konfigurationsschlüssel und die Wert für `value4`.</span><span class="sxs-lookup"><span data-stu-id="24d1f-581">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="24d1f-582">Beim Binden von Konfigurationsdaten, die ein Array enthalten, werden die Arrayindizes in den Konfigurationsschlüsseln lediglich zum Durchlaufen der Konfigurationsdaten beim Erstellen des Objekts verwendet.</span><span class="sxs-lookup"><span data-stu-id="24d1f-582">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="24d1f-583">Ein NULL-Wert kann in den Konfigurationsdaten nicht beibehalten werden, und ein NULL-Eintrag wird nicht in einem gebundenen Objekt erstellt, wenn ein Array in Konfigurationsschlüsseln mindestens einen Index überspringt.</span><span class="sxs-lookup"><span data-stu-id="24d1f-583">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="24d1f-584">Das fehlende Konfigurationselement für Index &num;3 kann vor dem Binden an die `ArrayExample`Instanz von jedem Konfigurationsanbieter bereitgestellt werden, der das richtige Schlüssel-Wert-Paar in der Konfiguration erzeugt.</span><span class="sxs-lookup"><span data-stu-id="24d1f-584">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="24d1f-585">Wenn das Beispiel einen zusätzlichen JSON-Konfigurationsanbieter mit dem fehlenden Schlüssel-Wert-Paar enthält, entspricht `ArrayExample.Entries` dem kompletten Konfigurationsarray:</span><span class="sxs-lookup"><span data-stu-id="24d1f-585">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="24d1f-586">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="24d1f-586">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="24d1f-587">In <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span><span class="sxs-lookup"><span data-stu-id="24d1f-587">In <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>:</span></span>

```csharp
config.AddJsonFile("missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="24d1f-588">Das Schlüssel-Wert-Paar in der Tabelle wird in die Konfiguration geladen.</span><span class="sxs-lookup"><span data-stu-id="24d1f-588">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="24d1f-589">Key</span><span class="sxs-lookup"><span data-stu-id="24d1f-589">Key</span></span>             | <span data-ttu-id="24d1f-590">Wert</span><span class="sxs-lookup"><span data-stu-id="24d1f-590">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="24d1f-591">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="24d1f-591">array:entries:3</span></span> | <span data-ttu-id="24d1f-592">value3</span><span class="sxs-lookup"><span data-stu-id="24d1f-592">value3</span></span> |

<span data-ttu-id="24d1f-593">Wenn die `ArrayExample`-Klasseninstanz gebunden wird, nachdem der JSON-Konfigurationsanbieter den Eintrag für Index &num;3 enthält, enthält das `ArrayExample.Entries`-Array den Wert.</span><span class="sxs-lookup"><span data-stu-id="24d1f-593">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="24d1f-594">`ArrayExample.Entries`-Index</span><span class="sxs-lookup"><span data-stu-id="24d1f-594">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="24d1f-595">`ArrayExample.Entries`-Wert</span><span class="sxs-lookup"><span data-stu-id="24d1f-595">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="24d1f-596">0</span><span class="sxs-lookup"><span data-stu-id="24d1f-596">0</span></span>                            | <span data-ttu-id="24d1f-597">value0</span><span class="sxs-lookup"><span data-stu-id="24d1f-597">value0</span></span>                       |
| <span data-ttu-id="24d1f-598">1</span><span class="sxs-lookup"><span data-stu-id="24d1f-598">1</span></span>                            | <span data-ttu-id="24d1f-599">value1</span><span class="sxs-lookup"><span data-stu-id="24d1f-599">value1</span></span>                       |
| <span data-ttu-id="24d1f-600">2</span><span class="sxs-lookup"><span data-stu-id="24d1f-600">2</span></span>                            | <span data-ttu-id="24d1f-601">value2</span><span class="sxs-lookup"><span data-stu-id="24d1f-601">value2</span></span>                       |
| <span data-ttu-id="24d1f-602">3</span><span class="sxs-lookup"><span data-stu-id="24d1f-602">3</span></span>                            | <span data-ttu-id="24d1f-603">value3</span><span class="sxs-lookup"><span data-stu-id="24d1f-603">value3</span></span>                       |
| <span data-ttu-id="24d1f-604">4</span><span class="sxs-lookup"><span data-stu-id="24d1f-604">4</span></span>                            | <span data-ttu-id="24d1f-605">value4</span><span class="sxs-lookup"><span data-stu-id="24d1f-605">value4</span></span>                       |
| <span data-ttu-id="24d1f-606">5</span><span class="sxs-lookup"><span data-stu-id="24d1f-606">5</span></span>                            | <span data-ttu-id="24d1f-607">value5</span><span class="sxs-lookup"><span data-stu-id="24d1f-607">value5</span></span>                       |

<span data-ttu-id="24d1f-608">**JSON-Arrayverarbeitung**</span><span class="sxs-lookup"><span data-stu-id="24d1f-608">**JSON array processing**</span></span>

<span data-ttu-id="24d1f-609">Wenn eine JSON-Datei ein Array enthält, werden Konfigurationsschlüssel für die Arrayelemente mit einem nullbasierten Abschnittsindex erstellt.</span><span class="sxs-lookup"><span data-stu-id="24d1f-609">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="24d1f-610">In der folgenden Konfigurationsdatei ist `subsection` ein Array:</span><span class="sxs-lookup"><span data-stu-id="24d1f-610">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="24d1f-611">Der JSON-Konfigurationsanbieter liest die Konfigurationsdaten in die folgenden Schlüssel-Wert-Paare:</span><span class="sxs-lookup"><span data-stu-id="24d1f-611">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="24d1f-612">Key</span><span class="sxs-lookup"><span data-stu-id="24d1f-612">Key</span></span>                     | <span data-ttu-id="24d1f-613">Wert</span><span class="sxs-lookup"><span data-stu-id="24d1f-613">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="24d1f-614">json_array:key</span><span class="sxs-lookup"><span data-stu-id="24d1f-614">json_array:key</span></span>          | <span data-ttu-id="24d1f-615">valueA</span><span class="sxs-lookup"><span data-stu-id="24d1f-615">valueA</span></span> |
| <span data-ttu-id="24d1f-616">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="24d1f-616">json_array:subsection:0</span></span> | <span data-ttu-id="24d1f-617">valueB</span><span class="sxs-lookup"><span data-stu-id="24d1f-617">valueB</span></span> |
| <span data-ttu-id="24d1f-618">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="24d1f-618">json_array:subsection:1</span></span> | <span data-ttu-id="24d1f-619">valueC</span><span class="sxs-lookup"><span data-stu-id="24d1f-619">valueC</span></span> |
| <span data-ttu-id="24d1f-620">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="24d1f-620">json_array:subsection:2</span></span> | <span data-ttu-id="24d1f-621">valueD</span><span class="sxs-lookup"><span data-stu-id="24d1f-621">valueD</span></span> |

<span data-ttu-id="24d1f-622">In der Beispiel-App können die Konfigurations-Schlüssel-Wert-Paare mit dieser POCO-Klasse gebunden werden:</span><span class="sxs-lookup"><span data-stu-id="24d1f-622">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="24d1f-623">Nach dem Binden enthält `JsonArrayExample.Key` den Wert `valueA`.</span><span class="sxs-lookup"><span data-stu-id="24d1f-623">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="24d1f-624">Die Unterabschnittwerte werden in der POCO-Arrayeigenschaft `Subsection` gespeichert.</span><span class="sxs-lookup"><span data-stu-id="24d1f-624">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="24d1f-625">`JsonArrayExample.Subsection`-Index</span><span class="sxs-lookup"><span data-stu-id="24d1f-625">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="24d1f-626">`JsonArrayExample.Subsection`-Wert</span><span class="sxs-lookup"><span data-stu-id="24d1f-626">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="24d1f-627">0</span><span class="sxs-lookup"><span data-stu-id="24d1f-627">0</span></span>                                   | <span data-ttu-id="24d1f-628">valueB</span><span class="sxs-lookup"><span data-stu-id="24d1f-628">valueB</span></span>                              |
| <span data-ttu-id="24d1f-629">1</span><span class="sxs-lookup"><span data-stu-id="24d1f-629">1</span></span>                                   | <span data-ttu-id="24d1f-630">valueC</span><span class="sxs-lookup"><span data-stu-id="24d1f-630">valueC</span></span>                              |
| <span data-ttu-id="24d1f-631">2</span><span class="sxs-lookup"><span data-stu-id="24d1f-631">2</span></span>                                   | <span data-ttu-id="24d1f-632">valueD</span><span class="sxs-lookup"><span data-stu-id="24d1f-632">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="24d1f-633">Benutzerdefinierter Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="24d1f-633">Custom configuration provider</span></span>

<span data-ttu-id="24d1f-634">Die Beispiel-App veranschaulicht, wie ein Standardkonfigurationsanbieter erstellt wird, der Konfigurations-Schlüssel-Wert-Paare aus einer Datenbank mit [Entity Framework (EF)](/ef/core/) liest.</span><span class="sxs-lookup"><span data-stu-id="24d1f-634">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="24d1f-635">Der Anbieter weist die folgenden Merkmale auf:</span><span class="sxs-lookup"><span data-stu-id="24d1f-635">The provider has the following characteristics:</span></span>

* <span data-ttu-id="24d1f-636">Die EF-In-Memory-Datenbank wird zu Demonstrationszwecken verwendet.</span><span class="sxs-lookup"><span data-stu-id="24d1f-636">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="24d1f-637">Um eine Datenbank zu verwenden, die eine Verbindungszeichenfolge benötigt, implementieren Sie einen sekundären `ConfigurationBuilder`, um die Verbindungszeichenfolge aus einem anderen Konfigurationsanbieter anzugeben.</span><span class="sxs-lookup"><span data-stu-id="24d1f-637">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="24d1f-638">Der Anbieter liest eine Datenbanktabelle beim Start in die Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="24d1f-638">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="24d1f-639">Der Anbieter fragt die Datenbank nicht pro Schlüssel ab.</span><span class="sxs-lookup"><span data-stu-id="24d1f-639">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="24d1f-640">Das erneute Laden bei Änderung ist nicht implementiert. Das heißt, das Aktualisieren der Datenbank nach App-Start hat keine Auswirkungen auf die App-Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="24d1f-640">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="24d1f-641">Definieren Sie eine `EFConfigurationValue`-Entität zum Speichern von Konfigurationswerten in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="24d1f-641">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="24d1f-642">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="24d1f-642">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="24d1f-643">Fügen Sie `EFConfigurationContext` hinzu, um die konfigurierten Werte zu speichern und auf diese zugreifen.</span><span class="sxs-lookup"><span data-stu-id="24d1f-643">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="24d1f-644">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="24d1f-644">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="24d1f-645">Erstellen Sie eine Klasse, die das <xref:Microsoft.Extensions.Configuration.IConfigurationSource> implementiert.</span><span class="sxs-lookup"><span data-stu-id="24d1f-645">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="24d1f-646">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="24d1f-646">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="24d1f-647">Erstellen Sie den benutzerdefinierten Konfigurationsanbieter durch Vererbung von <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="24d1f-647">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="24d1f-648">Der Konfigurationsanbieter initialisiert die Datenbank, wenn diese leer ist.</span><span class="sxs-lookup"><span data-stu-id="24d1f-648">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="24d1f-649">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="24d1f-649">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="24d1f-650">Mit einer `AddEFConfiguration`-Erweiterungsmethode kann die Konfigurationsquelle `ConfigurationBuilder` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="24d1f-650">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="24d1f-651">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="24d1f-651">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="24d1f-652">Der folgende Code veranschaulicht die Verwendung des benutzerdefinierten Anbieters `EFConfigurationProvider` in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="24d1f-652">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=26)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="24d1f-653">Zugreifen auf die Konfiguration während des Starts</span><span class="sxs-lookup"><span data-stu-id="24d1f-653">Access configuration during startup</span></span>

<span data-ttu-id="24d1f-654">Fügen Sie `IConfiguration` in den `Startup`-Konstruktor ein, um auf Konfigurationswerte in `Startup.ConfigureServices` zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="24d1f-654">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="24d1f-655">Um auf die Konfiguration in `Startup.Configure` zuzugreifen, fügen Sie entweder `IConfiguration` direkt in die Methode ein, oder verwenden Sie die Instanz aus dem Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="24d1f-655">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

<span data-ttu-id="24d1f-656">Ein Beispiel für den Zugriff auf die Konfiguration mit den Starthilfsmethoden finden Sie unter [Anwendungsstart: Hilfsmethoden](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="24d1f-656">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="24d1f-657">Zugreifen auf die Konfiguration auf einer Razor Pages-Seite oder in einer MVC-Ansicht</span><span class="sxs-lookup"><span data-stu-id="24d1f-657">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="24d1f-658">Um auf die Konfigurationseinstellungen auf einer Razor Pages-Seite oder in einer MVC-Ansicht zuzugreifen, fügen Sie eine [using-Anweisung](xref:mvc/views/razor#using) ([C#-Referenz: using-Anweisung](/dotnet/csharp/language-reference/keywords/using-directive)) für den [Microsoft.Extensions.Configuration-Namespace ](xref:Microsoft.Extensions.Configuration) hinzu, und fügen Sie <xref:Microsoft.Extensions.Configuration.IConfiguration> auf der Seite bzw. in der Ansicht ein.</span><span class="sxs-lookup"><span data-stu-id="24d1f-658">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="24d1f-659">Auf einer Razor Pages-Seite:</span><span class="sxs-lookup"><span data-stu-id="24d1f-659">In a Razor Pages page:</span></span>

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="24d1f-660">In einer MVC-Ansicht:</span><span class="sxs-lookup"><span data-stu-id="24d1f-660">In an MVC view:</span></span>

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="24d1f-661">Hinzufügen von Konfigurationen aus einer externen Assembly</span><span class="sxs-lookup"><span data-stu-id="24d1f-661">Add configuration from an external assembly</span></span>

<span data-ttu-id="24d1f-662">Eine <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>-Implementierung ermöglicht das Hinzufügen von Erweiterungen zu einer App beim Start von einer externen Assembly außerhalb der `Startup`-Klasse der App aus.</span><span class="sxs-lookup"><span data-stu-id="24d1f-662">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="24d1f-663">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="24d1f-663">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="24d1f-664">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="24d1f-664">Additional resources</span></span>

* <xref:fundamentals/configuration/options>
* <span data-ttu-id="24d1f-665">[Fundierte Einblicke in die Microsoft-Konfiguration](https://www.paraesthesia.com/archive/2018/06/20/microsoft-extensions-configuration-deep-dive/) (in englischer Sprache)</span><span class="sxs-lookup"><span data-stu-id="24d1f-665">[Deep Dive into Microsoft Configuration](https://www.paraesthesia.com/archive/2018/06/20/microsoft-extensions-configuration-deep-dive/)</span></span>
