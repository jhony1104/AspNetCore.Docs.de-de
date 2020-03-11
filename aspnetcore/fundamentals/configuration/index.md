---
title: Konfiguration in ASP.NET Core
author: guardrex
description: In diesem Artikel erfahren Sie, wie Sie mit der Konfigurations-API eine ASP.NET Core-App konfigurieren.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
uid: fundamentals/configuration/index
ms.openlocfilehash: 3dcabae3f76d81e641057c346dbb9097c2da42c7
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78644101"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="5c6f6-103">Konfiguration in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5c6f6-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="5c6f6-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5c6f6-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5c6f6-105">Die App-Konfiguration in ASP.NET Core basiert auf Schlüssel-Wert-Paaren, die von *Konfigurationsanbietern* erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="5c6f6-106">Konfigurationsanbieter lesen Konfigurationsdaten in Schlüssel-Wert-Paare aus verschiedenen Konfigurationsquellen:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="5c6f6-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="5c6f6-107">Azure Key Vault</span></span>
* <span data-ttu-id="5c6f6-108">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="5c6f6-108">Azure App Configuration</span></span>
* <span data-ttu-id="5c6f6-109">Befehlszeilenargumente</span><span class="sxs-lookup"><span data-stu-id="5c6f6-109">Command-line arguments</span></span>
* <span data-ttu-id="5c6f6-110">Benutzerdefinierte Anbieter (installiert oder erstellt)</span><span class="sxs-lookup"><span data-stu-id="5c6f6-110">Custom providers (installed or created)</span></span>
* <span data-ttu-id="5c6f6-111">Verzeichnisdateien</span><span class="sxs-lookup"><span data-stu-id="5c6f6-111">Directory files</span></span>
* <span data-ttu-id="5c6f6-112">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="5c6f6-112">Environment variables</span></span>
* <span data-ttu-id="5c6f6-113">Speicherinterne .NET Objekte</span><span class="sxs-lookup"><span data-stu-id="5c6f6-113">In-memory .NET objects</span></span>
* <span data-ttu-id="5c6f6-114">Einstellungsdateien</span><span class="sxs-lookup"><span data-stu-id="5c6f6-114">Settings files</span></span>

<span data-ttu-id="5c6f6-115">Konfigurationspakete für gängige Konfigurationsanbieterszenarien ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) sind über das Framework implizit enthalten.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-115">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included implicitly by the framework.</span></span>

<span data-ttu-id="5c6f6-116">Codebeispiele, die den <xref:Microsoft.Extensions.Configuration>-Namespace befolgen und in der Beispiel-App verwenden:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-116">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="5c6f6-117">Das *Optionsmuster* ist eine Erweiterung der in diesem Thema beschriebenen Konfigurationskonzepte.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-117">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="5c6f6-118">Optionen verwenden Klassen, um Gruppen von zusammengehörigen Einstellungen darzustellen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-118">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="5c6f6-119">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-119">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="5c6f6-120">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5c6f6-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="5c6f6-121">Hostkonfiguration und App-Konfiguration im Vergleich</span><span class="sxs-lookup"><span data-stu-id="5c6f6-121">Host versus app configuration</span></span>

<span data-ttu-id="5c6f6-122">Bevor die App konfiguriert und gestartet wird, wird ein *Host* konfiguriert und gestartet.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-122">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="5c6f6-123">Der Host ist verantwortlich für das Starten der App und das Verwalten der Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-123">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="5c6f6-124">Die App und der Host werden mit den in diesem Thema beschriebenen Konfigurationsanbietern konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-124">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="5c6f6-125">Schlüssel-Wert-Paare der Hostkonfiguration sind ebenfalls in der globalen App-Konfiguration enthalten.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-125">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="5c6f6-126">Weitere Informationen dazu, wie Konfigurationsanbieter beim Erstellen des Hosts verwendet werden, und wie sich Konfigurationsquellen auf die Hostkonfiguration auswirken, finden Sie unter <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-126">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="5c6f6-127">Sonstige Konfiguration</span><span class="sxs-lookup"><span data-stu-id="5c6f6-127">Other configuration</span></span>

<span data-ttu-id="5c6f6-128">Dieses Thema bezieht sich nur auf *App-Konfigurationen*.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-128">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="5c6f6-129">Andere Aspekte des Ausführens und Hostings von ASP.NET Core-Apps werden mithilfe von Konfigurationsdateien konfiguriert, die in diesem Thema nicht behandelt werden:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-129">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="5c6f6-130">*launch.json*/*launchSettings.json* sind Toolkonfigurationsdateien für die Entwicklungsumgebung, die hier beschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-130">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="5c6f6-131">In <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-131">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="5c6f6-132">In der Dokumentationsreihe, wo die Dateien zum Konfigurieren von ASP.NET Core-Apps für Entwicklungsszenarien verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-132">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="5c6f6-133">*web.config* ist eine Serverkonfigurationsdatei, die in den folgenden Themen beschrieben wird:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-133">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="5c6f6-134">Weitere Informationen zum Migrieren der App-Konfiguration aus früheren Versionen von ASP.NET finden Sie unter <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-134">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="5c6f6-135">Standardkonfiguration</span><span class="sxs-lookup"><span data-stu-id="5c6f6-135">Default configuration</span></span>

<span data-ttu-id="5c6f6-136">Web-Apps, die auf den [dotnet new](/dotnet/core/tools/dotnet-new)-Vorlagen von ASP.NET Core basieren, rufen beim Erstellen eines Hosts <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> auf.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-136">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="5c6f6-137">`CreateDefaultBuilder` legt die Standardkonfiguration für die App in der folgenden Reihenfolge fest:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-137">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="5c6f6-138">Folgendes gilt für Apps, die den [generischen Host](xref:fundamentals/host/generic-host) verwenden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-138">The following applies to apps using the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="5c6f6-139">Ausführliche Informationen zur Standardkonfiguration bei Verwendung des [Webhosts](xref:fundamentals/host/web-host) finden Sie in der [ASP.NET Core 2.2-Version dieses Themas](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-139">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="5c6f6-140">Die Konfiguration des Hosts wird durch Folgendes festgelegt:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-140">Host configuration is provided from:</span></span>
  * <span data-ttu-id="5c6f6-141">Umgebungsvariablen mit dem Präfix `DOTNET_` (zum Beispiel `DOTNET_ENVIRONMENT`) mithilfe des [Umgebungsvariablen-Konfigurationsanbieters](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-141">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="5c6f6-142">Das Präfix (`DOTNET_`) wird beim Laden der Schlüssel-Wert-Paare der Konfiguration entfernt.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-142">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="5c6f6-143">Befehlszeilenargumente mithilfe des [Befehlszeilen-Konfigurationsanbieters](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-143">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="5c6f6-144">Die Webhost-Standardkonfiguration wird eingerichtet (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="5c6f6-144">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="5c6f6-145">Als Webserver wird Kestrel verwendet und mithilfe der Konfigurationsanbieter der App konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-145">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="5c6f6-146">Fügen Sie Middleware zum Filtern von Hosts hinzu.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-146">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="5c6f6-147">Fügen Sie Middleware für weitergeleitete Header hinzu, wenn die Umgebungsvariable `ASPNETCORE_FORWARDEDHEADERS_ENABLED` auf `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-147">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="5c6f6-148">Aktivieren Sie die IIS-Integration.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-148">Enable IIS integration.</span></span>
* <span data-ttu-id="5c6f6-149">Die App-Konfiguration wird durch Folgendes festgelegt:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-149">App configuration is provided from:</span></span>
  * <span data-ttu-id="5c6f6-150">*appsettings.json* mithilfe des [Dateikonfigurationsanbieters](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-150">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="5c6f6-151">*appsettings.{Umgebung}.json* mithilfe des [Dateikonfigurationsanbieters](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-151">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="5c6f6-152">[Geheimnis-Manager](xref:security/app-secrets), wenn die App in der `Development`-Umgebung mit der Einstiegsassembly ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-152">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="5c6f6-153">Umgebungsvariablen mithilfe des [Umgebungsvariablen-Konfigurationsanbieters](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-153">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="5c6f6-154">Befehlszeilenargumente mithilfe des [Befehlszeilen-Konfigurationsanbieters](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-154">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="5c6f6-155">Sicherheit</span><span class="sxs-lookup"><span data-stu-id="5c6f6-155">Security</span></span>

<span data-ttu-id="5c6f6-156">Wenden Sie die folgenden Verfahren an, um vertrauliche Konfigurationsdaten zu schützen:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-156">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="5c6f6-157">Speichern Sie nie Kennwörter oder andere vertrauliche Daten im Konfigurationsanbietercode oder in Nur-Text-Konfigurationsdateien.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-157">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="5c6f6-158">Verwenden Sie keine Produktionsgeheimnisse in Entwicklungs- oder Testumgebungen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-158">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="5c6f6-159">Geben Sie Geheimnisse außerhalb des Projekts an, damit sie nicht versehentlich in ein Quellcoderepository übernommen werden können.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-159">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="5c6f6-160">Weitere Informationen finden Sie unter den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-160">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="5c6f6-161"><xref:security/app-secrets> &ndash; Enthält Hinweise zur Verwendung von Umgebungsvariablen zum Speichern sensibler Daten.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-161"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="5c6f6-162">Der Geheimnis-Manager verwendet den Dateikonfigurationsanbieter, um Benutzergeheimnisse in einer JSON-Datei im lokalen System zu speichern.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-162">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="5c6f6-163">Der Dateikonfigurationsanbieter wird später in diesem Thema beschrieben.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-163">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="5c6f6-164">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) speichert App-Geheimnisse für ASP.NET Core-Apps auf sichere Weise.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-164">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="5c6f6-165">Weitere Informationen finden Sie unter <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-165">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="5c6f6-166">Hierarchische Konfigurationsdaten</span><span class="sxs-lookup"><span data-stu-id="5c6f6-166">Hierarchical configuration data</span></span>

<span data-ttu-id="5c6f6-167">Die Konfigurations-API kann hierarchische Konfigurationsdaten erhalten, indem sie die hierarchischen Daten mit einem Trennzeichen in den Konfigurationsschlüsseln vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-167">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="5c6f6-168">Die folgende JSON-Datei enthält vier Schlüssel in einer strukturierten Hierarchie von zwei Abschnitten:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-168">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="5c6f6-169">Wenn die Datei in die Konfiguration gelesen wird, werden eindeutige Schlüssel erstellt, um die ursprüngliche hierarchische Datenstruktur der Konfigurationsquelle zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-169">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="5c6f6-170">Die Abschnitte und Schlüssel werden mithilfe eines Doppelpunkts (`:`) vereinfacht, um die Originalstruktur zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-170">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="5c6f6-171">section0:key0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-171">section0:key0</span></span>
* <span data-ttu-id="5c6f6-172">section0:key1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-172">section0:key1</span></span>
* <span data-ttu-id="5c6f6-173">section1:key0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-173">section1:key0</span></span>
* <span data-ttu-id="5c6f6-174">section1:key1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-174">section1:key1</span></span>

<span data-ttu-id="5c6f6-175">Mit den Methoden <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> und <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> können Abschnitte und untergeordnete Abschnittelemente in den Konfigurationsdaten isoliert werden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-175"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="5c6f6-176">Diese Methoden werden später im Abschnitt [GetSection, GetChildren und Exists](#getsection-getchildren-and-exists) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-176">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="5c6f6-177">Konventionen</span><span class="sxs-lookup"><span data-stu-id="5c6f6-177">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="5c6f6-178">Quellen und Anbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-178">Sources and providers</span></span>

<span data-ttu-id="5c6f6-179">Beim Starten der Anwendung werden Konfigurationsquellen in der Reihenfolge gelesen, in der ihre Konfigurationsanbieter angegeben sind.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-179">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="5c6f6-180">Konfigurationsanbieter, die Änderungserkennung implementieren, können Konfigurationen erneut laden, wenn zugrunde liegende Einstellungen geändert werden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-180">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="5c6f6-181">Der Dateikonfigurationsanbieter (weiter unten in diesem Thema beschrieben) und der [Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration) implementieren beispielsweise die Änderungserkennung.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-181">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="5c6f6-182"><xref:Microsoft.Extensions.Configuration.IConfiguration> steht im App-Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-182"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="5c6f6-183"><xref:Microsoft.Extensions.Configuration.IConfiguration> kann in eine <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel>-Klasse von Razor Pages oder <xref:Microsoft.AspNetCore.Mvc.Controller>-Klasse von MVC eingefügt werden, um die Konfiguration für die Klasse abzurufen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-183"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="5c6f6-184">In den folgenden Beispielen wird das Feld `_config` verwendet, um auf Konfigurationswerte zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-184">In the following examples, the `_config` field is used to access configuration values:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

<span data-ttu-id="5c6f6-185">Konfigurationsanbieter können die Abhängigkeitsinjektion nicht verwenden, weil sie nicht verfügbar ist, wenn sie vom Host eingerichtet werden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-185">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="5c6f6-186">Tasten</span><span class="sxs-lookup"><span data-stu-id="5c6f6-186">Keys</span></span>

<span data-ttu-id="5c6f6-187">Konfigurationsschlüssel entsprechen den folgenden Konventionen:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-187">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="5c6f6-188">Bei Schlüsseln wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-188">Keys are case-insensitive.</span></span> <span data-ttu-id="5c6f6-189">Beispielsweise verweisen `ConnectionString` und `connectionstring` auf denselben Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-189">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="5c6f6-190">Wenn ein Wert für denselben Schlüssel von denselben oder unterschiedlichen Konfigurationsanbietern festgelegt wird, wird der zuletzt für den Schlüssel bestimmte Wert verwendet.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-190">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="5c6f6-191">Hierarchische Schlüssel</span><span class="sxs-lookup"><span data-stu-id="5c6f6-191">Hierarchical keys</span></span>
  * <span data-ttu-id="5c6f6-192">Innerhalb der Konfigurations-API funktioniert ein Doppelpunkt (`:`) als Trennzeichen auf allen Plattformen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-192">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="5c6f6-193">In Umgebungsvariablen funktioniert ein Doppelpunkt als Trennzeichen ggf. nicht auf allen Plattformen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-193">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="5c6f6-194">Ein doppelter Unterstrich (`__`) wird auf allen Plattformen unterstützt und automatisch in einen Doppelpunkt konvertiert.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-194">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="5c6f6-195">In Azure Key Vault verwenden hierarchische Schlüssel zwei Bindestriche (`--`) als Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-195">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="5c6f6-196">Schreiben Sie Code, um die Bindestriche durch einen Doppelpunkt zu ersetzen, wenn die Geheimnisse in die App-Konfiguration geladen werden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-196">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="5c6f6-197"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> unterstützt das Binden von Arrays an Objekte mit Arrayindizes in Konfigurationsschlüsseln.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-197">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="5c6f6-198">Die Arraybindung wird im Abschnitt [Binden eines Arrays an eine Klasse](#bind-an-array-to-a-class) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-198">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="5c6f6-199">Werte</span><span class="sxs-lookup"><span data-stu-id="5c6f6-199">Values</span></span>

<span data-ttu-id="5c6f6-200">Konfigurationswerte entsprechen den folgenden Konventionen:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-200">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="5c6f6-201">Die Werte sind Zeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-201">Values are strings.</span></span>
* <span data-ttu-id="5c6f6-202">NULL-Werte können nicht in einer Konfiguration gespeichert oder an Objekte gebunden werden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-202">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="5c6f6-203">Anbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-203">Providers</span></span>

<span data-ttu-id="5c6f6-204">Die folgende Tabelle zeigt die für ASP.NET Core-Apps verfügbaren Konfigurationsanbieter.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-204">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="5c6f6-205">Anbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-205">Provider</span></span> | <span data-ttu-id="5c6f6-206">Bereitstellung der Konfiguration über&hellip;</span><span class="sxs-lookup"><span data-stu-id="5c6f6-206">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="5c6f6-207">[Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration) (Thema *Sicherheit*)</span><span class="sxs-lookup"><span data-stu-id="5c6f6-207">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="5c6f6-208">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="5c6f6-208">Azure Key Vault</span></span> |
| <span data-ttu-id="5c6f6-209">[Azure App Configuration-Anbieter](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure-Dokumentation)</span><span class="sxs-lookup"><span data-stu-id="5c6f6-209">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="5c6f6-210">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="5c6f6-210">Azure App Configuration</span></span> |
| [<span data-ttu-id="5c6f6-211">Befehlszeilen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-211">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="5c6f6-212">Befehlszeilenparameter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-212">Command-line parameters</span></span> |
| [<span data-ttu-id="5c6f6-213">Benutzerdefinierter Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-213">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="5c6f6-214">Benutzerdefinierte Quelle</span><span class="sxs-lookup"><span data-stu-id="5c6f6-214">Custom source</span></span> |
| [<span data-ttu-id="5c6f6-215">Umgebungsvariablen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-215">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="5c6f6-216">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="5c6f6-216">Environment variables</span></span> |
| [<span data-ttu-id="5c6f6-217">Dateikonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-217">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="5c6f6-218">Dateien (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="5c6f6-218">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="5c6f6-219">Schlüssel-pro-Datei-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-219">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="5c6f6-220">Verzeichnisdateien</span><span class="sxs-lookup"><span data-stu-id="5c6f6-220">Directory files</span></span> |
| [<span data-ttu-id="5c6f6-221">Speicherkonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-221">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="5c6f6-222">In-Memory-Sammlungen</span><span class="sxs-lookup"><span data-stu-id="5c6f6-222">In-memory collections</span></span> |
| <span data-ttu-id="5c6f6-223">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) (Thema *Sicherheit*)</span><span class="sxs-lookup"><span data-stu-id="5c6f6-223">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="5c6f6-224">Datei im Benutzerprofilverzeichnis</span><span class="sxs-lookup"><span data-stu-id="5c6f6-224">File in the user profile directory</span></span> |

<span data-ttu-id="5c6f6-225">Konfigurationsquellen werden beim Start in der Reihenfolge gelesen, in der ihre Konfigurationsanbieter angegeben sind.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-225">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="5c6f6-226">Die in diesem Thema beschriebenen Konfigurationsanbieter werden in alphabetischer Reihenfolge beschrieben, nicht in der Reihenfolge, in der der Code sie anordnet.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-226">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="5c6f6-227">Ordnen Sie die Konfigurationsanbieter im Code so an, dass sie den Prioritäten für die zugrunde liegenden Konfigurationsquellen entsprechen, die für die App erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-227">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="5c6f6-228">Eine typische Konfigurationsanbietersequenz ist:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-228">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="5c6f6-229">Dateien (*appsettings.json*, *appsettings.{Environment}.json*, wobei `{Environment}` die aktuelle Hostingumgebung der App ist)</span><span class="sxs-lookup"><span data-stu-id="5c6f6-229">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="5c6f6-230">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="5c6f6-230">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="5c6f6-231">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) (nur Entwicklungsumgebung)</span><span class="sxs-lookup"><span data-stu-id="5c6f6-231">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="5c6f6-232">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="5c6f6-232">Environment variables</span></span>
1. <span data-ttu-id="5c6f6-233">Befehlszeilenargumente</span><span class="sxs-lookup"><span data-stu-id="5c6f6-233">Command-line arguments</span></span>

<span data-ttu-id="5c6f6-234">In der Regel werden Befehlszeilen-Konfigurationsanbieter in einer Reihe von Anbietern an letzter Stelle positioniert, damit Befehlszeilenargumente die von anderen Anbietern festgelegte Konfiguration überschreiben können.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-234">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="5c6f6-235">Die vorhergehende Anbieterfolge wird verwendet, wenn Sie einen neuen Host-Generator mit `CreateDefaultBuilder` initialisieren.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-235">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5c6f6-236">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-236">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-configurehostconfiguration"></a><span data-ttu-id="5c6f6-237">Konfigurieren des Host-Generators mit ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="5c6f6-237">Configure the host builder with ConfigureHostConfiguration</span></span>

<span data-ttu-id="5c6f6-238">Um den Host-Generator zu konfigurieren, rufen Sie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> auf und geben die Konfiguration an.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-238">To configure the host builder, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> and supply the configuration.</span></span> <span data-ttu-id="5c6f6-239">`ConfigureHostConfiguration` wird verwendet, um das <xref:Microsoft.Extensions.Hosting.IHostEnvironment> für die spätere Verwendung im Buildprozess zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-239">`ConfigureHostConfiguration` is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> for use later in the build process.</span></span> <span data-ttu-id="5c6f6-240">`ConfigureHostConfiguration` kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-240">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureHostConfiguration(config =>
        {
            var dict = new Dictionary<string, string>
            {
                {"MemoryCollectionKey1", "value1"},
                {"MemoryCollectionKey2", "value2"}
            };

            config.AddInMemoryCollection(dict);
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

## <a name="configureappconfiguration"></a><span data-ttu-id="5c6f6-241">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="5c6f6-241">ConfigureAppConfiguration</span></span>

<span data-ttu-id="5c6f6-242">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfigurationsanbieter der App zusätzlich zu denen anzugeben, die automatisch von `CreateDefaultBuilder` hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-242">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="5c6f6-243">Überschreiben der vorherigen Konfiguration mit Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="5c6f6-243">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="5c6f6-244">Um die App-Konfiguration bereitzustellen, die mit Befehlszeilenargumenten überschrieben werden kann, rufen Sie `AddCommandLine` zuletzt auf:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-244">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="5c6f6-245">Entfernen von durch CreateDefaultBuilder hinzugefügten Anbietern</span><span class="sxs-lookup"><span data-stu-id="5c6f6-245">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="5c6f6-246">Rufen Sie zunächst [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) in [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) auf, um die durch `CreateDefaultBuilder` hinzugefügten Anbieter zu entfernen:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-246">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="5c6f6-247">Verarbeiten der Konfiguration während des App-Starts</span><span class="sxs-lookup"><span data-stu-id="5c6f6-247">Consume configuration during app startup</span></span>

<span data-ttu-id="5c6f6-248">Die für die App in der `ConfigureAppConfiguration`-Methode angegebene Konfiguration, einschließlich `Startup.ConfigureServices`, ist während des Starts der App verfügbar.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-248">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5c6f6-249">Weitere Informationen finden Sie im Abschnitt [Hinzufügen von Konfigurationen aus einer externen Assembly](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-249">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="5c6f6-250">Befehlszeilen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-250">Command-line Configuration Provider</span></span>

<span data-ttu-id="5c6f6-251"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren des Befehlszeilenarguments.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-251">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="5c6f6-252">Um die Befehlszeilenkonfiguration zu aktivieren, wird die <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>-Erweiterungsmethode für eine Instanz von <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-252">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="5c6f6-253">`AddCommandLine` wird automatisch aufgerufen, wenn `CreateDefaultBuilder(string [])` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-253">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="5c6f6-254">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-254">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="5c6f6-255">`CreateDefaultBuilder` lädt außerdem:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-255">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="5c6f6-256">Optionale Konfiguration aus den Dateien *appsettings.json* und *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-256">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="5c6f6-257">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) in der Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-257">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="5c6f6-258">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-258">Environment variables.</span></span>

<span data-ttu-id="5c6f6-259">`CreateDefaultBuilder` fügt den Befehlszeilen-Konfigurationsanbieter zuletzt hinzu.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-259">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="5c6f6-260">Während der Laufzeit übergebene Befehlszeilenargumente überschreiben die von anderen Anbietern festgelegte Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-260">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="5c6f6-261">`CreateDefaultBuilder` wird aktiv, wenn der Host erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-261">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="5c6f6-262">Deswegen kann die durch `CreateDefaultBuilder` aktivierte Befehlszeilenkonfiguration die Konfiguration des Hosts beeinflussen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-262">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="5c6f6-263">Für Apps, die auf den ASP.NET Core-Vorlagen basieren, wurde `AddCommandLine` bereits von `CreateDefaultBuilder` aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-263">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5c6f6-264">Um weitere Konfigurationsanbieter hinzuzufügen und die Möglichkeit einer Überschreibung der Konfiguration dieser Anbieter durch Befehlszeilenargumente beizubehalten, rufen Sie die zusätzlichen Anbieter in `ConfigureAppConfiguration` und zuletzt `AddCommandLine` auf.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-264">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="5c6f6-265">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="5c6f6-265">**Example**</span></span>

<span data-ttu-id="5c6f6-266">Die Beispiel-App nutzt zum Erstellen des Hosts die statische Hilfsmethode `CreateDefaultBuilder`, die einen Aufruf von <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> enthält.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-266">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="5c6f6-267">Öffnen Sie im Projektverzeichnis eine Eingabeaufforderung.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-267">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="5c6f6-268">Geben Sie ein Befehlszeilenargument für den `dotnet run`-Befehl an, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-268">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="5c6f6-269">Wenn die App ausgeführt wird, öffnen Sie einen Browser für die App unter `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-269">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="5c6f6-270">Beachten Sie, dass die Ausgabe das Schlüssel-Wert-Paar für das an `dotnet run` übergebene Konfigurations-Befehlszeilenargument enthält.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-270">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="5c6f6-271">Argumente</span><span class="sxs-lookup"><span data-stu-id="5c6f6-271">Arguments</span></span>

<span data-ttu-id="5c6f6-272">Der Wert muss einem Gleichheitszeichen (`=`) folgen, oder der Schlüssel muss ein Präfix (`--` oder `/`) haben, wenn der Wert einem Leerzeichen folgt.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-272">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="5c6f6-273">Der Wert ist nicht erforderlich, wenn ein Gleichheitszeichen verwendet wird (z. B. `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-273">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="5c6f6-274">Schlüsselpräfix</span><span class="sxs-lookup"><span data-stu-id="5c6f6-274">Key prefix</span></span>               | <span data-ttu-id="5c6f6-275">Beispiel</span><span class="sxs-lookup"><span data-stu-id="5c6f6-275">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="5c6f6-276">Ohne Präfix</span><span class="sxs-lookup"><span data-stu-id="5c6f6-276">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="5c6f6-277">Zwei Gedankenstriche (`--`)</span><span class="sxs-lookup"><span data-stu-id="5c6f6-277">Two dashes (`--`)</span></span>        | <span data-ttu-id="5c6f6-278">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="5c6f6-278">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="5c6f6-279">Schrägstrich (`/`)</span><span class="sxs-lookup"><span data-stu-id="5c6f6-279">Forward slash (`/`)</span></span>      | <span data-ttu-id="5c6f6-280">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="5c6f6-280">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="5c6f6-281">Kombinieren Sie in einem Befehl nicht Schlüssel-Wert-Paare, die ein Gleichheitszeichen verwenden, mit Schlüssel-Wert-Paaren, die ein Leerzeichen verwenden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-281">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="5c6f6-282">Beispielbefehle:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-282">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="5c6f6-283">Switchmappings</span><span class="sxs-lookup"><span data-stu-id="5c6f6-283">Switch mappings</span></span>

<span data-ttu-id="5c6f6-284">Switchmappings erlauben das Angeben einer Logik zum Ersetzen von Schlüsselnamen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-284">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="5c6f6-285">Wenn Sie die Konfiguration manuell mit <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> erstellen, können Sie ein Wörterbuch der Switchersetzungen für die <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>-Methode bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-285">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="5c6f6-286">Wenn das Switchmappingwörterbuch verwendet wird, wird das Wörterbuch für Schlüssel, die dem von einem Befehlszeilenargument angegebenen Schlüssel entsprechen, überprüft.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-286">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="5c6f6-287">Wenn der Befehlszeilenschlüssel im Wörterbuch gefunden wird, wird der Wörterbuchwert (der Schlüsselersatz) zum Festlegen des Schlüssel-Wert-Paares in der App-Konfiguration zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-287">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="5c6f6-288">Ein Switchmapping ist für jeden Befehlszeilenschlüssel erforderlich, dem ein einzelner Gedankenstrich (`-`) vorangestellt ist.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-288">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="5c6f6-289">Regeln für Schlüssel von Switchmappingwörterbüchern:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-289">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="5c6f6-290">Switchmappings müssen mit einem Gedankenstrich (`-`) oder zwei Gedankenstrichen (`--`) beginnen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-290">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="5c6f6-291">Das Switchmappingwörterbuch darf keine doppelten Schlüssel enthalten.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-291">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="5c6f6-292">Erstellen Sie ein Switchmappingwörterbuch.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-292">Create a switch mappings dictionary.</span></span> <span data-ttu-id="5c6f6-293">Im folgenden Beispiel werden zwei Switchmappings erstellt:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-293">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="5c6f6-294">Wenn der Host erstellt wird, rufen Sie `AddCommandLine` mit dem Switchmappingwörterbuch auf:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-294">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="5c6f6-295">Bei Apps, die Switchmappings verwenden, sollten im `CreateDefaultBuilder`-Aufruf keine Argumente übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-295">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="5c6f6-296">Der Aufruf von `CreateDefaultBuilder` der `AddCommandLine`-Methode umfasst keine zugeordneten Switches, und das Switchmappingwörterbuch kann nicht an `CreateDefaultBuilder` übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-296">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5c6f6-297">Die Lösung besteht nicht darin, die Argumente an `CreateDefaultBuilder` zu übergeben, sondern der Methode `AddCommandLine` der `ConfigurationBuilder`-Methode zu erlauben, sowohl die Argumente als auch das Switchmappingwörterbuch zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-297">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="5c6f6-298">Das erstellte Switchmappingwörterbuch enthält die in der folgenden Tabelle gezeigten Daten.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-298">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="5c6f6-299">Key</span><span class="sxs-lookup"><span data-stu-id="5c6f6-299">Key</span></span>       | <span data-ttu-id="5c6f6-300">Wert</span><span class="sxs-lookup"><span data-stu-id="5c6f6-300">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="5c6f6-301">Wenn Switches zugeordnete Schlüssel beim Start der App verwendet werden, erhält die Konfiguration den Konfigurationswert auf dem vom Wörterbuch bereitgestellten Schlüssel:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-301">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="5c6f6-302">Nach dem Ausführen des obigen Befehls enthält die Konfiguration die in der folgenden Tabelle angegebenen Werte.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-302">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="5c6f6-303">Key</span><span class="sxs-lookup"><span data-stu-id="5c6f6-303">Key</span></span>               | <span data-ttu-id="5c6f6-304">Wert</span><span class="sxs-lookup"><span data-stu-id="5c6f6-304">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="5c6f6-305">Umgebungsvariablen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-305">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="5c6f6-306"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-306">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="5c6f6-307">Um die Umgebungsvariablenkonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-307">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="5c6f6-308">[Azure App Service](https://azure.microsoft.com/services/app-service/) ermöglicht Ihnen Umgebungsvariablen im Azure-Portal festzulegen, die die App-Konfiguration mit dem Konfigurationsanbieter für Umgebungsvariablen überschreiben können.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-308">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="5c6f6-309">Weitere Informationen finden Sie unter [Azure-Apps: Überschreiben der App-Konfiguration im Azure-Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-309">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="5c6f6-310">`AddEnvironmentVariables` wird zum Laden von Umgebungsvariablen verwendet, die das Präfix `DOTNET_` für die [Hostkonfiguration](#host-versus-app-configuration) aufweisen, wenn ein neuer Host-Generator mit dem [generischen Host](xref:fundamentals/host/generic-host) initialisiert und `CreateDefaultBuilder` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-310">`AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Generic Host](xref:fundamentals/host/generic-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="5c6f6-311">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-311">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="5c6f6-312">`CreateDefaultBuilder` lädt außerdem:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-312">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="5c6f6-313">App-Konfiguration über Umgebungsvariablen ohne Präfix durch Aufruf von `AddEnvironmentVariables` ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-313">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="5c6f6-314">Optionale Konfiguration aus den Dateien *appsettings.json* und *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-314">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="5c6f6-315">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) in der Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-315">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="5c6f6-316">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="5c6f6-316">Command-line arguments.</span></span>

<span data-ttu-id="5c6f6-317">Der Umgebungsvariablen-Konfigurationsanbieter wird aufgerufen, nachdem die Konfiguration aus Benutzergeheimnissen und *appsettings*-Dateien erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-317">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="5c6f6-318">Das Aufrufen des Anbieters an dieser Stelle ermöglicht den während der Laufzeit gelesenen Umgebungsvariablen, die von Benutzergeheimnissen und *appsettings*-Dateien festgelegte Konfiguration zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-318">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="5c6f6-319">Wenn Sie App-Konfigurationen aus zusätzlichen Umgebungsvariablen bereitstellen müssen, rufen Sie die zusätzlichen Anbieter der App in `ConfigureAppConfiguration` auf, und rufen Sie `AddEnvironmentVariables` mit dem Präfix auf:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-319">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="5c6f6-320">Rufen Sie `AddEnvironmentVariables` zuletzt auf, um Umgebungsvariablen mit dem angegebenen Präfix zuzulassen, um Werte von anderen Anbietern zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-320">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="5c6f6-321">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="5c6f6-321">**Example**</span></span>

<span data-ttu-id="5c6f6-322">Die Beispiel-App nutzt zum Erstellen des Hosts die statische Hilfsmethode `CreateDefaultBuilder`, die einen Aufruf von `AddEnvironmentVariables` enthält.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-322">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="5c6f6-323">Führen Sie die Beispiel-App aus.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-323">Run the sample app.</span></span> <span data-ttu-id="5c6f6-324">Öffnen Sie einen Browser für die App unter `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-324">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="5c6f6-325">Beachten Sie, dass die Ausgabe das Schlüssel-Wert-Paar für die Umgebungsvariable `ENVIRONMENT` enthält.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-325">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="5c6f6-326">Der Wert entspricht der Umgebung, in der die App ausgeführt wird. In der Regel ist das `Development` bei lokaler Ausführung.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-326">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="5c6f6-327">Um die Liste der von der App gerenderten Umgebungsvariablen kurz zu halten, filtert die App Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-327">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="5c6f6-328">Weitere Informationen finden Sie in der Datei *Pages/Index.cshtml.cs* der Beispiel-App.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-328">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="5c6f6-329">Wenn Sie alle für die App verfügbaren Umgebungsvariablen verfügbar machen möchten, ändern Sie `FilteredConfiguration` in *Pages/Index.cshtml.cs* wie folgt:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-329">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="5c6f6-330">Präfixe</span><span class="sxs-lookup"><span data-stu-id="5c6f6-330">Prefixes</span></span>

<span data-ttu-id="5c6f6-331">Umgebungsvariablen, die in die Konfiguration der App geladen werden, werden gefiltert, wenn Sie ein Präfix für die Methode `AddEnvironmentVariables` angeben.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-331">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="5c6f6-332">Um beispielsweise Umgebungsvariablen nach dem Präfix `CUSTOM_` zu filtern, geben Sie das Präfix für den Konfigurationsanbieter an:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-332">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="5c6f6-333">Das Präfix wird beim Erstellen der Schlüssel-Wert-Paare der Konfiguration entfernt.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-333">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="5c6f6-334">Beim Erstellen des Host-Generators wird die Hostkonfiguration durch Umgebungsvariablen bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-334">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="5c6f6-335">Weitere Informationen zu dem Präfix, das für diese Umgebungsvariablen verwendet wird, finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-335">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="5c6f6-336">**Präfixe für Verbindungszeichenfolgen**</span><span class="sxs-lookup"><span data-stu-id="5c6f6-336">**Connection string prefixes**</span></span>

<span data-ttu-id="5c6f6-337">Die Konfigurations-API verfügt über spezielle Verarbeitungsregeln für vier Umgebungsvariablen für Verbindungszeichenfolgen, die beim Konfigurieren von Azure-Verbindungszeichenfolgen für die App-Umgebung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-337">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="5c6f6-338">Umgebungsvariablen mit Präfixen, die in der Tabelle aufgeführt werden, werden in die App geladen, wenn für `AddEnvironmentVariables` kein Präfix angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-338">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="5c6f6-339">Präfix für Verbindungszeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="5c6f6-339">Connection string prefix</span></span> | <span data-ttu-id="5c6f6-340">Anbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-340">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="5c6f6-341">Benutzerdefinierter Anbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-341">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="5c6f6-342">MySQL</span><span class="sxs-lookup"><span data-stu-id="5c6f6-342">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="5c6f6-343">Azure SQL-Datenbank</span><span class="sxs-lookup"><span data-stu-id="5c6f6-343">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="5c6f6-344">SQL Server</span><span class="sxs-lookup"><span data-stu-id="5c6f6-344">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="5c6f6-345">Wenn eine Umgebungsvariable entdeckt und mit einem der vier Präfixe aus der Tabelle in die Konfiguration geladen wird, tritt Folgendes ein:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-345">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="5c6f6-346">Der Konfigurationsschlüssel wird durch Entfernen des Umgebungsvariablenpräfixes und Hinzufügen eines Konfigurationsschlüsselabschnitts (`ConnectionStrings`) erstellt.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-346">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="5c6f6-347">Ein neues Konfigurations-Schlüssel-Wert-Paar wird erstellt, das den Datenbankverbindungsanbieter repräsentiert (mit Ausnahme des Präfixes `CUSTOMCONNSTR_`, für das kein Anbieter angegeben ist).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-347">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="5c6f6-348">Umgebungsvariablenschlüssel</span><span class="sxs-lookup"><span data-stu-id="5c6f6-348">Environment variable key</span></span> | <span data-ttu-id="5c6f6-349">Konvertierter Konfigurationsschlüssel</span><span class="sxs-lookup"><span data-stu-id="5c6f6-349">Converted configuration key</span></span> | <span data-ttu-id="5c6f6-350">Anbieterkonfigurationseintrag</span><span class="sxs-lookup"><span data-stu-id="5c6f6-350">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="5c6f6-351">Konfigurationseintrag wurde nicht erstellt.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-351">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="5c6f6-352">Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-352">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="5c6f6-353">Wert: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="5c6f6-353">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="5c6f6-354">Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-354">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="5c6f6-355">Wert: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="5c6f6-355">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="5c6f6-356">Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-356">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="5c6f6-357">Wert: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="5c6f6-357">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="5c6f6-358">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="5c6f6-358">**Example**</span></span>

<span data-ttu-id="5c6f6-359">Eine benutzerdefinierte Umgebungsvariable einer Verbindungszeichenfolge wird auf dem Server erstellt:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-359">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="5c6f6-360">Name: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="5c6f6-360">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="5c6f6-361">Wert: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="5c6f6-361">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="5c6f6-362">Wenn `IConfiguration` eingefügt und einem Feld namens `_config` zugewiesen wird, sieht der Wert so aus:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-362">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="5c6f6-363">Dateikonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-363">File Configuration Provider</span></span>

<span data-ttu-id="5c6f6-364"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> ist die Basisklasse für das Laden einer Konfiguration aus dem Dateisystem.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-364"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="5c6f6-365">Die folgenden Konfigurationsanbieter sind für bestimmte Dateitypen vorgesehen:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-365">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="5c6f6-366">INI-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-366">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="5c6f6-367">JSON-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-367">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="5c6f6-368">XML-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-368">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="5c6f6-369">INI-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-369">INI Configuration Provider</span></span>

<span data-ttu-id="5c6f6-370"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der INI-Datei.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-370">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="5c6f6-371">Um die INI-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-371">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="5c6f6-372">Der Doppelpunkt kann in einer INI-Dateikonfiguration als Abschnittstrennzeichen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-372">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="5c6f6-373">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-373">Overloads permit specifying:</span></span>

* <span data-ttu-id="5c6f6-374">Ob die Datei optional ist</span><span class="sxs-lookup"><span data-stu-id="5c6f6-374">Whether the file is optional.</span></span>
* <span data-ttu-id="5c6f6-375">Ob die Konfiguration bei Dateiänderungen neu geladen wird</span><span class="sxs-lookup"><span data-stu-id="5c6f6-375">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="5c6f6-376">Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird</span><span class="sxs-lookup"><span data-stu-id="5c6f6-376">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="5c6f6-377">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-377">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="5c6f6-378">Ein allgemeines Beispiel einer INI-Konfigurationsdatei:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-378">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="5c6f6-379">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-379">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="5c6f6-380">section0:key0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-380">section0:key0</span></span>
* <span data-ttu-id="5c6f6-381">section0:key1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-381">section0:key1</span></span>
* <span data-ttu-id="5c6f6-382">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="5c6f6-382">section1:subsection:key</span></span>
* <span data-ttu-id="5c6f6-383">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="5c6f6-383">section2:subsection0:key</span></span>
* <span data-ttu-id="5c6f6-384">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="5c6f6-384">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="5c6f6-385">JSON-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-385">JSON Configuration Provider</span></span>

<span data-ttu-id="5c6f6-386"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der JSON-Datei.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-386">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="5c6f6-387">Um die JSON-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-387">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="5c6f6-388">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-388">Overloads permit specifying:</span></span>

* <span data-ttu-id="5c6f6-389">Ob die Datei optional ist</span><span class="sxs-lookup"><span data-stu-id="5c6f6-389">Whether the file is optional.</span></span>
* <span data-ttu-id="5c6f6-390">Ob die Konfiguration bei Dateiänderungen neu geladen wird</span><span class="sxs-lookup"><span data-stu-id="5c6f6-390">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="5c6f6-391">Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird</span><span class="sxs-lookup"><span data-stu-id="5c6f6-391">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="5c6f6-392">`AddJsonFile` wird automatisch zweimal aufgerufen, wenn Sie einen neuen Host-Generator mit `CreateDefaultBuilder` initialisieren.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-392">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5c6f6-393">Die Methode wird aufgerufen, um die Konfiguration aus folgenden Dateien zu laden:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-393">The method is called to load configuration from:</span></span>

* <span data-ttu-id="5c6f6-394">*appsettings.json* &ndash; Diese Datei wird zuerst gelesen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-394">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="5c6f6-395">Die Umgebungsversion der Datei kann die Werte der Datei *appsettings.json* überschreiben.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-395">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="5c6f6-396">*appsettings.{Environment}.json* &ndash; Die Umgebungsversion der Datei wird basierend auf [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) geladen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-396">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="5c6f6-397">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-397">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="5c6f6-398">`CreateDefaultBuilder` lädt außerdem:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-398">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="5c6f6-399">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-399">Environment variables.</span></span>
* <span data-ttu-id="5c6f6-400">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) in der Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-400">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="5c6f6-401">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="5c6f6-401">Command-line arguments.</span></span>

<span data-ttu-id="5c6f6-402">Der JSON-Konfigurationsanbieter wird zuerst eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-402">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="5c6f6-403">Daher überschreiben Benutzergeheimnisse, Umgebungsvariablen und Befehlszeilenargumente die von *appsettings*-Dateien festgelegte Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-403">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="5c6f6-404">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um Konfiguration der App für andere Dateien als *appsettings.json* und *appsettings.{Environment}.json* anzugeben:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-404">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="5c6f6-405">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="5c6f6-405">**Example**</span></span>

<span data-ttu-id="5c6f6-406">Die Beispiel-App nutzt zum Erstellen des Hosts die statische Hilfsmethode `CreateDefaultBuilder`, die zwei Aufrufe von `AddJsonFile` enthält:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-406">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="5c6f6-407">Der erste Aufruf auf `AddJsonFile` lädt die Konfiguration aus *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-407">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="5c6f6-408">Der zweite Aufruf auf `AddJsonFile` lädt die Konfiguration aus *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-408">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="5c6f6-409">Die folgende Datei wird für *appsettings.Development.json* in der Beispiel-App geladen:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-409">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="5c6f6-410">Führen Sie die Beispiel-App aus.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-410">Run the sample app.</span></span> <span data-ttu-id="5c6f6-411">Öffnen Sie einen Browser für die App unter `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-411">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="5c6f6-412">Die Ausgabe enthält Schlüssel-Wert-Paare für die Konfiguration basierend auf der Umgebung der App.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-412">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="5c6f6-413">Die Protokollebene für den Schlüssel `Logging:LogLevel:Default` ist `Debug`, wenn die App in der Entwicklungsumgebung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-413">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="5c6f6-414">Führen Sie die Beispiel-App noch mal in der Produktionsumgebung aus:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-414">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="5c6f6-415">Öffnen Sie die Datei *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-415">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="5c6f6-416">Ändern Sie im `ConfigurationSample`-Profil den Wert der Umgebungsvariablen `ASPNETCORE_ENVIRONMENT` in `Production`.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-416">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="5c6f6-417">Speichern Sie die Datei, und führen Sie die App mit `dotnet run` in einer Befehlsshell aus.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-417">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="5c6f6-418">Die Einstellungen in der *appsettings.Development.json*-Datei setzen die Einstellungen in der Datei *appsettings.json* nicht länger außer Kraft.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-418">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="5c6f6-419">Die Protokollebene für den Schlüssel `Logging:LogLevel:Default` lautet `Information`.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-419">The log level for the key `Logging:LogLevel:Default` is `Information`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="5c6f6-420">XML-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-420">XML Configuration Provider</span></span>

<span data-ttu-id="5c6f6-421"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der XML-Datei.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-421">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="5c6f6-422">Um die XML-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-422">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="5c6f6-423">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-423">Overloads permit specifying:</span></span>

* <span data-ttu-id="5c6f6-424">Ob die Datei optional ist</span><span class="sxs-lookup"><span data-stu-id="5c6f6-424">Whether the file is optional.</span></span>
* <span data-ttu-id="5c6f6-425">Ob die Konfiguration bei Dateiänderungen neu geladen wird</span><span class="sxs-lookup"><span data-stu-id="5c6f6-425">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="5c6f6-426">Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird</span><span class="sxs-lookup"><span data-stu-id="5c6f6-426">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="5c6f6-427">Der Stammknoten der Konfigurationsdatei wird beim Erstellen der Konfigurations-Schlüssel-Wert-Paare ignoriert.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-427">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="5c6f6-428">Geben Sie keine Dokumenttypdefinition (DTD) und keinen Namespace in der Datei an.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-428">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="5c6f6-429">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-429">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="5c6f6-430">XML-Konfigurationsdateien können unterschiedliche Elementnamen für wiederholte Abschnitte verwenden:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-430">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="5c6f6-431">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-431">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="5c6f6-432">section0:key0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-432">section0:key0</span></span>
* <span data-ttu-id="5c6f6-433">section0:key1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-433">section0:key1</span></span>
* <span data-ttu-id="5c6f6-434">section1:key0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-434">section1:key0</span></span>
* <span data-ttu-id="5c6f6-435">section1:key1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-435">section1:key1</span></span>

<span data-ttu-id="5c6f6-436">Wiederholte Elemente mit den gleichen Elementnamen funktionieren, wenn das `name`-Attribut zur Unterscheidung der Elemente verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-436">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="5c6f6-437">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-437">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="5c6f6-438">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-438">section:section0:key:key0</span></span>
* <span data-ttu-id="5c6f6-439">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-439">section:section0:key:key1</span></span>
* <span data-ttu-id="5c6f6-440">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-440">section:section1:key:key0</span></span>
* <span data-ttu-id="5c6f6-441">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-441">section:section1:key:key1</span></span>

<span data-ttu-id="5c6f6-442">Mit Attributen können Werte bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-442">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="5c6f6-443">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-443">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="5c6f6-444">key:attribute</span><span class="sxs-lookup"><span data-stu-id="5c6f6-444">key:attribute</span></span>
* <span data-ttu-id="5c6f6-445">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="5c6f6-445">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="5c6f6-446">Schlüssel-pro-Datei-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-446">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="5c6f6-447"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> verwendet Verzeichnisdateien als Konfigurations-Schlüssel-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-447">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="5c6f6-448">Der Schlüssel ist der Dateiname.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-448">The key is the file name.</span></span> <span data-ttu-id="5c6f6-449">Der Wert enthält den Inhalt der Datei.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-449">The value contains the file's contents.</span></span> <span data-ttu-id="5c6f6-450">Der Schlüssel-pro-Datei-Konfigurationsanbieter wird in Docker-Hostingszenarien verwendet.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-450">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="5c6f6-451">Um die Schlüssel-pro-Datei-Konfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-451">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="5c6f6-452">Der `directoryPath` zu den Dateien muss ein absoluter Pfad sein.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-452">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="5c6f6-453">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-453">Overloads permit specifying:</span></span>

* <span data-ttu-id="5c6f6-454">Einen `Action<KeyPerFileConfigurationSource>`-Delegat, der die Quelle konfiguriert</span><span class="sxs-lookup"><span data-stu-id="5c6f6-454">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="5c6f6-455">Ob das Verzeichnis optional ist; und den Pfad zum Verzeichnis</span><span class="sxs-lookup"><span data-stu-id="5c6f6-455">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="5c6f6-456">Der doppelte Unterstrich (`__`) wird als Trennzeichen für Konfigurationsschlüssel in Dateinamen verwendet.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-456">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="5c6f6-457">Der Dateiname `Logging__LogLevel__System` erzeugt z.B. den Konfigurationsschlüssel `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-457">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="5c6f6-458">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-458">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="5c6f6-459">Speicherkonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-459">Memory Configuration Provider</span></span>

<span data-ttu-id="5c6f6-460"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> verwendet eine In-Memory-Sammlung für Konfigurations-Schlüssel-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-460">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="5c6f6-461">Um die In-Memory-Sammlungskonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-461">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="5c6f6-462">Der Konfigurationsanbieter kann mit `IEnumerable<KeyValuePair<String,String>>` initialisiert werden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-462">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="5c6f6-463">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-463">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="5c6f6-464">Im folgenden Beispiel wird ein Konfigurationswörterbuch erstellt:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-464">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="5c6f6-465">Das Wörterbuch wird mit einem Aufruf von `AddInMemoryCollection` verwendet, um die Konfiguration bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-465">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="5c6f6-466">GetValue</span><span class="sxs-lookup"><span data-stu-id="5c6f6-466">GetValue</span></span>

<span data-ttu-id="5c6f6-467">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahiert einen einzelnen Wert aus der Konfiguration mit einem angegebenen Schlüssel und konvertiert ihn in den angegebenen Typ, der kein Auflistungstyp ist.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-467">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="5c6f6-468">Eine Überladung akzeptiert einen Standardwert.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-468">An overload accepts a default value.</span></span>

<span data-ttu-id="5c6f6-469">Im Beispiel unten geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-469">The following example:</span></span>

* <span data-ttu-id="5c6f6-470">Der Zeichenfolgenwert aus der Konfiguration wird mit dem Schlüssel `NumberKey` extrahiert.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-470">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="5c6f6-471">Wenn `NumberKey` nicht in den Konfigurationsschlüsseln gefunden wird, wird der Standardwert `99` verwendet.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-471">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="5c6f6-472">Der Typ für den Wert wird als `int` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-472">Types the value as an `int`.</span></span>
* <span data-ttu-id="5c6f6-473">Der Wert wird in der `NumberConfig`-Eigenschaft für die Verwendung durch die Seite gespeichert.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-473">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="5c6f6-474">GetSection, GetChildren und Exists</span><span class="sxs-lookup"><span data-stu-id="5c6f6-474">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="5c6f6-475">Betrachten Sie für die folgenden Beispiele die JSON-Datei unten.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-475">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="5c6f6-476">Vier Schlüssel befinden sich in zwei Abschnitten, von denen einer ein Paar Unterabschnitte enthält:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-476">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="5c6f6-477">Wenn die Datei in die Konfiguration gelesen wird, werden die folgenden eindeutigen hierarchischen Schlüssel erstellt, um die Konfigurationswerte zu enthalten:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-477">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="5c6f6-478">section0:key0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-478">section0:key0</span></span>
* <span data-ttu-id="5c6f6-479">section0:key1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-479">section0:key1</span></span>
* <span data-ttu-id="5c6f6-480">section1:key0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-480">section1:key0</span></span>
* <span data-ttu-id="5c6f6-481">section1:key1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-481">section1:key1</span></span>
* <span data-ttu-id="5c6f6-482">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-482">section2:subsection0:key0</span></span>
* <span data-ttu-id="5c6f6-483">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-483">section2:subsection0:key1</span></span>
* <span data-ttu-id="5c6f6-484">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-484">section2:subsection1:key0</span></span>
* <span data-ttu-id="5c6f6-485">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-485">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="5c6f6-486">GetSection</span><span class="sxs-lookup"><span data-stu-id="5c6f6-486">GetSection</span></span>

<span data-ttu-id="5c6f6-487">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrahiert einen Konfigurationsunterabschnitt mit dem angegebenen Unterabschnittsschlüssel.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-487">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="5c6f6-488">Um ein <xref:Microsoft.Extensions.Configuration.IConfigurationSection>-Element zurückzugeben, das nur die Schlüssel-Wert-Paare in `section1` enthält, rufen Sie `GetSection` auf, und geben Sie den Abschnittsnamen an:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-488">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="5c6f6-489">Das Element `configSection` weist keinen Wert auf, nur einen Schlüssel und einen Pfad.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-489">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="5c6f6-490">Um die Werte für Schlüssel in `section2:subsection0` zu erhalten, rufen Sie `GetSection` auf, und geben Sie den Abschnittspfad an:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-490">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="5c6f6-491">`GetSection` gibt nie `null` zurück.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-491">`GetSection` never returns `null`.</span></span> <span data-ttu-id="5c6f6-492">Wenn kein entsprechender Abschnitt gefunden wird, wird ein leeres `IConfigurationSection`-Element zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-492">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="5c6f6-493">Wenn `GetSection` einen entsprechenden Abschnitt zurückgibt, wird <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> nicht aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-493">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="5c6f6-494">Eine Eigenschaft <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> und <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> werden zurückgegeben, wenn der Abschnitt vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-494">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="5c6f6-495">GetChildren</span><span class="sxs-lookup"><span data-stu-id="5c6f6-495">GetChildren</span></span>

<span data-ttu-id="5c6f6-496">Ein Aufruf von [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) auf `section2` erhält `IEnumerable<IConfigurationSection>` mit folgenden Elementen:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-496">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="5c6f6-497">Vorhanden</span><span class="sxs-lookup"><span data-stu-id="5c6f6-497">Exists</span></span>

<span data-ttu-id="5c6f6-498">Verwenden Sie [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) um zu bestimmen, ob ein Konfigurationsabschnitt vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-498">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="5c6f6-499">Im Falle der Beispieldaten ist `sectionExists``false`, weil in den Konfigurationsdaten kein Abschnitt `section2:subsection2` vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-499">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="5c6f6-500">Binden an eine Klasse</span><span class="sxs-lookup"><span data-stu-id="5c6f6-500">Bind to a class</span></span>

<span data-ttu-id="5c6f6-501">Die Konfiguration kann mit *Optionsmustern* an Klassen gebunden werden, die Gruppen von verwandten Einstellungen darstellen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-501">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="5c6f6-502">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-502">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="5c6f6-503">Konfigurationswerte werden als Zeichenfolgen zurückgegeben, doch das Aufrufen von <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> ermöglicht die Erstellung von [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object)-Objekten.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-503">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span> <span data-ttu-id="5c6f6-504">Der Binder bindet Werte an alle öffentlichen Lese-/Schreibeigenschaften des angegebenen Typs.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-504">The binder binds values to all of the public read/write properties of the type provided.</span></span> <span data-ttu-id="5c6f6-505">Felder werden **nicht** gebunden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-505">Fields are **not** bound.</span></span>

<span data-ttu-id="5c6f6-506">Die Beispiel-App enthält ein `Starship`-Modell (*Models/Starship.cs*):</span><span class="sxs-lookup"><span data-stu-id="5c6f6-506">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

<span data-ttu-id="5c6f6-507">Der Abschnitt `starship` der Datei *starship.json* erstellt die Konfiguration, wenn die Beispiel-App den JSON-Konfigurationsanbieter zum Laden der Konfiguration verwendet:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-507">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

[!code-json[](index/samples/3.x/ConfigurationSample/starship.json)]

<span data-ttu-id="5c6f6-508">Die folgenden Schlüssel-Wert-Paare werden für die Konfiguration erstellt:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-508">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="5c6f6-509">Key</span><span class="sxs-lookup"><span data-stu-id="5c6f6-509">Key</span></span>                   | <span data-ttu-id="5c6f6-510">Wert</span><span class="sxs-lookup"><span data-stu-id="5c6f6-510">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="5c6f6-511">starship:name</span><span class="sxs-lookup"><span data-stu-id="5c6f6-511">starship:name</span></span>         | <span data-ttu-id="5c6f6-512">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="5c6f6-512">USS Enterprise</span></span>                                    |
| <span data-ttu-id="5c6f6-513">starship:registry</span><span class="sxs-lookup"><span data-stu-id="5c6f6-513">starship:registry</span></span>     | <span data-ttu-id="5c6f6-514">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="5c6f6-514">NCC-1701</span></span>                                          |
| <span data-ttu-id="5c6f6-515">starship:class</span><span class="sxs-lookup"><span data-stu-id="5c6f6-515">starship:class</span></span>        | <span data-ttu-id="5c6f6-516">Constitution</span><span class="sxs-lookup"><span data-stu-id="5c6f6-516">Constitution</span></span>                                      |
| <span data-ttu-id="5c6f6-517">starship:length</span><span class="sxs-lookup"><span data-stu-id="5c6f6-517">starship:length</span></span>       | <span data-ttu-id="5c6f6-518">304,8</span><span class="sxs-lookup"><span data-stu-id="5c6f6-518">304.8</span></span>                                             |
| <span data-ttu-id="5c6f6-519">starship:commissioned</span><span class="sxs-lookup"><span data-stu-id="5c6f6-519">starship:commissioned</span></span> | <span data-ttu-id="5c6f6-520">False</span><span class="sxs-lookup"><span data-stu-id="5c6f6-520">False</span></span>                                             |
| <span data-ttu-id="5c6f6-521">trademark</span><span class="sxs-lookup"><span data-stu-id="5c6f6-521">trademark</span></span>             | <span data-ttu-id="5c6f6-522">Paramount Pictures Corp. https://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="5c6f6-522">Paramount Pictures Corp. https://www.paramount.com</span></span> |

<span data-ttu-id="5c6f6-523">Die Beispiel-App ruft `GetSection` mit dem `starship`-Schlüssel auf.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-523">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="5c6f6-524">Die `starship`-Schlüssel-Wert-Paaren sind isoliert.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-524">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="5c6f6-525">Die `Bind`-Methode wird auf dem Unterabschnitt aufgerufen, der in einer Instanz der `Starship`-Klasse übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-525">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="5c6f6-526">Nach dem Binden der Instanzwerte wird die Instanz einer Eigenschaft zum Rendern zugewiesen:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-526">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="5c6f6-527">Binden an ein Objektdiagramm</span><span class="sxs-lookup"><span data-stu-id="5c6f6-527">Bind to an object graph</span></span>

<span data-ttu-id="5c6f6-528"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> kann ein ganzes POCO-Objektdiagramm binden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-528"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="5c6f6-529">Wie beim Binden eines einfachen Objekts werden nur öffentliche Lese-/Schreibeigenschaften gebunden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-529">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="5c6f6-530">Das Beispiel enthält ein `TvShow`-Modell, dessen Objektdiagramm die Klassen `Metadata` und `Actors` enthält (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="5c6f6-530">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="5c6f6-531">Die Beispiel-App verfügt über eine *tvshow.xml*-Datei mit den Konfigurationsdaten:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-531">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/3.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="5c6f6-532">Die Konfiguration wird mit der `Bind`-Methode an das gesamte `TvShow`-Objektdiagramm gebunden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-532">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="5c6f6-533">Die gebundene Instanz ist einer Eigenschaft zum Rendern zugewiesen:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-533">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="5c6f6-534">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) bindet den angegebenen Typ und gibt ihn zurück.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-534">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="5c6f6-535">`Get<T>` ist praktischer als `Bind`.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-535">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="5c6f6-536">Der folgende Code zeigt die Verwendung von `Get<T>` mit dem vorherigen Beispiel, sodass die gebundene Instanz direkt der Eigenschaft zum Rendern zugewiesen werden kann:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-536">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="5c6f6-537">Binden eines Arrays an eine Klasse</span><span class="sxs-lookup"><span data-stu-id="5c6f6-537">Bind an array to a class</span></span>

<span data-ttu-id="5c6f6-538">*Die Beispiel-App veranschaulicht die in diesem Abschnitt erläuterten Konzepte.*</span><span class="sxs-lookup"><span data-stu-id="5c6f6-538">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="5c6f6-539"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> unterstützt das Binden von Arrays an Objekte mit Arrayindizes in Konfigurationsschlüsseln.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-539">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="5c6f6-540">Jedes Arrayformat, das ein numerisches Schlüsselsegment (`:0:`, `:1:`, &hellip; `:{n}:`) verfügbar macht, kann ein Array an ein POCO-Klassenarray binden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-540">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="5c6f6-541">Das Binden ist standardmäßig möglich.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-541">Binding is provided by convention.</span></span> <span data-ttu-id="5c6f6-542">Benutzerdefinierte Konfigurationsanbieter sind nicht erforderlich, um Arraybindung zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-542">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="5c6f6-543">**In-Memory-Arrayverarbeitung**</span><span class="sxs-lookup"><span data-stu-id="5c6f6-543">**In-memory array processing**</span></span>

<span data-ttu-id="5c6f6-544">Betrachten Sie die Konfigurationsschlüssel und -werte in der folgenden Tabelle.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-544">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="5c6f6-545">Key</span><span class="sxs-lookup"><span data-stu-id="5c6f6-545">Key</span></span>             | <span data-ttu-id="5c6f6-546">Wert</span><span class="sxs-lookup"><span data-stu-id="5c6f6-546">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="5c6f6-547">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-547">array:entries:0</span></span> | <span data-ttu-id="5c6f6-548">value0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-548">value0</span></span> |
| <span data-ttu-id="5c6f6-549">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-549">array:entries:1</span></span> | <span data-ttu-id="5c6f6-550">value1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-550">value1</span></span> |
| <span data-ttu-id="5c6f6-551">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="5c6f6-551">array:entries:2</span></span> | <span data-ttu-id="5c6f6-552">value2</span><span class="sxs-lookup"><span data-stu-id="5c6f6-552">value2</span></span> |
| <span data-ttu-id="5c6f6-553">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="5c6f6-553">array:entries:4</span></span> | <span data-ttu-id="5c6f6-554">value4</span><span class="sxs-lookup"><span data-stu-id="5c6f6-554">value4</span></span> |
| <span data-ttu-id="5c6f6-555">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="5c6f6-555">array:entries:5</span></span> | <span data-ttu-id="5c6f6-556">value5</span><span class="sxs-lookup"><span data-stu-id="5c6f6-556">value5</span></span> |

<span data-ttu-id="5c6f6-557">Diese Schlüssel und Werte werden mit dem Speicherkonfigurationsanbieter in die Beispiel-App geladen:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-557">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="5c6f6-558">Das Array überspringt einen Wert für Index &num;3.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-558">The array skips a value for index &num;3.</span></span> <span data-ttu-id="5c6f6-559">Der Konfigurationsbinder kann weder NULL-Werte binden noch NULL-Einträge in gebundenen Objekten erstellen. Dies wird deutlich, wenn das Ergebnis der Bindung dieses Arrays an ein Objekt demonstriert wird.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-559">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="5c6f6-560">In der Beispiel-App ist eine POCO-Klasse verfügbar, um die gebundenen Konfigurationsdaten zu enthalten:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-560">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="5c6f6-561">Die Konfigurationsdaten werden an das Objekt gebunden:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-561">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="5c6f6-562">Die [ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)-Syntax kann auch verwendet werden, wodurch ein kompakterer Code entsteht:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-562">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="5c6f6-563">Das gebundene Objekt, eine Instanz von `ArrayExample`, empfängt die Arraydaten aus der Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-563">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="5c6f6-564">`ArrayExample.Entries`-Index</span><span class="sxs-lookup"><span data-stu-id="5c6f6-564">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="5c6f6-565">`ArrayExample.Entries`-Wert</span><span class="sxs-lookup"><span data-stu-id="5c6f6-565">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="5c6f6-566">0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-566">0</span></span>                            | <span data-ttu-id="5c6f6-567">value0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-567">value0</span></span>                       |
| <span data-ttu-id="5c6f6-568">1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-568">1</span></span>                            | <span data-ttu-id="5c6f6-569">value1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-569">value1</span></span>                       |
| <span data-ttu-id="5c6f6-570">2</span><span class="sxs-lookup"><span data-stu-id="5c6f6-570">2</span></span>                            | <span data-ttu-id="5c6f6-571">value2</span><span class="sxs-lookup"><span data-stu-id="5c6f6-571">value2</span></span>                       |
| <span data-ttu-id="5c6f6-572">3</span><span class="sxs-lookup"><span data-stu-id="5c6f6-572">3</span></span>                            | <span data-ttu-id="5c6f6-573">value4</span><span class="sxs-lookup"><span data-stu-id="5c6f6-573">value4</span></span>                       |
| <span data-ttu-id="5c6f6-574">4</span><span class="sxs-lookup"><span data-stu-id="5c6f6-574">4</span></span>                            | <span data-ttu-id="5c6f6-575">value5</span><span class="sxs-lookup"><span data-stu-id="5c6f6-575">value5</span></span>                       |

<span data-ttu-id="5c6f6-576">Index &num;3 im gebundenen Objekt enthält die Konfigurationsdaten für den `array:4`-Konfigurationsschlüssel und die Wert für `value4`.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-576">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="5c6f6-577">Beim Binden von Konfigurationsdaten, die ein Array enthalten, werden die Arrayindizes in den Konfigurationsschlüsseln lediglich zum Durchlaufen der Konfigurationsdaten beim Erstellen des Objekts verwendet.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-577">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="5c6f6-578">Ein NULL-Wert kann in den Konfigurationsdaten nicht beibehalten werden, und ein NULL-Eintrag wird nicht in einem gebundenen Objekt erstellt, wenn ein Array in Konfigurationsschlüsseln mindestens einen Index überspringt.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-578">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="5c6f6-579">Das fehlende Konfigurationselement für Index &num;3 kann vor dem Binden an die `ArrayExample`Instanz von jedem Konfigurationsanbieter bereitgestellt werden, der das richtige Schlüssel-Wert-Paar in der Konfiguration erzeugt.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-579">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="5c6f6-580">Wenn das Beispiel einen zusätzlichen JSON-Konfigurationsanbieter mit dem fehlenden Schlüssel-Wert-Paar enthält, entspricht `ArrayExample.Entries` dem kompletten Konfigurationsarray:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-580">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="5c6f6-581">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-581">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="5c6f6-582">In `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-582">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="5c6f6-583">Das Schlüssel-Wert-Paar in der Tabelle wird in die Konfiguration geladen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-583">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="5c6f6-584">Key</span><span class="sxs-lookup"><span data-stu-id="5c6f6-584">Key</span></span>             | <span data-ttu-id="5c6f6-585">Wert</span><span class="sxs-lookup"><span data-stu-id="5c6f6-585">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="5c6f6-586">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="5c6f6-586">array:entries:3</span></span> | <span data-ttu-id="5c6f6-587">value3</span><span class="sxs-lookup"><span data-stu-id="5c6f6-587">value3</span></span> |

<span data-ttu-id="5c6f6-588">Wenn die `ArrayExample`-Klasseninstanz gebunden wird, nachdem der JSON-Konfigurationsanbieter den Eintrag für Index &num;3 enthält, enthält das `ArrayExample.Entries`-Array den Wert.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-588">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="5c6f6-589">`ArrayExample.Entries`-Index</span><span class="sxs-lookup"><span data-stu-id="5c6f6-589">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="5c6f6-590">`ArrayExample.Entries`-Wert</span><span class="sxs-lookup"><span data-stu-id="5c6f6-590">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="5c6f6-591">0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-591">0</span></span>                            | <span data-ttu-id="5c6f6-592">value0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-592">value0</span></span>                       |
| <span data-ttu-id="5c6f6-593">1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-593">1</span></span>                            | <span data-ttu-id="5c6f6-594">value1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-594">value1</span></span>                       |
| <span data-ttu-id="5c6f6-595">2</span><span class="sxs-lookup"><span data-stu-id="5c6f6-595">2</span></span>                            | <span data-ttu-id="5c6f6-596">value2</span><span class="sxs-lookup"><span data-stu-id="5c6f6-596">value2</span></span>                       |
| <span data-ttu-id="5c6f6-597">3</span><span class="sxs-lookup"><span data-stu-id="5c6f6-597">3</span></span>                            | <span data-ttu-id="5c6f6-598">value3</span><span class="sxs-lookup"><span data-stu-id="5c6f6-598">value3</span></span>                       |
| <span data-ttu-id="5c6f6-599">4</span><span class="sxs-lookup"><span data-stu-id="5c6f6-599">4</span></span>                            | <span data-ttu-id="5c6f6-600">value4</span><span class="sxs-lookup"><span data-stu-id="5c6f6-600">value4</span></span>                       |
| <span data-ttu-id="5c6f6-601">5</span><span class="sxs-lookup"><span data-stu-id="5c6f6-601">5</span></span>                            | <span data-ttu-id="5c6f6-602">value5</span><span class="sxs-lookup"><span data-stu-id="5c6f6-602">value5</span></span>                       |

<span data-ttu-id="5c6f6-603">**JSON-Arrayverarbeitung**</span><span class="sxs-lookup"><span data-stu-id="5c6f6-603">**JSON array processing**</span></span>

<span data-ttu-id="5c6f6-604">Wenn eine JSON-Datei ein Array enthält, werden Konfigurationsschlüssel für die Arrayelemente mit einem nullbasierten Abschnittsindex erstellt.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-604">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="5c6f6-605">In der folgenden Konfigurationsdatei ist `subsection` ein Array:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-605">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/3.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="5c6f6-606">Der JSON-Konfigurationsanbieter liest die Konfigurationsdaten in die folgenden Schlüssel-Wert-Paare:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-606">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="5c6f6-607">Key</span><span class="sxs-lookup"><span data-stu-id="5c6f6-607">Key</span></span>                     | <span data-ttu-id="5c6f6-608">Wert</span><span class="sxs-lookup"><span data-stu-id="5c6f6-608">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="5c6f6-609">json_array:key</span><span class="sxs-lookup"><span data-stu-id="5c6f6-609">json_array:key</span></span>          | <span data-ttu-id="5c6f6-610">valueA</span><span class="sxs-lookup"><span data-stu-id="5c6f6-610">valueA</span></span> |
| <span data-ttu-id="5c6f6-611">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-611">json_array:subsection:0</span></span> | <span data-ttu-id="5c6f6-612">valueB</span><span class="sxs-lookup"><span data-stu-id="5c6f6-612">valueB</span></span> |
| <span data-ttu-id="5c6f6-613">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-613">json_array:subsection:1</span></span> | <span data-ttu-id="5c6f6-614">valueC</span><span class="sxs-lookup"><span data-stu-id="5c6f6-614">valueC</span></span> |
| <span data-ttu-id="5c6f6-615">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="5c6f6-615">json_array:subsection:2</span></span> | <span data-ttu-id="5c6f6-616">valueD</span><span class="sxs-lookup"><span data-stu-id="5c6f6-616">valueD</span></span> |

<span data-ttu-id="5c6f6-617">In der Beispiel-App können die Konfigurations-Schlüssel-Wert-Paare mit dieser POCO-Klasse gebunden werden:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-617">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="5c6f6-618">Nach dem Binden enthält `JsonArrayExample.Key` den Wert `valueA`.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-618">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="5c6f6-619">Die Unterabschnittwerte werden in der POCO-Arrayeigenschaft `Subsection` gespeichert.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-619">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="5c6f6-620">`JsonArrayExample.Subsection`-Index</span><span class="sxs-lookup"><span data-stu-id="5c6f6-620">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="5c6f6-621">`JsonArrayExample.Subsection`-Wert</span><span class="sxs-lookup"><span data-stu-id="5c6f6-621">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="5c6f6-622">0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-622">0</span></span>                                   | <span data-ttu-id="5c6f6-623">valueB</span><span class="sxs-lookup"><span data-stu-id="5c6f6-623">valueB</span></span>                              |
| <span data-ttu-id="5c6f6-624">1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-624">1</span></span>                                   | <span data-ttu-id="5c6f6-625">valueC</span><span class="sxs-lookup"><span data-stu-id="5c6f6-625">valueC</span></span>                              |
| <span data-ttu-id="5c6f6-626">2</span><span class="sxs-lookup"><span data-stu-id="5c6f6-626">2</span></span>                                   | <span data-ttu-id="5c6f6-627">valueD</span><span class="sxs-lookup"><span data-stu-id="5c6f6-627">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="5c6f6-628">Benutzerdefinierter Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-628">Custom configuration provider</span></span>

<span data-ttu-id="5c6f6-629">Die Beispiel-App veranschaulicht, wie ein Standardkonfigurationsanbieter erstellt wird, der Konfigurations-Schlüssel-Wert-Paare aus einer Datenbank mit [Entity Framework (EF)](/ef/core/) liest.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-629">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="5c6f6-630">Der Anbieter weist die folgenden Merkmale auf:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-630">The provider has the following characteristics:</span></span>

* <span data-ttu-id="5c6f6-631">Die EF-In-Memory-Datenbank wird zu Demonstrationszwecken verwendet.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-631">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="5c6f6-632">Um eine Datenbank zu verwenden, die eine Verbindungszeichenfolge benötigt, implementieren Sie einen sekundären `ConfigurationBuilder`, um die Verbindungszeichenfolge aus einem anderen Konfigurationsanbieter anzugeben.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-632">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="5c6f6-633">Der Anbieter liest eine Datenbanktabelle beim Start in die Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-633">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="5c6f6-634">Der Anbieter fragt die Datenbank nicht pro Schlüssel ab.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-634">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="5c6f6-635">Das erneute Laden bei Änderung ist nicht implementiert. Das heißt, das Aktualisieren der Datenbank nach App-Start hat keine Auswirkungen auf die App-Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-635">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="5c6f6-636">Definieren Sie eine `EFConfigurationValue`-Entität zum Speichern von Konfigurationswerten in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-636">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="5c6f6-637">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-637">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="5c6f6-638">Fügen Sie `EFConfigurationContext` hinzu, um die konfigurierten Werte zu speichern und auf diese zugreifen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-638">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="5c6f6-639">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-639">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="5c6f6-640">Erstellen Sie eine Klasse, die das <xref:Microsoft.Extensions.Configuration.IConfigurationSource> implementiert.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-640">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="5c6f6-641">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-641">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="5c6f6-642">Erstellen Sie den benutzerdefinierten Konfigurationsanbieter durch Vererbung von <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-642">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="5c6f6-643">Der Konfigurationsanbieter initialisiert die Datenbank, wenn diese leer ist.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-643">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="5c6f6-644">Da [Konfigurationsschlüssel die Groß-/Kleinschreibung nicht beachten](#keys), wird das zum Initialisieren der Datenbank verwendete Wörterbuch mit der Vergleichsfunktion, die die Groß-/Kleinschreibung nicht beachtet, erstellt ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-644">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="5c6f6-645">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-645">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="5c6f6-646">Mit einer `AddEFConfiguration`-Erweiterungsmethode kann die Konfigurationsquelle `ConfigurationBuilder` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-646">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="5c6f6-647">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-647">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="5c6f6-648">Der folgende Code veranschaulicht die Verwendung des benutzerdefinierten Anbieters `EFConfigurationProvider` in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-648">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="5c6f6-649">Zugreifen auf die Konfiguration während des Starts</span><span class="sxs-lookup"><span data-stu-id="5c6f6-649">Access configuration during startup</span></span>

<span data-ttu-id="5c6f6-650">Fügen Sie `IConfiguration` in den `Startup`-Konstruktor ein, um auf Konfigurationswerte in `Startup.ConfigureServices` zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-650">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5c6f6-651">Um auf die Konfiguration in `Startup.Configure` zuzugreifen, fügen Sie entweder `IConfiguration` direkt in die Methode ein, oder verwenden Sie die Instanz aus dem Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-651">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="5c6f6-652">Ein Beispiel für den Zugriff auf die Konfiguration mit den Starthilfsmethoden finden Sie unter [Anwendungsstart: Hilfsmethoden](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-652">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="5c6f6-653">Zugreifen auf die Konfiguration auf einer Razor Pages-Seite oder in einer MVC-Ansicht</span><span class="sxs-lookup"><span data-stu-id="5c6f6-653">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="5c6f6-654">Um auf die Konfigurationseinstellungen auf einer Razor Pages-Seite oder in einer MVC-Ansicht zuzugreifen, fügen Sie eine [using-Anweisung](xref:mvc/views/razor#using) ([C#-Referenz: using-Anweisung](/dotnet/csharp/language-reference/keywords/using-directive)) für den [Microsoft.Extensions.Configuration-Namespace ](xref:Microsoft.Extensions.Configuration) hinzu, und fügen Sie <xref:Microsoft.Extensions.Configuration.IConfiguration> auf der Seite bzw. in der Ansicht ein.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-654">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="5c6f6-655">Auf einer Razor Pages-Seite:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-655">In a Razor Pages page:</span></span>

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

<span data-ttu-id="5c6f6-656">In einer MVC-Ansicht:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-656">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="5c6f6-657">Hinzufügen von Konfigurationen aus einer externen Assembly</span><span class="sxs-lookup"><span data-stu-id="5c6f6-657">Add configuration from an external assembly</span></span>

<span data-ttu-id="5c6f6-658">Eine <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>-Implementierung ermöglicht das Hinzufügen von Erweiterungen zu einer App beim Start von einer externen Assembly außerhalb der `Startup`-Klasse der App aus.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-658">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="5c6f6-659">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-659">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5c6f6-660">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="5c6f6-660">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5c6f6-661">Die App-Konfiguration in ASP.NET Core basiert auf Schlüssel-Wert-Paaren, die von *Konfigurationsanbietern* erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-661">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="5c6f6-662">Konfigurationsanbieter lesen Konfigurationsdaten in Schlüssel-Wert-Paare aus verschiedenen Konfigurationsquellen:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-662">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="5c6f6-663">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="5c6f6-663">Azure Key Vault</span></span>
* <span data-ttu-id="5c6f6-664">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="5c6f6-664">Azure App Configuration</span></span>
* <span data-ttu-id="5c6f6-665">Befehlszeilenargumente</span><span class="sxs-lookup"><span data-stu-id="5c6f6-665">Command-line arguments</span></span>
* <span data-ttu-id="5c6f6-666">Benutzerdefinierte Anbieter (installiert oder erstellt)</span><span class="sxs-lookup"><span data-stu-id="5c6f6-666">Custom providers (installed or created)</span></span>
* <span data-ttu-id="5c6f6-667">Verzeichnisdateien</span><span class="sxs-lookup"><span data-stu-id="5c6f6-667">Directory files</span></span>
* <span data-ttu-id="5c6f6-668">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="5c6f6-668">Environment variables</span></span>
* <span data-ttu-id="5c6f6-669">Speicherinterne .NET Objekte</span><span class="sxs-lookup"><span data-stu-id="5c6f6-669">In-memory .NET objects</span></span>
* <span data-ttu-id="5c6f6-670">Einstellungsdateien</span><span class="sxs-lookup"><span data-stu-id="5c6f6-670">Settings files</span></span>

<span data-ttu-id="5c6f6-671">Konfigurationspakete für gängige Konfigurationsanbieterszenarien ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) sind im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-671">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="5c6f6-672">Codebeispiele, die den <xref:Microsoft.Extensions.Configuration>-Namespace befolgen und in der Beispiel-App verwenden:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-672">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="5c6f6-673">Das *Optionsmuster* ist eine Erweiterung der in diesem Thema beschriebenen Konfigurationskonzepte.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-673">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="5c6f6-674">Optionen verwenden Klassen, um Gruppen von zusammengehörigen Einstellungen darzustellen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-674">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="5c6f6-675">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-675">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="5c6f6-676">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5c6f6-676">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="5c6f6-677">Hostkonfiguration und App-Konfiguration im Vergleich</span><span class="sxs-lookup"><span data-stu-id="5c6f6-677">Host versus app configuration</span></span>

<span data-ttu-id="5c6f6-678">Bevor die App konfiguriert und gestartet wird, wird ein *Host* konfiguriert und gestartet.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-678">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="5c6f6-679">Der Host ist verantwortlich für das Starten der App und das Verwalten der Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-679">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="5c6f6-680">Die App und der Host werden mit den in diesem Thema beschriebenen Konfigurationsanbietern konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-680">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="5c6f6-681">Schlüssel-Wert-Paare der Hostkonfiguration sind ebenfalls in der globalen App-Konfiguration enthalten.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-681">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="5c6f6-682">Weitere Informationen dazu, wie Konfigurationsanbieter beim Erstellen des Hosts verwendet werden, und wie sich Konfigurationsquellen auf die Hostkonfiguration auswirken, finden Sie unter <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-682">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="5c6f6-683">Sonstige Konfiguration</span><span class="sxs-lookup"><span data-stu-id="5c6f6-683">Other configuration</span></span>

<span data-ttu-id="5c6f6-684">Dieses Thema bezieht sich nur auf *App-Konfigurationen*.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-684">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="5c6f6-685">Andere Aspekte des Ausführens und Hostings von ASP.NET Core-Apps werden mithilfe von Konfigurationsdateien konfiguriert, die in diesem Thema nicht behandelt werden:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-685">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="5c6f6-686">*launch.json*/*launchSettings.json* sind Toolkonfigurationsdateien für die Entwicklungsumgebung, die hier beschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-686">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="5c6f6-687">In <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-687">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="5c6f6-688">In der Dokumentationsreihe, wo die Dateien zum Konfigurieren von ASP.NET Core-Apps für Entwicklungsszenarien verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-688">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="5c6f6-689">*web.config* ist eine Serverkonfigurationsdatei, die in den folgenden Themen beschrieben wird:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-689">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="5c6f6-690">Weitere Informationen zum Migrieren der App-Konfiguration aus früheren Versionen von ASP.NET finden Sie unter <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-690">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="5c6f6-691">Standardkonfiguration</span><span class="sxs-lookup"><span data-stu-id="5c6f6-691">Default configuration</span></span>

<span data-ttu-id="5c6f6-692">Web-Apps, die auf den [dotnet new](/dotnet/core/tools/dotnet-new)-Vorlagen von ASP.NET Core basieren, rufen beim Erstellen eines Hosts <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> auf.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-692">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="5c6f6-693">`CreateDefaultBuilder` legt die Standardkonfiguration für die App in der folgenden Reihenfolge fest:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-693">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="5c6f6-694">Folgendes gilt für Apps, die den [Webhost](xref:fundamentals/host/web-host) verwenden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-694">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="5c6f6-695">Ausführliche Informationen zur Standardkonfiguration bei Verwendung des [generischen Hosts](xref:fundamentals/host/generic-host) finden Sie in der [neuesten Version dieses Themas](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-695">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="5c6f6-696">Die Konfiguration des Hosts wird durch Folgendes festgelegt:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-696">Host configuration is provided from:</span></span>
  * <span data-ttu-id="5c6f6-697">Umgebungsvariablen mit dem Präfix `ASPNETCORE_` (zum Beispiel `ASPNETCORE_ENVIRONMENT`) mithilfe des [Umgebungsvariablen-Konfigurationsanbieters](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-697">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="5c6f6-698">Das Präfix (`ASPNETCORE_`) wird beim Laden der Schlüssel-Wert-Paare der Konfiguration entfernt.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-698">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="5c6f6-699">Befehlszeilenargumente mithilfe des [Befehlszeilen-Konfigurationsanbieters](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-699">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="5c6f6-700">Die App-Konfiguration wird durch Folgendes festgelegt:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-700">App configuration is provided from:</span></span>
  * <span data-ttu-id="5c6f6-701">*appsettings.json* mithilfe des [Dateikonfigurationsanbieters](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-701">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="5c6f6-702">*appsettings.{Umgebung}.json* mithilfe des [Dateikonfigurationsanbieters](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-702">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="5c6f6-703">[Geheimnis-Manager](xref:security/app-secrets), wenn die App in der `Development`-Umgebung mit der Einstiegsassembly ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-703">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="5c6f6-704">Umgebungsvariablen mithilfe des [Umgebungsvariablen-Konfigurationsanbieters](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-704">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="5c6f6-705">Befehlszeilenargumente mithilfe des [Befehlszeilen-Konfigurationsanbieters](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-705">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="5c6f6-706">Sicherheit</span><span class="sxs-lookup"><span data-stu-id="5c6f6-706">Security</span></span>

<span data-ttu-id="5c6f6-707">Wenden Sie die folgenden Verfahren an, um vertrauliche Konfigurationsdaten zu schützen:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-707">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="5c6f6-708">Speichern Sie nie Kennwörter oder andere vertrauliche Daten im Konfigurationsanbietercode oder in Nur-Text-Konfigurationsdateien.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-708">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="5c6f6-709">Verwenden Sie keine Produktionsgeheimnisse in Entwicklungs- oder Testumgebungen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-709">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="5c6f6-710">Geben Sie Geheimnisse außerhalb des Projekts an, damit sie nicht versehentlich in ein Quellcoderepository übernommen werden können.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-710">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="5c6f6-711">Weitere Informationen finden Sie unter den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-711">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="5c6f6-712"><xref:security/app-secrets> &ndash; Enthält Hinweise zur Verwendung von Umgebungsvariablen zum Speichern sensibler Daten.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-712"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="5c6f6-713">Der Geheimnis-Manager verwendet den Dateikonfigurationsanbieter, um Benutzergeheimnisse in einer JSON-Datei im lokalen System zu speichern.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-713">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="5c6f6-714">Der Dateikonfigurationsanbieter wird später in diesem Thema beschrieben.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-714">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="5c6f6-715">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) speichert App-Geheimnisse für ASP.NET Core-Apps auf sichere Weise.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-715">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="5c6f6-716">Weitere Informationen finden Sie unter <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-716">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="5c6f6-717">Hierarchische Konfigurationsdaten</span><span class="sxs-lookup"><span data-stu-id="5c6f6-717">Hierarchical configuration data</span></span>

<span data-ttu-id="5c6f6-718">Die Konfigurations-API kann hierarchische Konfigurationsdaten erhalten, indem sie die hierarchischen Daten mit einem Trennzeichen in den Konfigurationsschlüsseln vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-718">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="5c6f6-719">Die folgende JSON-Datei enthält vier Schlüssel in einer strukturierten Hierarchie von zwei Abschnitten:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-719">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="5c6f6-720">Wenn die Datei in die Konfiguration gelesen wird, werden eindeutige Schlüssel erstellt, um die ursprüngliche hierarchische Datenstruktur der Konfigurationsquelle zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-720">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="5c6f6-721">Die Abschnitte und Schlüssel werden mithilfe eines Doppelpunkts (`:`) vereinfacht, um die Originalstruktur zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-721">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="5c6f6-722">section0:key0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-722">section0:key0</span></span>
* <span data-ttu-id="5c6f6-723">section0:key1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-723">section0:key1</span></span>
* <span data-ttu-id="5c6f6-724">section1:key0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-724">section1:key0</span></span>
* <span data-ttu-id="5c6f6-725">section1:key1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-725">section1:key1</span></span>

<span data-ttu-id="5c6f6-726">Mit den Methoden <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> und <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> können Abschnitte und untergeordnete Abschnittelemente in den Konfigurationsdaten isoliert werden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-726"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="5c6f6-727">Diese Methoden werden später im Abschnitt [GetSection, GetChildren und Exists](#getsection-getchildren-and-exists) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-727">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="5c6f6-728">Konventionen</span><span class="sxs-lookup"><span data-stu-id="5c6f6-728">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="5c6f6-729">Quellen und Anbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-729">Sources and providers</span></span>

<span data-ttu-id="5c6f6-730">Beim Starten der Anwendung werden Konfigurationsquellen in der Reihenfolge gelesen, in der ihre Konfigurationsanbieter angegeben sind.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-730">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="5c6f6-731">Konfigurationsanbieter, die Änderungserkennung implementieren, können Konfigurationen erneut laden, wenn zugrunde liegende Einstellungen geändert werden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-731">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="5c6f6-732">Der Dateikonfigurationsanbieter (weiter unten in diesem Thema beschrieben) und der [Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration) implementieren beispielsweise die Änderungserkennung.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-732">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="5c6f6-733"><xref:Microsoft.Extensions.Configuration.IConfiguration> steht im App-Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-733"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="5c6f6-734"><xref:Microsoft.Extensions.Configuration.IConfiguration> kann in eine <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel>-Klasse von Razor Pages oder <xref:Microsoft.AspNetCore.Mvc.Controller>-Klasse von MVC eingefügt werden, um die Konfiguration für die Klasse abzurufen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-734"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="5c6f6-735">In den folgenden Beispielen wird das Feld `_config` verwendet, um auf Konfigurationswerte zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-735">In the following examples, the `_config` field is used to access configuration values:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

<span data-ttu-id="5c6f6-736">Konfigurationsanbieter können die Abhängigkeitsinjektion nicht verwenden, weil sie nicht verfügbar ist, wenn sie vom Host eingerichtet werden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-736">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="5c6f6-737">Tasten</span><span class="sxs-lookup"><span data-stu-id="5c6f6-737">Keys</span></span>

<span data-ttu-id="5c6f6-738">Konfigurationsschlüssel entsprechen den folgenden Konventionen:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-738">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="5c6f6-739">Bei Schlüsseln wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-739">Keys are case-insensitive.</span></span> <span data-ttu-id="5c6f6-740">Beispielsweise verweisen `ConnectionString` und `connectionstring` auf denselben Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-740">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="5c6f6-741">Wenn ein Wert für denselben Schlüssel von denselben oder unterschiedlichen Konfigurationsanbietern festgelegt wird, wird der zuletzt für den Schlüssel bestimmte Wert verwendet.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-741">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="5c6f6-742">Hierarchische Schlüssel</span><span class="sxs-lookup"><span data-stu-id="5c6f6-742">Hierarchical keys</span></span>
  * <span data-ttu-id="5c6f6-743">Innerhalb der Konfigurations-API funktioniert ein Doppelpunkt (`:`) als Trennzeichen auf allen Plattformen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-743">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="5c6f6-744">In Umgebungsvariablen funktioniert ein Doppelpunkt als Trennzeichen ggf. nicht auf allen Plattformen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-744">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="5c6f6-745">Ein doppelter Unterstrich (`__`) wird auf allen Plattformen unterstützt und automatisch in einen Doppelpunkt konvertiert.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-745">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="5c6f6-746">In Azure Key Vault verwenden hierarchische Schlüssel zwei Bindestriche (`--`) als Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-746">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="5c6f6-747">Schreiben Sie Code, um die Bindestriche durch einen Doppelpunkt zu ersetzen, wenn die Geheimnisse in die App-Konfiguration geladen werden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-747">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="5c6f6-748"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> unterstützt das Binden von Arrays an Objekte mit Arrayindizes in Konfigurationsschlüsseln.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-748">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="5c6f6-749">Die Arraybindung wird im Abschnitt [Binden eines Arrays an eine Klasse](#bind-an-array-to-a-class) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-749">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="5c6f6-750">Werte</span><span class="sxs-lookup"><span data-stu-id="5c6f6-750">Values</span></span>

<span data-ttu-id="5c6f6-751">Konfigurationswerte entsprechen den folgenden Konventionen:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-751">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="5c6f6-752">Die Werte sind Zeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-752">Values are strings.</span></span>
* <span data-ttu-id="5c6f6-753">NULL-Werte können nicht in einer Konfiguration gespeichert oder an Objekte gebunden werden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-753">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="5c6f6-754">Anbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-754">Providers</span></span>

<span data-ttu-id="5c6f6-755">Die folgende Tabelle zeigt die für ASP.NET Core-Apps verfügbaren Konfigurationsanbieter.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-755">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="5c6f6-756">Anbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-756">Provider</span></span> | <span data-ttu-id="5c6f6-757">Bereitstellung der Konfiguration über&hellip;</span><span class="sxs-lookup"><span data-stu-id="5c6f6-757">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="5c6f6-758">[Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration) (Thema *Sicherheit*)</span><span class="sxs-lookup"><span data-stu-id="5c6f6-758">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="5c6f6-759">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="5c6f6-759">Azure Key Vault</span></span> |
| <span data-ttu-id="5c6f6-760">[Azure App Configuration-Anbieter](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure-Dokumentation)</span><span class="sxs-lookup"><span data-stu-id="5c6f6-760">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="5c6f6-761">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="5c6f6-761">Azure App Configuration</span></span> |
| [<span data-ttu-id="5c6f6-762">Befehlszeilen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-762">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="5c6f6-763">Befehlszeilenparameter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-763">Command-line parameters</span></span> |
| [<span data-ttu-id="5c6f6-764">Benutzerdefinierter Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-764">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="5c6f6-765">Benutzerdefinierte Quelle</span><span class="sxs-lookup"><span data-stu-id="5c6f6-765">Custom source</span></span> |
| [<span data-ttu-id="5c6f6-766">Umgebungsvariablen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-766">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="5c6f6-767">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="5c6f6-767">Environment variables</span></span> |
| [<span data-ttu-id="5c6f6-768">Dateikonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-768">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="5c6f6-769">Dateien (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="5c6f6-769">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="5c6f6-770">Schlüssel-pro-Datei-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-770">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="5c6f6-771">Verzeichnisdateien</span><span class="sxs-lookup"><span data-stu-id="5c6f6-771">Directory files</span></span> |
| [<span data-ttu-id="5c6f6-772">Speicherkonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-772">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="5c6f6-773">In-Memory-Sammlungen</span><span class="sxs-lookup"><span data-stu-id="5c6f6-773">In-memory collections</span></span> |
| <span data-ttu-id="5c6f6-774">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) (Thema *Sicherheit*)</span><span class="sxs-lookup"><span data-stu-id="5c6f6-774">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="5c6f6-775">Datei im Benutzerprofilverzeichnis</span><span class="sxs-lookup"><span data-stu-id="5c6f6-775">File in the user profile directory</span></span> |

<span data-ttu-id="5c6f6-776">Konfigurationsquellen werden beim Start in der Reihenfolge gelesen, in der ihre Konfigurationsanbieter angegeben sind.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-776">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="5c6f6-777">Die in diesem Thema beschriebenen Konfigurationsanbieter werden in alphabetischer Reihenfolge beschrieben, nicht in der Reihenfolge, in der der Code sie anordnet.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-777">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="5c6f6-778">Ordnen Sie die Konfigurationsanbieter im Code so an, dass sie den Prioritäten für die zugrunde liegenden Konfigurationsquellen entsprechen, die für die App erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-778">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="5c6f6-779">Eine typische Konfigurationsanbietersequenz ist:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-779">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="5c6f6-780">Dateien (*appsettings.json*, *appsettings.{Environment}.json*, wobei `{Environment}` die aktuelle Hostingumgebung der App ist)</span><span class="sxs-lookup"><span data-stu-id="5c6f6-780">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="5c6f6-781">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="5c6f6-781">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="5c6f6-782">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) (nur Entwicklungsumgebung)</span><span class="sxs-lookup"><span data-stu-id="5c6f6-782">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="5c6f6-783">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="5c6f6-783">Environment variables</span></span>
1. <span data-ttu-id="5c6f6-784">Befehlszeilenargumente</span><span class="sxs-lookup"><span data-stu-id="5c6f6-784">Command-line arguments</span></span>

<span data-ttu-id="5c6f6-785">In der Regel werden Befehlszeilen-Konfigurationsanbieter in einer Reihe von Anbietern an letzter Stelle positioniert, damit Befehlszeilenargumente die von anderen Anbietern festgelegte Konfiguration überschreiben können.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-785">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="5c6f6-786">Die vorhergehende Anbieterfolge wird verwendet, wenn Sie einen neuen Host-Generator mit `CreateDefaultBuilder` initialisieren.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-786">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5c6f6-787">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-787">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="5c6f6-788">Konfigurieren des Host-Generators mit UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="5c6f6-788">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="5c6f6-789">Um den Host-Generator zu konfigurieren, rufen Sie <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> mit der Konfiguration für den Host-Generator auf.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-789">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

## <a name="configureappconfiguration"></a><span data-ttu-id="5c6f6-790">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="5c6f6-790">ConfigureAppConfiguration</span></span>

<span data-ttu-id="5c6f6-791">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfigurationsanbieter der App zusätzlich zu denen anzugeben, die automatisch von `CreateDefaultBuilder` hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-791">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="5c6f6-792">Überschreiben der vorherigen Konfiguration mit Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="5c6f6-792">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="5c6f6-793">Um die App-Konfiguration bereitzustellen, die mit Befehlszeilenargumenten überschrieben werden kann, rufen Sie `AddCommandLine` zuletzt auf:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-793">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="5c6f6-794">Entfernen von durch CreateDefaultBuilder hinzugefügten Anbietern</span><span class="sxs-lookup"><span data-stu-id="5c6f6-794">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="5c6f6-795">Rufen Sie zunächst [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) in [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) auf, um die durch `CreateDefaultBuilder` hinzugefügten Anbieter zu entfernen:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-795">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="5c6f6-796">Verarbeiten der Konfiguration während des App-Starts</span><span class="sxs-lookup"><span data-stu-id="5c6f6-796">Consume configuration during app startup</span></span>

<span data-ttu-id="5c6f6-797">Die für die App in der `ConfigureAppConfiguration`-Methode angegebene Konfiguration, einschließlich `Startup.ConfigureServices`, ist während des Starts der App verfügbar.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-797">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5c6f6-798">Weitere Informationen finden Sie im Abschnitt [Hinzufügen von Konfigurationen aus einer externen Assembly](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-798">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="5c6f6-799">Befehlszeilen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-799">Command-line Configuration Provider</span></span>

<span data-ttu-id="5c6f6-800"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren des Befehlszeilenarguments.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-800">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="5c6f6-801">Um die Befehlszeilenkonfiguration zu aktivieren, wird die <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>-Erweiterungsmethode für eine Instanz von <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-801">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="5c6f6-802">`AddCommandLine` wird automatisch aufgerufen, wenn `CreateDefaultBuilder(string [])` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-802">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="5c6f6-803">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-803">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="5c6f6-804">`CreateDefaultBuilder` lädt außerdem:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-804">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="5c6f6-805">Optionale Konfiguration aus den Dateien *appsettings.json* und *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-805">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="5c6f6-806">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) in der Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-806">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="5c6f6-807">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-807">Environment variables.</span></span>

<span data-ttu-id="5c6f6-808">`CreateDefaultBuilder` fügt den Befehlszeilen-Konfigurationsanbieter zuletzt hinzu.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-808">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="5c6f6-809">Während der Laufzeit übergebene Befehlszeilenargumente überschreiben die von anderen Anbietern festgelegte Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-809">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="5c6f6-810">`CreateDefaultBuilder` wird aktiv, wenn der Host erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-810">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="5c6f6-811">Deswegen kann die durch `CreateDefaultBuilder` aktivierte Befehlszeilenkonfiguration die Konfiguration des Hosts beeinflussen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-811">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="5c6f6-812">Für Apps, die auf den ASP.NET Core-Vorlagen basieren, wurde `AddCommandLine` bereits von `CreateDefaultBuilder` aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-812">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5c6f6-813">Um weitere Konfigurationsanbieter hinzuzufügen und die Möglichkeit einer Überschreibung der Konfiguration dieser Anbieter durch Befehlszeilenargumente beizubehalten, rufen Sie die zusätzlichen Anbieter in `ConfigureAppConfiguration` und zuletzt `AddCommandLine` auf.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-813">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="5c6f6-814">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="5c6f6-814">**Example**</span></span>

<span data-ttu-id="5c6f6-815">Die Beispiel-App nutzt zum Erstellen des Hosts die statische Hilfsmethode `CreateDefaultBuilder`, die einen Aufruf von <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> enthält.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-815">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="5c6f6-816">Öffnen Sie im Projektverzeichnis eine Eingabeaufforderung.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-816">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="5c6f6-817">Geben Sie ein Befehlszeilenargument für den `dotnet run`-Befehl an, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-817">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="5c6f6-818">Wenn die App ausgeführt wird, öffnen Sie einen Browser für die App unter `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-818">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="5c6f6-819">Beachten Sie, dass die Ausgabe das Schlüssel-Wert-Paar für das an `dotnet run` übergebene Konfigurations-Befehlszeilenargument enthält.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-819">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="5c6f6-820">Argumente</span><span class="sxs-lookup"><span data-stu-id="5c6f6-820">Arguments</span></span>

<span data-ttu-id="5c6f6-821">Der Wert muss einem Gleichheitszeichen (`=`) folgen, oder der Schlüssel muss ein Präfix (`--` oder `/`) haben, wenn der Wert einem Leerzeichen folgt.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-821">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="5c6f6-822">Der Wert ist nicht erforderlich, wenn ein Gleichheitszeichen verwendet wird (z. B. `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-822">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="5c6f6-823">Schlüsselpräfix</span><span class="sxs-lookup"><span data-stu-id="5c6f6-823">Key prefix</span></span>               | <span data-ttu-id="5c6f6-824">Beispiel</span><span class="sxs-lookup"><span data-stu-id="5c6f6-824">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="5c6f6-825">Ohne Präfix</span><span class="sxs-lookup"><span data-stu-id="5c6f6-825">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="5c6f6-826">Zwei Gedankenstriche (`--`)</span><span class="sxs-lookup"><span data-stu-id="5c6f6-826">Two dashes (`--`)</span></span>        | <span data-ttu-id="5c6f6-827">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="5c6f6-827">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="5c6f6-828">Schrägstrich (`/`)</span><span class="sxs-lookup"><span data-stu-id="5c6f6-828">Forward slash (`/`)</span></span>      | <span data-ttu-id="5c6f6-829">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="5c6f6-829">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="5c6f6-830">Kombinieren Sie in einem Befehl nicht Schlüssel-Wert-Paare, die ein Gleichheitszeichen verwenden, mit Schlüssel-Wert-Paaren, die ein Leerzeichen verwenden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-830">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="5c6f6-831">Beispielbefehle:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-831">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="5c6f6-832">Switchmappings</span><span class="sxs-lookup"><span data-stu-id="5c6f6-832">Switch mappings</span></span>

<span data-ttu-id="5c6f6-833">Switchmappings erlauben das Angeben einer Logik zum Ersetzen von Schlüsselnamen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-833">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="5c6f6-834">Wenn Sie die Konfiguration manuell mit <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> erstellen, können Sie ein Wörterbuch der Switchersetzungen für die <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>-Methode bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-834">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="5c6f6-835">Wenn das Switchmappingwörterbuch verwendet wird, wird das Wörterbuch für Schlüssel, die dem von einem Befehlszeilenargument angegebenen Schlüssel entsprechen, überprüft.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-835">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="5c6f6-836">Wenn der Befehlszeilenschlüssel im Wörterbuch gefunden wird, wird der Wörterbuchwert (der Schlüsselersatz) zum Festlegen des Schlüssel-Wert-Paares in der App-Konfiguration zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-836">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="5c6f6-837">Ein Switchmapping ist für jeden Befehlszeilenschlüssel erforderlich, dem ein einzelner Gedankenstrich (`-`) vorangestellt ist.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-837">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="5c6f6-838">Regeln für Schlüssel von Switchmappingwörterbüchern:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-838">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="5c6f6-839">Switchmappings müssen mit einem Gedankenstrich (`-`) oder zwei Gedankenstrichen (`--`) beginnen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-839">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="5c6f6-840">Das Switchmappingwörterbuch darf keine doppelten Schlüssel enthalten.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-840">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="5c6f6-841">Erstellen Sie ein Switchmappingwörterbuch.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-841">Create a switch mappings dictionary.</span></span> <span data-ttu-id="5c6f6-842">Im folgenden Beispiel werden zwei Switchmappings erstellt:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-842">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="5c6f6-843">Wenn der Host erstellt wird, rufen Sie `AddCommandLine` mit dem Switchmappingwörterbuch auf:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-843">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="5c6f6-844">Bei Apps, die Switchmappings verwenden, sollten im `CreateDefaultBuilder`-Aufruf keine Argumente übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-844">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="5c6f6-845">Der Aufruf von `CreateDefaultBuilder` der `AddCommandLine`-Methode umfasst keine zugeordneten Switches, und das Switchmappingwörterbuch kann nicht an `CreateDefaultBuilder` übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-845">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5c6f6-846">Die Lösung besteht nicht darin, die Argumente an `CreateDefaultBuilder` zu übergeben, sondern der Methode `AddCommandLine` der `ConfigurationBuilder`-Methode zu erlauben, sowohl die Argumente als auch das Switchmappingwörterbuch zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-846">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="5c6f6-847">Das erstellte Switchmappingwörterbuch enthält die in der folgenden Tabelle gezeigten Daten.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-847">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="5c6f6-848">Key</span><span class="sxs-lookup"><span data-stu-id="5c6f6-848">Key</span></span>       | <span data-ttu-id="5c6f6-849">Wert</span><span class="sxs-lookup"><span data-stu-id="5c6f6-849">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="5c6f6-850">Wenn Switches zugeordnete Schlüssel beim Start der App verwendet werden, erhält die Konfiguration den Konfigurationswert auf dem vom Wörterbuch bereitgestellten Schlüssel:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-850">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="5c6f6-851">Nach dem Ausführen des obigen Befehls enthält die Konfiguration die in der folgenden Tabelle angegebenen Werte.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-851">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="5c6f6-852">Key</span><span class="sxs-lookup"><span data-stu-id="5c6f6-852">Key</span></span>               | <span data-ttu-id="5c6f6-853">Wert</span><span class="sxs-lookup"><span data-stu-id="5c6f6-853">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="5c6f6-854">Umgebungsvariablen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-854">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="5c6f6-855"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-855">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="5c6f6-856">Um die Umgebungsvariablenkonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-856">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="5c6f6-857">[Azure App Service](https://azure.microsoft.com/services/app-service/) ermöglicht Ihnen Umgebungsvariablen im Azure-Portal festzulegen, die die App-Konfiguration mit dem Konfigurationsanbieter für Umgebungsvariablen überschreiben können.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-857">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="5c6f6-858">Weitere Informationen finden Sie unter [Azure-Apps: Überschreiben der App-Konfiguration im Azure-Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-858">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="5c6f6-859">`AddEnvironmentVariables` wird zum Laden von Umgebungsvariablen verwendet, die das Präfix `ASPNETCORE_` für die [Hostkonfiguration](#host-versus-app-configuration) aufweisen, wenn ein neuer Host-Generator mit dem [Webhost](xref:fundamentals/host/web-host) initialisiert und `CreateDefaultBuilder` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-859">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="5c6f6-860">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-860">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="5c6f6-861">`CreateDefaultBuilder` lädt außerdem:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-861">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="5c6f6-862">App-Konfiguration über Umgebungsvariablen ohne Präfix durch Aufruf von `AddEnvironmentVariables` ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-862">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="5c6f6-863">Optionale Konfiguration aus den Dateien *appsettings.json* und *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-863">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="5c6f6-864">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) in der Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-864">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="5c6f6-865">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="5c6f6-865">Command-line arguments.</span></span>

<span data-ttu-id="5c6f6-866">Der Umgebungsvariablen-Konfigurationsanbieter wird aufgerufen, nachdem die Konfiguration aus Benutzergeheimnissen und *appsettings*-Dateien erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-866">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="5c6f6-867">Das Aufrufen des Anbieters an dieser Stelle ermöglicht den während der Laufzeit gelesenen Umgebungsvariablen, die von Benutzergeheimnissen und *appsettings*-Dateien festgelegte Konfiguration zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-867">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="5c6f6-868">Wenn Sie App-Konfigurationen aus zusätzlichen Umgebungsvariablen bereitstellen müssen, rufen Sie die zusätzlichen Anbieter der App in `ConfigureAppConfiguration` auf, und rufen Sie `AddEnvironmentVariables` mit dem Präfix auf:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-868">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="5c6f6-869">Rufen Sie `AddEnvironmentVariables` zuletzt auf, um Umgebungsvariablen mit dem angegebenen Präfix zuzulassen, um Werte von anderen Anbietern zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-869">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="5c6f6-870">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="5c6f6-870">**Example**</span></span>

<span data-ttu-id="5c6f6-871">Die Beispiel-App nutzt zum Erstellen des Hosts die statische Hilfsmethode `CreateDefaultBuilder`, die einen Aufruf von `AddEnvironmentVariables` enthält.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-871">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="5c6f6-872">Führen Sie die Beispiel-App aus.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-872">Run the sample app.</span></span> <span data-ttu-id="5c6f6-873">Öffnen Sie einen Browser für die App unter `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-873">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="5c6f6-874">Beachten Sie, dass die Ausgabe das Schlüssel-Wert-Paar für die Umgebungsvariable `ENVIRONMENT` enthält.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-874">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="5c6f6-875">Der Wert entspricht der Umgebung, in der die App ausgeführt wird. In der Regel ist das `Development` bei lokaler Ausführung.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-875">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="5c6f6-876">Um die Liste der von der App gerenderten Umgebungsvariablen kurz zu halten, filtert die App Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-876">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="5c6f6-877">Weitere Informationen finden Sie in der Datei *Pages/Index.cshtml.cs* der Beispiel-App.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-877">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="5c6f6-878">Wenn Sie alle für die App verfügbaren Umgebungsvariablen verfügbar machen möchten, ändern Sie `FilteredConfiguration` in *Pages/Index.cshtml.cs* wie folgt:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-878">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="5c6f6-879">Präfixe</span><span class="sxs-lookup"><span data-stu-id="5c6f6-879">Prefixes</span></span>

<span data-ttu-id="5c6f6-880">Umgebungsvariablen, die in die Konfiguration der App geladen werden, werden gefiltert, wenn Sie ein Präfix für die Methode `AddEnvironmentVariables` angeben.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-880">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="5c6f6-881">Um beispielsweise Umgebungsvariablen nach dem Präfix `CUSTOM_` zu filtern, geben Sie das Präfix für den Konfigurationsanbieter an:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-881">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="5c6f6-882">Das Präfix wird beim Erstellen der Schlüssel-Wert-Paare der Konfiguration entfernt.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-882">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="5c6f6-883">Beim Erstellen des Host-Generators wird die Hostkonfiguration durch Umgebungsvariablen bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-883">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="5c6f6-884">Weitere Informationen zu dem Präfix, das für diese Umgebungsvariablen verwendet wird, finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-884">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="5c6f6-885">**Präfixe für Verbindungszeichenfolgen**</span><span class="sxs-lookup"><span data-stu-id="5c6f6-885">**Connection string prefixes**</span></span>

<span data-ttu-id="5c6f6-886">Die Konfigurations-API verfügt über spezielle Verarbeitungsregeln für vier Umgebungsvariablen für Verbindungszeichenfolgen, die beim Konfigurieren von Azure-Verbindungszeichenfolgen für die App-Umgebung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-886">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="5c6f6-887">Umgebungsvariablen mit Präfixen, die in der Tabelle aufgeführt werden, werden in die App geladen, wenn für `AddEnvironmentVariables` kein Präfix angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-887">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="5c6f6-888">Präfix für Verbindungszeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="5c6f6-888">Connection string prefix</span></span> | <span data-ttu-id="5c6f6-889">Anbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-889">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="5c6f6-890">Benutzerdefinierter Anbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-890">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="5c6f6-891">MySQL</span><span class="sxs-lookup"><span data-stu-id="5c6f6-891">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="5c6f6-892">Azure SQL-Datenbank</span><span class="sxs-lookup"><span data-stu-id="5c6f6-892">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="5c6f6-893">SQL Server</span><span class="sxs-lookup"><span data-stu-id="5c6f6-893">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="5c6f6-894">Wenn eine Umgebungsvariable entdeckt und mit einem der vier Präfixe aus der Tabelle in die Konfiguration geladen wird, tritt Folgendes ein:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-894">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="5c6f6-895">Der Konfigurationsschlüssel wird durch Entfernen des Umgebungsvariablenpräfixes und Hinzufügen eines Konfigurationsschlüsselabschnitts (`ConnectionStrings`) erstellt.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-895">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="5c6f6-896">Ein neues Konfigurations-Schlüssel-Wert-Paar wird erstellt, das den Datenbankverbindungsanbieter repräsentiert (mit Ausnahme des Präfixes `CUSTOMCONNSTR_`, für das kein Anbieter angegeben ist).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-896">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="5c6f6-897">Umgebungsvariablenschlüssel</span><span class="sxs-lookup"><span data-stu-id="5c6f6-897">Environment variable key</span></span> | <span data-ttu-id="5c6f6-898">Konvertierter Konfigurationsschlüssel</span><span class="sxs-lookup"><span data-stu-id="5c6f6-898">Converted configuration key</span></span> | <span data-ttu-id="5c6f6-899">Anbieterkonfigurationseintrag</span><span class="sxs-lookup"><span data-stu-id="5c6f6-899">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="5c6f6-900">Konfigurationseintrag wurde nicht erstellt.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-900">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="5c6f6-901">Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-901">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="5c6f6-902">Wert: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="5c6f6-902">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="5c6f6-903">Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-903">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="5c6f6-904">Wert: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="5c6f6-904">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="5c6f6-905">Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-905">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="5c6f6-906">Wert: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="5c6f6-906">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="5c6f6-907">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="5c6f6-907">**Example**</span></span>

<span data-ttu-id="5c6f6-908">Eine benutzerdefinierte Umgebungsvariable einer Verbindungszeichenfolge wird auf dem Server erstellt:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-908">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="5c6f6-909">Name: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="5c6f6-909">Name &ndash; `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="5c6f6-910">Wert: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="5c6f6-910">Value &ndash; `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="5c6f6-911">Wenn `IConfiguration` eingefügt und einem Feld namens `_config` zugewiesen wird, sieht der Wert so aus:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-911">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="5c6f6-912">Dateikonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-912">File Configuration Provider</span></span>

<span data-ttu-id="5c6f6-913"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> ist die Basisklasse für das Laden einer Konfiguration aus dem Dateisystem.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-913"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="5c6f6-914">Die folgenden Konfigurationsanbieter sind für bestimmte Dateitypen vorgesehen:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-914">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="5c6f6-915">INI-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-915">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="5c6f6-916">JSON-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-916">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="5c6f6-917">XML-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-917">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="5c6f6-918">INI-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-918">INI Configuration Provider</span></span>

<span data-ttu-id="5c6f6-919"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der INI-Datei.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-919">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="5c6f6-920">Um die INI-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-920">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="5c6f6-921">Der Doppelpunkt kann in einer INI-Dateikonfiguration als Abschnittstrennzeichen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-921">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="5c6f6-922">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-922">Overloads permit specifying:</span></span>

* <span data-ttu-id="5c6f6-923">Ob die Datei optional ist</span><span class="sxs-lookup"><span data-stu-id="5c6f6-923">Whether the file is optional.</span></span>
* <span data-ttu-id="5c6f6-924">Ob die Konfiguration bei Dateiänderungen neu geladen wird</span><span class="sxs-lookup"><span data-stu-id="5c6f6-924">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="5c6f6-925">Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird</span><span class="sxs-lookup"><span data-stu-id="5c6f6-925">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="5c6f6-926">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-926">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="5c6f6-927">Ein allgemeines Beispiel einer INI-Konfigurationsdatei:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-927">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="5c6f6-928">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-928">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="5c6f6-929">section0:key0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-929">section0:key0</span></span>
* <span data-ttu-id="5c6f6-930">section0:key1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-930">section0:key1</span></span>
* <span data-ttu-id="5c6f6-931">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="5c6f6-931">section1:subsection:key</span></span>
* <span data-ttu-id="5c6f6-932">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="5c6f6-932">section2:subsection0:key</span></span>
* <span data-ttu-id="5c6f6-933">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="5c6f6-933">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="5c6f6-934">JSON-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-934">JSON Configuration Provider</span></span>

<span data-ttu-id="5c6f6-935"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der JSON-Datei.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-935">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="5c6f6-936">Um die JSON-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-936">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="5c6f6-937">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-937">Overloads permit specifying:</span></span>

* <span data-ttu-id="5c6f6-938">Ob die Datei optional ist</span><span class="sxs-lookup"><span data-stu-id="5c6f6-938">Whether the file is optional.</span></span>
* <span data-ttu-id="5c6f6-939">Ob die Konfiguration bei Dateiänderungen neu geladen wird</span><span class="sxs-lookup"><span data-stu-id="5c6f6-939">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="5c6f6-940">Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird</span><span class="sxs-lookup"><span data-stu-id="5c6f6-940">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="5c6f6-941">`AddJsonFile` wird automatisch zweimal aufgerufen, wenn Sie einen neuen Host-Generator mit `CreateDefaultBuilder` initialisieren.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-941">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5c6f6-942">Die Methode wird aufgerufen, um die Konfiguration aus folgenden Dateien zu laden:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-942">The method is called to load configuration from:</span></span>

* <span data-ttu-id="5c6f6-943">*appsettings.json* &ndash; Diese Datei wird zuerst gelesen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-943">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="5c6f6-944">Die Umgebungsversion der Datei kann die Werte der Datei *appsettings.json* überschreiben.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-944">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="5c6f6-945">*appsettings.{Environment}.json* &ndash; Die Umgebungsversion der Datei wird basierend auf [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) geladen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-945">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="5c6f6-946">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-946">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="5c6f6-947">`CreateDefaultBuilder` lädt außerdem:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-947">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="5c6f6-948">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-948">Environment variables.</span></span>
* <span data-ttu-id="5c6f6-949">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) in der Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-949">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="5c6f6-950">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="5c6f6-950">Command-line arguments.</span></span>

<span data-ttu-id="5c6f6-951">Der JSON-Konfigurationsanbieter wird zuerst eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-951">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="5c6f6-952">Daher überschreiben Benutzergeheimnisse, Umgebungsvariablen und Befehlszeilenargumente die von *appsettings*-Dateien festgelegte Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-952">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="5c6f6-953">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um Konfiguration der App für andere Dateien als *appsettings.json* und *appsettings.{Environment}.json* anzugeben:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-953">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="5c6f6-954">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="5c6f6-954">**Example**</span></span>

<span data-ttu-id="5c6f6-955">Die Beispiel-App nutzt zum Erstellen des Hosts die statische Hilfsmethode `CreateDefaultBuilder`, die zwei Aufrufe von `AddJsonFile` enthält:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-955">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="5c6f6-956">Der erste Aufruf auf `AddJsonFile` lädt die Konfiguration aus *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-956">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="5c6f6-957">Der zweite Aufruf auf `AddJsonFile` lädt die Konfiguration aus *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-957">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="5c6f6-958">Die folgende Datei wird für *appsettings.Development.json* in der Beispiel-App geladen:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-958">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="5c6f6-959">Führen Sie die Beispiel-App aus.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-959">Run the sample app.</span></span> <span data-ttu-id="5c6f6-960">Öffnen Sie einen Browser für die App unter `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-960">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="5c6f6-961">Die Ausgabe enthält Schlüssel-Wert-Paare für die Konfiguration basierend auf der Umgebung der App.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-961">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="5c6f6-962">Die Protokollebene für den Schlüssel `Logging:LogLevel:Default` ist `Debug`, wenn die App in der Entwicklungsumgebung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-962">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="5c6f6-963">Führen Sie die Beispiel-App noch mal in der Produktionsumgebung aus:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-963">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="5c6f6-964">Öffnen Sie die Datei *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-964">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="5c6f6-965">Ändern Sie im `ConfigurationSample`-Profil den Wert der Umgebungsvariablen `ASPNETCORE_ENVIRONMENT` in `Production`.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-965">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="5c6f6-966">Speichern Sie die Datei, und führen Sie die App mit `dotnet run` in einer Befehlsshell aus.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-966">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="5c6f6-967">Die Einstellungen in der *appsettings.Development.json*-Datei setzen die Einstellungen in der Datei *appsettings.json* nicht länger außer Kraft.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-967">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="5c6f6-968">Die Protokollebene für den Schlüssel `Logging:LogLevel:Default` lautet `Warning`.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-968">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="5c6f6-969">XML-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-969">XML Configuration Provider</span></span>

<span data-ttu-id="5c6f6-970"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der XML-Datei.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-970">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="5c6f6-971">Um die XML-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-971">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="5c6f6-972">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-972">Overloads permit specifying:</span></span>

* <span data-ttu-id="5c6f6-973">Ob die Datei optional ist</span><span class="sxs-lookup"><span data-stu-id="5c6f6-973">Whether the file is optional.</span></span>
* <span data-ttu-id="5c6f6-974">Ob die Konfiguration bei Dateiänderungen neu geladen wird</span><span class="sxs-lookup"><span data-stu-id="5c6f6-974">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="5c6f6-975">Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird</span><span class="sxs-lookup"><span data-stu-id="5c6f6-975">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="5c6f6-976">Der Stammknoten der Konfigurationsdatei wird beim Erstellen der Konfigurations-Schlüssel-Wert-Paare ignoriert.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-976">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="5c6f6-977">Geben Sie keine Dokumenttypdefinition (DTD) und keinen Namespace in der Datei an.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-977">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="5c6f6-978">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-978">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="5c6f6-979">XML-Konfigurationsdateien können unterschiedliche Elementnamen für wiederholte Abschnitte verwenden:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-979">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="5c6f6-980">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-980">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="5c6f6-981">section0:key0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-981">section0:key0</span></span>
* <span data-ttu-id="5c6f6-982">section0:key1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-982">section0:key1</span></span>
* <span data-ttu-id="5c6f6-983">section1:key0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-983">section1:key0</span></span>
* <span data-ttu-id="5c6f6-984">section1:key1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-984">section1:key1</span></span>

<span data-ttu-id="5c6f6-985">Wiederholte Elemente mit den gleichen Elementnamen funktionieren, wenn das `name`-Attribut zur Unterscheidung der Elemente verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-985">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="5c6f6-986">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-986">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="5c6f6-987">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-987">section:section0:key:key0</span></span>
* <span data-ttu-id="5c6f6-988">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-988">section:section0:key:key1</span></span>
* <span data-ttu-id="5c6f6-989">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-989">section:section1:key:key0</span></span>
* <span data-ttu-id="5c6f6-990">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-990">section:section1:key:key1</span></span>

<span data-ttu-id="5c6f6-991">Mit Attributen können Werte bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-991">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="5c6f6-992">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-992">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="5c6f6-993">key:attribute</span><span class="sxs-lookup"><span data-stu-id="5c6f6-993">key:attribute</span></span>
* <span data-ttu-id="5c6f6-994">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="5c6f6-994">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="5c6f6-995">Schlüssel-pro-Datei-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-995">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="5c6f6-996"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> verwendet Verzeichnisdateien als Konfigurations-Schlüssel-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-996">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="5c6f6-997">Der Schlüssel ist der Dateiname.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-997">The key is the file name.</span></span> <span data-ttu-id="5c6f6-998">Der Wert enthält den Inhalt der Datei.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-998">The value contains the file's contents.</span></span> <span data-ttu-id="5c6f6-999">Der Schlüssel-pro-Datei-Konfigurationsanbieter wird in Docker-Hostingszenarien verwendet.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-999">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="5c6f6-1000">Um die Schlüssel-pro-Datei-Konfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1000">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="5c6f6-1001">Der `directoryPath` zu den Dateien muss ein absoluter Pfad sein.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1001">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="5c6f6-1002">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1002">Overloads permit specifying:</span></span>

* <span data-ttu-id="5c6f6-1003">Einen `Action<KeyPerFileConfigurationSource>`-Delegat, der die Quelle konfiguriert</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1003">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="5c6f6-1004">Ob das Verzeichnis optional ist; und den Pfad zum Verzeichnis</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1004">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="5c6f6-1005">Der doppelte Unterstrich (`__`) wird als Trennzeichen für Konfigurationsschlüssel in Dateinamen verwendet.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1005">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="5c6f6-1006">Der Dateiname `Logging__LogLevel__System` erzeugt z.B. den Konfigurationsschlüssel `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1006">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="5c6f6-1007">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1007">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="5c6f6-1008">Speicherkonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1008">Memory Configuration Provider</span></span>

<span data-ttu-id="5c6f6-1009"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> verwendet eine In-Memory-Sammlung für Konfigurations-Schlüssel-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1009">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="5c6f6-1010">Um die In-Memory-Sammlungskonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1010">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="5c6f6-1011">Der Konfigurationsanbieter kann mit `IEnumerable<KeyValuePair<String,String>>` initialisiert werden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1011">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="5c6f6-1012">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1012">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="5c6f6-1013">Im folgenden Beispiel wird ein Konfigurationswörterbuch erstellt:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1013">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="5c6f6-1014">Das Wörterbuch wird mit einem Aufruf von `AddInMemoryCollection` verwendet, um die Konfiguration bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1014">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="5c6f6-1015">GetValue</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1015">GetValue</span></span>

<span data-ttu-id="5c6f6-1016">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahiert einen einzelnen Wert aus der Konfiguration mit einem angegebenen Schlüssel und konvertiert ihn in den angegebenen Typ, der kein Auflistungstyp ist.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1016">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="5c6f6-1017">Eine Überladung akzeptiert einen Standardwert.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1017">An overload accepts a default value.</span></span>

<span data-ttu-id="5c6f6-1018">Im Beispiel unten geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1018">The following example:</span></span>

* <span data-ttu-id="5c6f6-1019">Der Zeichenfolgenwert aus der Konfiguration wird mit dem Schlüssel `NumberKey` extrahiert.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1019">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="5c6f6-1020">Wenn `NumberKey` nicht in den Konfigurationsschlüsseln gefunden wird, wird der Standardwert `99` verwendet.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1020">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="5c6f6-1021">Der Typ für den Wert wird als `int` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1021">Types the value as an `int`.</span></span>
* <span data-ttu-id="5c6f6-1022">Der Wert wird in der `NumberConfig`-Eigenschaft für die Verwendung durch die Seite gespeichert.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1022">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="5c6f6-1023">GetSection, GetChildren und Exists</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1023">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="5c6f6-1024">Betrachten Sie für die folgenden Beispiele die JSON-Datei unten.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1024">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="5c6f6-1025">Vier Schlüssel befinden sich in zwei Abschnitten, von denen einer ein Paar Unterabschnitte enthält:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1025">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="5c6f6-1026">Wenn die Datei in die Konfiguration gelesen wird, werden die folgenden eindeutigen hierarchischen Schlüssel erstellt, um die Konfigurationswerte zu enthalten:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1026">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="5c6f6-1027">section0:key0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1027">section0:key0</span></span>
* <span data-ttu-id="5c6f6-1028">section0:key1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1028">section0:key1</span></span>
* <span data-ttu-id="5c6f6-1029">section1:key0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1029">section1:key0</span></span>
* <span data-ttu-id="5c6f6-1030">section1:key1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1030">section1:key1</span></span>
* <span data-ttu-id="5c6f6-1031">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1031">section2:subsection0:key0</span></span>
* <span data-ttu-id="5c6f6-1032">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1032">section2:subsection0:key1</span></span>
* <span data-ttu-id="5c6f6-1033">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1033">section2:subsection1:key0</span></span>
* <span data-ttu-id="5c6f6-1034">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1034">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="5c6f6-1035">GetSection</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1035">GetSection</span></span>

<span data-ttu-id="5c6f6-1036">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrahiert einen Konfigurationsunterabschnitt mit dem angegebenen Unterabschnittsschlüssel.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1036">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="5c6f6-1037">Um ein <xref:Microsoft.Extensions.Configuration.IConfigurationSection>-Element zurückzugeben, das nur die Schlüssel-Wert-Paare in `section1` enthält, rufen Sie `GetSection` auf, und geben Sie den Abschnittsnamen an:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1037">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="5c6f6-1038">Das Element `configSection` weist keinen Wert auf, nur einen Schlüssel und einen Pfad.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1038">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="5c6f6-1039">Um die Werte für Schlüssel in `section2:subsection0` zu erhalten, rufen Sie `GetSection` auf, und geben Sie den Abschnittspfad an:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1039">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="5c6f6-1040">`GetSection` gibt nie `null` zurück.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1040">`GetSection` never returns `null`.</span></span> <span data-ttu-id="5c6f6-1041">Wenn kein entsprechender Abschnitt gefunden wird, wird ein leeres `IConfigurationSection`-Element zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1041">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="5c6f6-1042">Wenn `GetSection` einen entsprechenden Abschnitt zurückgibt, wird <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> nicht aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1042">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="5c6f6-1043">Eine Eigenschaft <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> und <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> werden zurückgegeben, wenn der Abschnitt vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1043">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="5c6f6-1044">GetChildren</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1044">GetChildren</span></span>

<span data-ttu-id="5c6f6-1045">Ein Aufruf von [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) auf `section2` erhält `IEnumerable<IConfigurationSection>` mit folgenden Elementen:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1045">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="5c6f6-1046">Vorhanden</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1046">Exists</span></span>

<span data-ttu-id="5c6f6-1047">Verwenden Sie [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) um zu bestimmen, ob ein Konfigurationsabschnitt vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1047">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="5c6f6-1048">Im Falle der Beispieldaten ist `sectionExists``false`, weil in den Konfigurationsdaten kein Abschnitt `section2:subsection2` vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1048">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="5c6f6-1049">Binden an eine Klasse</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1049">Bind to a class</span></span>

<span data-ttu-id="5c6f6-1050">Die Konfiguration kann mit *Optionsmustern* an Klassen gebunden werden, die Gruppen von verwandten Einstellungen darstellen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1050">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="5c6f6-1051">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1051">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="5c6f6-1052">Konfigurationswerte werden als Zeichenfolgen zurückgegeben, doch das Aufrufen von <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> ermöglicht die Erstellung von [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object)-Objekten.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1052">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span> <span data-ttu-id="5c6f6-1053">Der Binder bindet Werte an alle öffentlichen Lese-/Schreibeigenschaften des angegebenen Typs.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1053">The binder binds values to all of the public read/write properties of the type provided.</span></span> <span data-ttu-id="5c6f6-1054">Felder werden **nicht** gebunden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1054">Fields are **not** bound.</span></span>

<span data-ttu-id="5c6f6-1055">Die Beispiel-App enthält ein `Starship`-Modell (*Models/Starship.cs*):</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1055">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

<span data-ttu-id="5c6f6-1056">Der Abschnitt `starship` der Datei *starship.json* erstellt die Konfiguration, wenn die Beispiel-App den JSON-Konfigurationsanbieter zum Laden der Konfiguration verwendet:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1056">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

<span data-ttu-id="5c6f6-1057">Die folgenden Schlüssel-Wert-Paare werden für die Konfiguration erstellt:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1057">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="5c6f6-1058">Key</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1058">Key</span></span>                   | <span data-ttu-id="5c6f6-1059">Wert</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1059">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="5c6f6-1060">starship:name</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1060">starship:name</span></span>         | <span data-ttu-id="5c6f6-1061">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1061">USS Enterprise</span></span>                                    |
| <span data-ttu-id="5c6f6-1062">starship:registry</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1062">starship:registry</span></span>     | <span data-ttu-id="5c6f6-1063">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1063">NCC-1701</span></span>                                          |
| <span data-ttu-id="5c6f6-1064">starship:class</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1064">starship:class</span></span>        | <span data-ttu-id="5c6f6-1065">Constitution</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1065">Constitution</span></span>                                      |
| <span data-ttu-id="5c6f6-1066">starship:length</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1066">starship:length</span></span>       | <span data-ttu-id="5c6f6-1067">304,8</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1067">304.8</span></span>                                             |
| <span data-ttu-id="5c6f6-1068">starship:commissioned</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1068">starship:commissioned</span></span> | <span data-ttu-id="5c6f6-1069">False</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1069">False</span></span>                                             |
| <span data-ttu-id="5c6f6-1070">trademark</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1070">trademark</span></span>             | <span data-ttu-id="5c6f6-1071">Paramount Pictures Corp. https://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1071">Paramount Pictures Corp. https://www.paramount.com</span></span> |

<span data-ttu-id="5c6f6-1072">Die Beispiel-App ruft `GetSection` mit dem `starship`-Schlüssel auf.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1072">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="5c6f6-1073">Die `starship`-Schlüssel-Wert-Paaren sind isoliert.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1073">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="5c6f6-1074">Die `Bind`-Methode wird auf dem Unterabschnitt aufgerufen, der in einer Instanz der `Starship`-Klasse übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1074">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="5c6f6-1075">Nach dem Binden der Instanzwerte wird die Instanz einer Eigenschaft zum Rendern zugewiesen:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1075">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="5c6f6-1076">Binden an ein Objektdiagramm</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1076">Bind to an object graph</span></span>

<span data-ttu-id="5c6f6-1077"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> kann ein ganzes POCO-Objektdiagramm binden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1077"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="5c6f6-1078">Wie beim Binden eines einfachen Objekts werden nur öffentliche Lese-/Schreibeigenschaften gebunden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1078">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="5c6f6-1079">Das Beispiel enthält ein `TvShow`-Modell, dessen Objektdiagramm die Klassen `Metadata` und `Actors` enthält (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1079">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="5c6f6-1080">Die Beispiel-App verfügt über eine *tvshow.xml*-Datei mit den Konfigurationsdaten:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1080">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="5c6f6-1081">Die Konfiguration wird mit der `Bind`-Methode an das gesamte `TvShow`-Objektdiagramm gebunden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1081">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="5c6f6-1082">Die gebundene Instanz ist einer Eigenschaft zum Rendern zugewiesen:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1082">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="5c6f6-1083">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) bindet den angegebenen Typ und gibt ihn zurück.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1083">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="5c6f6-1084">`Get<T>` ist praktischer als `Bind`.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1084">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="5c6f6-1085">Der folgende Code zeigt die Verwendung von `Get<T>` mit dem vorherigen Beispiel, sodass die gebundene Instanz direkt der Eigenschaft zum Rendern zugewiesen werden kann:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1085">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="5c6f6-1086">Binden eines Arrays an eine Klasse</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1086">Bind an array to a class</span></span>

<span data-ttu-id="5c6f6-1087">*Die Beispiel-App veranschaulicht die in diesem Abschnitt erläuterten Konzepte.*</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1087">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="5c6f6-1088"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> unterstützt das Binden von Arrays an Objekte mit Arrayindizes in Konfigurationsschlüsseln.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1088">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="5c6f6-1089">Jedes Arrayformat, das ein numerisches Schlüsselsegment (`:0:`, `:1:`, &hellip; `:{n}:`) verfügbar macht, kann ein Array an ein POCO-Klassenarray binden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1089">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="5c6f6-1090">Das Binden ist standardmäßig möglich.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1090">Binding is provided by convention.</span></span> <span data-ttu-id="5c6f6-1091">Benutzerdefinierte Konfigurationsanbieter sind nicht erforderlich, um Arraybindung zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1091">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="5c6f6-1092">**In-Memory-Arrayverarbeitung**</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1092">**In-memory array processing**</span></span>

<span data-ttu-id="5c6f6-1093">Betrachten Sie die Konfigurationsschlüssel und -werte in der folgenden Tabelle.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1093">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="5c6f6-1094">Key</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1094">Key</span></span>             | <span data-ttu-id="5c6f6-1095">Wert</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1095">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="5c6f6-1096">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1096">array:entries:0</span></span> | <span data-ttu-id="5c6f6-1097">value0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1097">value0</span></span> |
| <span data-ttu-id="5c6f6-1098">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1098">array:entries:1</span></span> | <span data-ttu-id="5c6f6-1099">value1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1099">value1</span></span> |
| <span data-ttu-id="5c6f6-1100">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1100">array:entries:2</span></span> | <span data-ttu-id="5c6f6-1101">value2</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1101">value2</span></span> |
| <span data-ttu-id="5c6f6-1102">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1102">array:entries:4</span></span> | <span data-ttu-id="5c6f6-1103">value4</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1103">value4</span></span> |
| <span data-ttu-id="5c6f6-1104">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1104">array:entries:5</span></span> | <span data-ttu-id="5c6f6-1105">value5</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1105">value5</span></span> |

<span data-ttu-id="5c6f6-1106">Diese Schlüssel und Werte werden mit dem Speicherkonfigurationsanbieter in die Beispiel-App geladen:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1106">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="5c6f6-1107">Das Array überspringt einen Wert für Index &num;3.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1107">The array skips a value for index &num;3.</span></span> <span data-ttu-id="5c6f6-1108">Der Konfigurationsbinder kann weder NULL-Werte binden noch NULL-Einträge in gebundenen Objekten erstellen. Dies wird deutlich, wenn das Ergebnis der Bindung dieses Arrays an ein Objekt demonstriert wird.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1108">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="5c6f6-1109">In der Beispiel-App ist eine POCO-Klasse verfügbar, um die gebundenen Konfigurationsdaten zu enthalten:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1109">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="5c6f6-1110">Die Konfigurationsdaten werden an das Objekt gebunden:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1110">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="5c6f6-1111">Die [ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)-Syntax kann auch verwendet werden, wodurch ein kompakterer Code entsteht:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1111">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="5c6f6-1112">Das gebundene Objekt, eine Instanz von `ArrayExample`, empfängt die Arraydaten aus der Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1112">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="5c6f6-1113">`ArrayExample.Entries`-Index</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1113">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="5c6f6-1114">`ArrayExample.Entries`-Wert</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1114">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="5c6f6-1115">0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1115">0</span></span>                            | <span data-ttu-id="5c6f6-1116">value0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1116">value0</span></span>                       |
| <span data-ttu-id="5c6f6-1117">1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1117">1</span></span>                            | <span data-ttu-id="5c6f6-1118">value1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1118">value1</span></span>                       |
| <span data-ttu-id="5c6f6-1119">2</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1119">2</span></span>                            | <span data-ttu-id="5c6f6-1120">value2</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1120">value2</span></span>                       |
| <span data-ttu-id="5c6f6-1121">3</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1121">3</span></span>                            | <span data-ttu-id="5c6f6-1122">value4</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1122">value4</span></span>                       |
| <span data-ttu-id="5c6f6-1123">4</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1123">4</span></span>                            | <span data-ttu-id="5c6f6-1124">value5</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1124">value5</span></span>                       |

<span data-ttu-id="5c6f6-1125">Index &num;3 im gebundenen Objekt enthält die Konfigurationsdaten für den `array:4`-Konfigurationsschlüssel und die Wert für `value4`.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1125">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="5c6f6-1126">Beim Binden von Konfigurationsdaten, die ein Array enthalten, werden die Arrayindizes in den Konfigurationsschlüsseln lediglich zum Durchlaufen der Konfigurationsdaten beim Erstellen des Objekts verwendet.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1126">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="5c6f6-1127">Ein NULL-Wert kann in den Konfigurationsdaten nicht beibehalten werden, und ein NULL-Eintrag wird nicht in einem gebundenen Objekt erstellt, wenn ein Array in Konfigurationsschlüsseln mindestens einen Index überspringt.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1127">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="5c6f6-1128">Das fehlende Konfigurationselement für Index &num;3 kann vor dem Binden an die `ArrayExample`Instanz von jedem Konfigurationsanbieter bereitgestellt werden, der das richtige Schlüssel-Wert-Paar in der Konfiguration erzeugt.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1128">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="5c6f6-1129">Wenn das Beispiel einen zusätzlichen JSON-Konfigurationsanbieter mit dem fehlenden Schlüssel-Wert-Paar enthält, entspricht `ArrayExample.Entries` dem kompletten Konfigurationsarray:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1129">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="5c6f6-1130">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1130">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="5c6f6-1131">In `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1131">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="5c6f6-1132">Das Schlüssel-Wert-Paar in der Tabelle wird in die Konfiguration geladen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1132">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="5c6f6-1133">Key</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1133">Key</span></span>             | <span data-ttu-id="5c6f6-1134">Wert</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1134">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="5c6f6-1135">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1135">array:entries:3</span></span> | <span data-ttu-id="5c6f6-1136">value3</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1136">value3</span></span> |

<span data-ttu-id="5c6f6-1137">Wenn die `ArrayExample`-Klasseninstanz gebunden wird, nachdem der JSON-Konfigurationsanbieter den Eintrag für Index &num;3 enthält, enthält das `ArrayExample.Entries`-Array den Wert.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1137">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="5c6f6-1138">`ArrayExample.Entries`-Index</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1138">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="5c6f6-1139">`ArrayExample.Entries`-Wert</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1139">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="5c6f6-1140">0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1140">0</span></span>                            | <span data-ttu-id="5c6f6-1141">value0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1141">value0</span></span>                       |
| <span data-ttu-id="5c6f6-1142">1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1142">1</span></span>                            | <span data-ttu-id="5c6f6-1143">value1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1143">value1</span></span>                       |
| <span data-ttu-id="5c6f6-1144">2</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1144">2</span></span>                            | <span data-ttu-id="5c6f6-1145">value2</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1145">value2</span></span>                       |
| <span data-ttu-id="5c6f6-1146">3</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1146">3</span></span>                            | <span data-ttu-id="5c6f6-1147">value3</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1147">value3</span></span>                       |
| <span data-ttu-id="5c6f6-1148">4</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1148">4</span></span>                            | <span data-ttu-id="5c6f6-1149">value4</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1149">value4</span></span>                       |
| <span data-ttu-id="5c6f6-1150">5</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1150">5</span></span>                            | <span data-ttu-id="5c6f6-1151">value5</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1151">value5</span></span>                       |

<span data-ttu-id="5c6f6-1152">**JSON-Arrayverarbeitung**</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1152">**JSON array processing**</span></span>

<span data-ttu-id="5c6f6-1153">Wenn eine JSON-Datei ein Array enthält, werden Konfigurationsschlüssel für die Arrayelemente mit einem nullbasierten Abschnittsindex erstellt.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1153">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="5c6f6-1154">In der folgenden Konfigurationsdatei ist `subsection` ein Array:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1154">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="5c6f6-1155">Der JSON-Konfigurationsanbieter liest die Konfigurationsdaten in die folgenden Schlüssel-Wert-Paare:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1155">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="5c6f6-1156">Key</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1156">Key</span></span>                     | <span data-ttu-id="5c6f6-1157">Wert</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1157">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="5c6f6-1158">json_array:key</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1158">json_array:key</span></span>          | <span data-ttu-id="5c6f6-1159">valueA</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1159">valueA</span></span> |
| <span data-ttu-id="5c6f6-1160">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1160">json_array:subsection:0</span></span> | <span data-ttu-id="5c6f6-1161">valueB</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1161">valueB</span></span> |
| <span data-ttu-id="5c6f6-1162">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1162">json_array:subsection:1</span></span> | <span data-ttu-id="5c6f6-1163">valueC</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1163">valueC</span></span> |
| <span data-ttu-id="5c6f6-1164">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1164">json_array:subsection:2</span></span> | <span data-ttu-id="5c6f6-1165">valueD</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1165">valueD</span></span> |

<span data-ttu-id="5c6f6-1166">In der Beispiel-App können die Konfigurations-Schlüssel-Wert-Paare mit dieser POCO-Klasse gebunden werden:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1166">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="5c6f6-1167">Nach dem Binden enthält `JsonArrayExample.Key` den Wert `valueA`.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1167">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="5c6f6-1168">Die Unterabschnittwerte werden in der POCO-Arrayeigenschaft `Subsection` gespeichert.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1168">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="5c6f6-1169">`JsonArrayExample.Subsection`-Index</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1169">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="5c6f6-1170">`JsonArrayExample.Subsection`-Wert</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1170">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="5c6f6-1171">0</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1171">0</span></span>                                   | <span data-ttu-id="5c6f6-1172">valueB</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1172">valueB</span></span>                              |
| <span data-ttu-id="5c6f6-1173">1</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1173">1</span></span>                                   | <span data-ttu-id="5c6f6-1174">valueC</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1174">valueC</span></span>                              |
| <span data-ttu-id="5c6f6-1175">2</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1175">2</span></span>                                   | <span data-ttu-id="5c6f6-1176">valueD</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1176">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="5c6f6-1177">Benutzerdefinierter Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1177">Custom configuration provider</span></span>

<span data-ttu-id="5c6f6-1178">Die Beispiel-App veranschaulicht, wie ein Standardkonfigurationsanbieter erstellt wird, der Konfigurations-Schlüssel-Wert-Paare aus einer Datenbank mit [Entity Framework (EF)](/ef/core/) liest.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1178">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="5c6f6-1179">Der Anbieter weist die folgenden Merkmale auf:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1179">The provider has the following characteristics:</span></span>

* <span data-ttu-id="5c6f6-1180">Die EF-In-Memory-Datenbank wird zu Demonstrationszwecken verwendet.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1180">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="5c6f6-1181">Um eine Datenbank zu verwenden, die eine Verbindungszeichenfolge benötigt, implementieren Sie einen sekundären `ConfigurationBuilder`, um die Verbindungszeichenfolge aus einem anderen Konfigurationsanbieter anzugeben.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1181">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="5c6f6-1182">Der Anbieter liest eine Datenbanktabelle beim Start in die Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1182">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="5c6f6-1183">Der Anbieter fragt die Datenbank nicht pro Schlüssel ab.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1183">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="5c6f6-1184">Das erneute Laden bei Änderung ist nicht implementiert. Das heißt, das Aktualisieren der Datenbank nach App-Start hat keine Auswirkungen auf die App-Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1184">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="5c6f6-1185">Definieren Sie eine `EFConfigurationValue`-Entität zum Speichern von Konfigurationswerten in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1185">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="5c6f6-1186">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1186">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="5c6f6-1187">Fügen Sie `EFConfigurationContext` hinzu, um die konfigurierten Werte zu speichern und auf diese zugreifen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1187">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="5c6f6-1188">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1188">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="5c6f6-1189">Erstellen Sie eine Klasse, die das <xref:Microsoft.Extensions.Configuration.IConfigurationSource> implementiert.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1189">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="5c6f6-1190">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1190">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="5c6f6-1191">Erstellen Sie den benutzerdefinierten Konfigurationsanbieter durch Vererbung von <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1191">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="5c6f6-1192">Der Konfigurationsanbieter initialisiert die Datenbank, wenn diese leer ist.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1192">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="5c6f6-1193">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1193">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="5c6f6-1194">Mit einer `AddEFConfiguration`-Erweiterungsmethode kann die Konfigurationsquelle `ConfigurationBuilder` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1194">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="5c6f6-1195">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1195">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="5c6f6-1196">Der folgende Code veranschaulicht die Verwendung des benutzerdefinierten Anbieters `EFConfigurationProvider` in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1196">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="5c6f6-1197">Zugreifen auf die Konfiguration während des Starts</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1197">Access configuration during startup</span></span>

<span data-ttu-id="5c6f6-1198">Fügen Sie `IConfiguration` in den `Startup`-Konstruktor ein, um auf Konfigurationswerte in `Startup.ConfigureServices` zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1198">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5c6f6-1199">Um auf die Konfiguration in `Startup.Configure` zuzugreifen, fügen Sie entweder `IConfiguration` direkt in die Methode ein, oder verwenden Sie die Instanz aus dem Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1199">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="5c6f6-1200">Ein Beispiel für den Zugriff auf die Konfiguration mit den Starthilfsmethoden finden Sie unter [Anwendungsstart: Hilfsmethoden](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1200">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="5c6f6-1201">Zugreifen auf die Konfiguration auf einer Razor Pages-Seite oder in einer MVC-Ansicht</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1201">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="5c6f6-1202">Um auf die Konfigurationseinstellungen auf einer Razor Pages-Seite oder in einer MVC-Ansicht zuzugreifen, fügen Sie eine [using-Anweisung](xref:mvc/views/razor#using) ([C#-Referenz: using-Anweisung](/dotnet/csharp/language-reference/keywords/using-directive)) für den [Microsoft.Extensions.Configuration-Namespace ](xref:Microsoft.Extensions.Configuration) hinzu, und fügen Sie <xref:Microsoft.Extensions.Configuration.IConfiguration> auf der Seite bzw. in der Ansicht ein.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1202">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="5c6f6-1203">Auf einer Razor Pages-Seite:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1203">In a Razor Pages page:</span></span>

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

<span data-ttu-id="5c6f6-1204">In einer MVC-Ansicht:</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1204">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="5c6f6-1205">Hinzufügen von Konfigurationen aus einer externen Assembly</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1205">Add configuration from an external assembly</span></span>

<span data-ttu-id="5c6f6-1206">Eine <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>-Implementierung ermöglicht das Hinzufügen von Erweiterungen zu einer App beim Start von einer externen Assembly außerhalb der `Startup`-Klasse der App aus.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1206">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="5c6f6-1207">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1207">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5c6f6-1208">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="5c6f6-1208">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
