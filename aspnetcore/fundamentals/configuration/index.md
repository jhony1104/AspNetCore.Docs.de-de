---
title: Konfiguration in ASP.NET Core
author: guardrex
description: In diesem Artikel erfahren Sie, wie Sie mit der Konfigurations-API eine ASP.NET Core-App konfigurieren.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
uid: fundamentals/configuration/index
ms.openlocfilehash: 09ef06f179e34cd7f4f04ac30c3b5dd95d058244
ms.sourcegitcommit: 2388c2a7334ce66b6be3ffbab06dd7923df18f60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75951873"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="8b016-103">Konfiguration in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8b016-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="8b016-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8b016-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="8b016-105">Die App-Konfiguration in ASP.NET Core basiert auf Schlüssel-Wert-Paaren, die von *Konfigurationsanbietern* erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="8b016-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="8b016-106">Konfigurationsanbieter lesen Konfigurationsdaten in Schlüssel-Wert-Paare aus verschiedenen Konfigurationsquellen:</span><span class="sxs-lookup"><span data-stu-id="8b016-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="8b016-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8b016-107">Azure Key Vault</span></span>
* <span data-ttu-id="8b016-108">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="8b016-108">Azure App Configuration</span></span>
* <span data-ttu-id="8b016-109">Befehlszeilenargumente</span><span class="sxs-lookup"><span data-stu-id="8b016-109">Command-line arguments</span></span>
* <span data-ttu-id="8b016-110">Benutzerdefinierte Anbieter (installiert oder erstellt)</span><span class="sxs-lookup"><span data-stu-id="8b016-110">Custom providers (installed or created)</span></span>
* <span data-ttu-id="8b016-111">Verzeichnisdateien</span><span class="sxs-lookup"><span data-stu-id="8b016-111">Directory files</span></span>
* <span data-ttu-id="8b016-112">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="8b016-112">Environment variables</span></span>
* <span data-ttu-id="8b016-113">Speicherinterne .NET Objekte</span><span class="sxs-lookup"><span data-stu-id="8b016-113">In-memory .NET objects</span></span>
* <span data-ttu-id="8b016-114">Einstellungsdateien</span><span class="sxs-lookup"><span data-stu-id="8b016-114">Settings files</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8b016-115">Konfigurationspakete für gängige Konfigurationsanbieterszenarien ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) sind über das Framework implizit enthalten.</span><span class="sxs-lookup"><span data-stu-id="8b016-115">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included implicitly by the framework.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8b016-116">Konfigurationspakete für gängige Konfigurationsanbieterszenarien ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) sind im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="8b016-116">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="8b016-117">Codebeispiele, die den <xref:Microsoft.Extensions.Configuration>-Namespace befolgen und in der Beispiel-App verwenden:</span><span class="sxs-lookup"><span data-stu-id="8b016-117">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="8b016-118">Das *Optionsmuster* ist eine Erweiterung der in diesem Thema beschriebenen Konfigurationskonzepte.</span><span class="sxs-lookup"><span data-stu-id="8b016-118">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="8b016-119">Optionen verwenden Klassen, um Gruppen von zusammengehörigen Einstellungen darzustellen.</span><span class="sxs-lookup"><span data-stu-id="8b016-119">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="8b016-120">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="8b016-120">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="8b016-121">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8b016-121">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="8b016-122">Hostkonfiguration und App-Konfiguration im Vergleich</span><span class="sxs-lookup"><span data-stu-id="8b016-122">Host versus app configuration</span></span>

<span data-ttu-id="8b016-123">Bevor die App konfiguriert und gestartet wird, wird ein *Host* konfiguriert und gestartet.</span><span class="sxs-lookup"><span data-stu-id="8b016-123">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="8b016-124">Der Host ist verantwortlich für das Starten der App und das Verwalten der Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="8b016-124">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="8b016-125">Die App und der Host werden mit den in diesem Thema beschriebenen Konfigurationsanbietern konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="8b016-125">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="8b016-126">Schlüssel-Wert-Paare der Hostkonfiguration sind ebenfalls in der globalen App-Konfiguration enthalten.</span><span class="sxs-lookup"><span data-stu-id="8b016-126">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="8b016-127">Weitere Informationen dazu, wie Konfigurationsanbieter beim Erstellen des Hosts verwendet werden, und wie sich Konfigurationsquellen auf die Hostkonfiguration auswirken, finden Sie unter <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="8b016-127">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="8b016-128">Standardkonfiguration</span><span class="sxs-lookup"><span data-stu-id="8b016-128">Default configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8b016-129">Web-Apps, die auf den [dotnet new](/dotnet/core/tools/dotnet-new)-Vorlagen von ASP.NET Core basieren, rufen beim Erstellen eines Hosts <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> auf.</span><span class="sxs-lookup"><span data-stu-id="8b016-129">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="8b016-130">`CreateDefaultBuilder` legt die Standardkonfiguration für die App in der folgenden Reihenfolge fest:</span><span class="sxs-lookup"><span data-stu-id="8b016-130">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="8b016-131">Folgendes gilt für Apps, die den [generischen Host](xref:fundamentals/host/generic-host) verwenden.</span><span class="sxs-lookup"><span data-stu-id="8b016-131">The following applies to apps using the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="8b016-132">Ausführliche Informationen zur Standardkonfiguration bei Verwendung des [Webhosts](xref:fundamentals/host/web-host) finden Sie in der [ASP.NET Core 2.2-Version dieses Themas](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="8b016-132">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="8b016-133">Die Konfiguration des Hosts wird durch Folgendes festgelegt:</span><span class="sxs-lookup"><span data-stu-id="8b016-133">Host configuration is provided from:</span></span>
  * <span data-ttu-id="8b016-134">Umgebungsvariablen mit dem Präfix `DOTNET_` (zum Beispiel `DOTNET_ENVIRONMENT`) mithilfe des [Umgebungsvariablen-Konfigurationsanbieters](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8b016-134">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="8b016-135">Das Präfix (`DOTNET_`) wird beim Laden der Schlüssel-Wert-Paare der Konfiguration entfernt.</span><span class="sxs-lookup"><span data-stu-id="8b016-135">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="8b016-136">Befehlszeilenargumente mithilfe des [Befehlszeilen-Konfigurationsanbieters](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8b016-136">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="8b016-137">Die Webhost-Standardkonfiguration wird eingerichtet (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="8b016-137">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="8b016-138">Als Webserver wird Kestrel verwendet und mithilfe der Konfigurationsanbieter der App konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="8b016-138">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="8b016-139">Fügen Sie Middleware zum Filtern von Hosts hinzu.</span><span class="sxs-lookup"><span data-stu-id="8b016-139">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="8b016-140">Fügen Sie Middleware für weitergeleitete Header hinzu, wenn die Umgebungsvariable `ASPNETCORE_FORWARDEDHEADERS_ENABLED` auf `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="8b016-140">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="8b016-141">Aktivieren Sie die IIS-Integration.</span><span class="sxs-lookup"><span data-stu-id="8b016-141">Enable IIS integration.</span></span>
* <span data-ttu-id="8b016-142">Die App-Konfiguration wird durch Folgendes festgelegt:</span><span class="sxs-lookup"><span data-stu-id="8b016-142">App configuration is provided from:</span></span>
  * <span data-ttu-id="8b016-143">*appsettings.json* mithilfe des [Dateikonfigurationsanbieters](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8b016-143">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="8b016-144">*appsettings.{Umgebung}.json* mithilfe des [Dateikonfigurationsanbieters](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8b016-144">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="8b016-145">[Geheimnis-Manager](xref:security/app-secrets), wenn die App in der `Development`-Umgebung mit der Einstiegsassembly ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="8b016-145">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="8b016-146">Umgebungsvariablen mithilfe des [Umgebungsvariablen-Konfigurationsanbieters](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8b016-146">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="8b016-147">Befehlszeilenargumente mithilfe des [Befehlszeilen-Konfigurationsanbieters](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8b016-147">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8b016-148">Web-Apps, die auf den [dotnet new](/dotnet/core/tools/dotnet-new)-Vorlagen von ASP.NET Core basieren, rufen beim Erstellen eines Hosts <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> auf.</span><span class="sxs-lookup"><span data-stu-id="8b016-148">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="8b016-149">`CreateDefaultBuilder` legt die Standardkonfiguration für die App in der folgenden Reihenfolge fest:</span><span class="sxs-lookup"><span data-stu-id="8b016-149">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="8b016-150">Folgendes gilt für Apps, die den [Webhost](xref:fundamentals/host/web-host) verwenden.</span><span class="sxs-lookup"><span data-stu-id="8b016-150">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="8b016-151">Ausführliche Informationen zur Standardkonfiguration bei Verwendung des [generischen Hosts](xref:fundamentals/host/generic-host) finden Sie in der [neuesten Version dieses Themas](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="8b016-151">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="8b016-152">Die Konfiguration des Hosts wird durch Folgendes festgelegt:</span><span class="sxs-lookup"><span data-stu-id="8b016-152">Host configuration is provided from:</span></span>
  * <span data-ttu-id="8b016-153">Umgebungsvariablen mit dem Präfix `ASPNETCORE_` (zum Beispiel `ASPNETCORE_ENVIRONMENT`) mithilfe des [Umgebungsvariablen-Konfigurationsanbieters](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8b016-153">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="8b016-154">Das Präfix (`ASPNETCORE_`) wird beim Laden der Schlüssel-Wert-Paare der Konfiguration entfernt.</span><span class="sxs-lookup"><span data-stu-id="8b016-154">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="8b016-155">Befehlszeilenargumente mithilfe des [Befehlszeilen-Konfigurationsanbieters](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8b016-155">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="8b016-156">Die App-Konfiguration wird durch Folgendes festgelegt:</span><span class="sxs-lookup"><span data-stu-id="8b016-156">App configuration is provided from:</span></span>
  * <span data-ttu-id="8b016-157">*appsettings.json* mithilfe des [Dateikonfigurationsanbieters](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8b016-157">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="8b016-158">*appsettings.{Umgebung}.json* mithilfe des [Dateikonfigurationsanbieters](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8b016-158">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="8b016-159">[Geheimnis-Manager](xref:security/app-secrets), wenn die App in der `Development`-Umgebung mit der Einstiegsassembly ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="8b016-159">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="8b016-160">Umgebungsvariablen mithilfe des [Umgebungsvariablen-Konfigurationsanbieters](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8b016-160">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="8b016-161">Befehlszeilenargumente mithilfe des [Befehlszeilen-Konfigurationsanbieters](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="8b016-161">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

## <a name="security"></a><span data-ttu-id="8b016-162">Sicherheit</span><span class="sxs-lookup"><span data-stu-id="8b016-162">Security</span></span>

<span data-ttu-id="8b016-163">Wenden Sie die folgenden Verfahren an, um vertrauliche Konfigurationsdaten zu schützen:</span><span class="sxs-lookup"><span data-stu-id="8b016-163">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="8b016-164">Speichern Sie nie Kennwörter oder andere vertrauliche Daten im Konfigurationsanbietercode oder in Nur-Text-Konfigurationsdateien.</span><span class="sxs-lookup"><span data-stu-id="8b016-164">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="8b016-165">Verwenden Sie keine Produktionsgeheimnisse in Entwicklungs- oder Testumgebungen.</span><span class="sxs-lookup"><span data-stu-id="8b016-165">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="8b016-166">Geben Sie Geheimnisse außerhalb des Projekts an, damit sie nicht versehentlich in ein Quellcoderepository übernommen werden können.</span><span class="sxs-lookup"><span data-stu-id="8b016-166">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="8b016-167">Weitere Informationen finden Sie unter den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="8b016-167">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="8b016-168"><xref:security/app-secrets> &ndash; Enthält Hinweise zur Verwendung von Umgebungsvariablen zum Speichern sensibler Daten.</span><span class="sxs-lookup"><span data-stu-id="8b016-168"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="8b016-169">Der Geheimnis-Manager verwendet den Dateikonfigurationsanbieter, um Benutzergeheimnisse in einer JSON-Datei im lokalen System zu speichern.</span><span class="sxs-lookup"><span data-stu-id="8b016-169">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="8b016-170">Der Dateikonfigurationsanbieter wird später in diesem Thema beschrieben.</span><span class="sxs-lookup"><span data-stu-id="8b016-170">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="8b016-171">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) speichert App-Geheimnisse für ASP.NET Core-Apps auf sichere Weise.</span><span class="sxs-lookup"><span data-stu-id="8b016-171">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="8b016-172">Weitere Informationen finden Sie unter <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="8b016-172">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="8b016-173">Hierarchische Konfigurationsdaten</span><span class="sxs-lookup"><span data-stu-id="8b016-173">Hierarchical configuration data</span></span>

<span data-ttu-id="8b016-174">Die Konfigurations-API kann hierarchische Konfigurationsdaten erhalten, indem sie die hierarchischen Daten mit einem Trennzeichen in den Konfigurationsschlüsseln vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="8b016-174">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="8b016-175">Die folgende JSON-Datei enthält vier Schlüssel in einer strukturierten Hierarchie von zwei Abschnitten:</span><span class="sxs-lookup"><span data-stu-id="8b016-175">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="8b016-176">Wenn die Datei in die Konfiguration gelesen wird, werden eindeutige Schlüssel erstellt, um die ursprüngliche hierarchische Datenstruktur der Konfigurationsquelle zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="8b016-176">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="8b016-177">Die Abschnitte und Schlüssel werden mithilfe eines Doppelpunkts (`:`) vereinfacht, um die Originalstruktur zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="8b016-177">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="8b016-178">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8b016-178">section0:key0</span></span>
* <span data-ttu-id="8b016-179">section0:key1</span><span class="sxs-lookup"><span data-stu-id="8b016-179">section0:key1</span></span>
* <span data-ttu-id="8b016-180">section1:key0</span><span class="sxs-lookup"><span data-stu-id="8b016-180">section1:key0</span></span>
* <span data-ttu-id="8b016-181">section1:key1</span><span class="sxs-lookup"><span data-stu-id="8b016-181">section1:key1</span></span>

<span data-ttu-id="8b016-182">Mit den Methoden <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> und <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> können Abschnitte und untergeordnete Abschnittelemente in den Konfigurationsdaten isoliert werden.</span><span class="sxs-lookup"><span data-stu-id="8b016-182"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="8b016-183">Diese Methoden werden später im Abschnitt [GetSection, GetChildren und Exists](#getsection-getchildren-and-exists) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="8b016-183">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="8b016-184">Konventionen</span><span class="sxs-lookup"><span data-stu-id="8b016-184">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="8b016-185">Quellen und Anbieter</span><span class="sxs-lookup"><span data-stu-id="8b016-185">Sources and providers</span></span>

<span data-ttu-id="8b016-186">Beim Starten der Anwendung werden Konfigurationsquellen in der Reihenfolge gelesen, in der ihre Konfigurationsanbieter angegeben sind.</span><span class="sxs-lookup"><span data-stu-id="8b016-186">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="8b016-187">Konfigurationsanbieter, die Änderungserkennung implementieren, können Konfigurationen erneut laden, wenn zugrunde liegende Einstellungen geändert werden.</span><span class="sxs-lookup"><span data-stu-id="8b016-187">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="8b016-188">Der Dateikonfigurationsanbieter (weiter unten in diesem Thema beschrieben) und der [Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration) implementieren beispielsweise die Änderungserkennung.</span><span class="sxs-lookup"><span data-stu-id="8b016-188">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="8b016-189"><xref:Microsoft.Extensions.Configuration.IConfiguration> steht im App-Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="8b016-189"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="8b016-190"><xref:Microsoft.Extensions.Configuration.IConfiguration> kann in eine <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel>-Klasse von Razor Pages oder <xref:Microsoft.AspNetCore.Mvc.Controller>-Klasse von MVC eingefügt werden, um die Konfiguration für die Klasse abzurufen.</span><span class="sxs-lookup"><span data-stu-id="8b016-190"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="8b016-191">In den folgenden Beispielen wird das Feld `_config` verwendet, um auf Konfigurationswerte zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="8b016-191">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="8b016-192">Konfigurationsanbieter können die Abhängigkeitsinjektion nicht verwenden, weil sie nicht verfügbar ist, wenn sie vom Host eingerichtet werden.</span><span class="sxs-lookup"><span data-stu-id="8b016-192">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="8b016-193">Tasten</span><span class="sxs-lookup"><span data-stu-id="8b016-193">Keys</span></span>

<span data-ttu-id="8b016-194">Konfigurationsschlüssel entsprechen den folgenden Konventionen:</span><span class="sxs-lookup"><span data-stu-id="8b016-194">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="8b016-195">Bei Schlüsseln wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="8b016-195">Keys are case-insensitive.</span></span> <span data-ttu-id="8b016-196">Beispielsweise verweisen `ConnectionString` und `connectionstring` auf denselben Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="8b016-196">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="8b016-197">Wenn ein Wert für denselben Schlüssel von denselben oder unterschiedlichen Konfigurationsanbietern festgelegt wird, wird der zuletzt für den Schlüssel bestimmte Wert verwendet.</span><span class="sxs-lookup"><span data-stu-id="8b016-197">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="8b016-198">Hierarchische Schlüssel</span><span class="sxs-lookup"><span data-stu-id="8b016-198">Hierarchical keys</span></span>
  * <span data-ttu-id="8b016-199">Innerhalb der Konfigurations-API funktioniert ein Doppelpunkt (`:`) als Trennzeichen auf allen Plattformen.</span><span class="sxs-lookup"><span data-stu-id="8b016-199">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="8b016-200">In Umgebungsvariablen funktioniert ein Doppelpunkt als Trennzeichen ggf. nicht auf allen Plattformen.</span><span class="sxs-lookup"><span data-stu-id="8b016-200">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="8b016-201">Ein doppelter Unterstrich (`__`) wird auf allen Plattformen unterstützt und automatisch in einen Doppelpunkt konvertiert.</span><span class="sxs-lookup"><span data-stu-id="8b016-201">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="8b016-202">In Azure Key Vault verwenden hierarchische Schlüssel zwei Bindestriche (`--`) als Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="8b016-202">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="8b016-203">Schreiben Sie Code, um die Bindestriche durch einen Doppelpunkt zu ersetzen, wenn die Geheimnisse in die App-Konfiguration geladen werden.</span><span class="sxs-lookup"><span data-stu-id="8b016-203">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="8b016-204"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> unterstützt das Binden von Arrays an Objekte mit Arrayindizes in Konfigurationsschlüsseln.</span><span class="sxs-lookup"><span data-stu-id="8b016-204">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8b016-205">Die Arraybindung wird im Abschnitt [Binden eines Arrays an eine Klasse](#bind-an-array-to-a-class) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="8b016-205">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="8b016-206">Werte</span><span class="sxs-lookup"><span data-stu-id="8b016-206">Values</span></span>

<span data-ttu-id="8b016-207">Konfigurationswerte entsprechen den folgenden Konventionen:</span><span class="sxs-lookup"><span data-stu-id="8b016-207">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="8b016-208">Die Werte sind Zeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="8b016-208">Values are strings.</span></span>
* <span data-ttu-id="8b016-209">NULL-Werte können nicht in einer Konfiguration gespeichert oder an Objekte gebunden werden.</span><span class="sxs-lookup"><span data-stu-id="8b016-209">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="8b016-210">Anbieter</span><span class="sxs-lookup"><span data-stu-id="8b016-210">Providers</span></span>

<span data-ttu-id="8b016-211">Die folgende Tabelle zeigt die für ASP.NET Core-Apps verfügbaren Konfigurationsanbieter.</span><span class="sxs-lookup"><span data-stu-id="8b016-211">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="8b016-212">Anbieter</span><span class="sxs-lookup"><span data-stu-id="8b016-212">Provider</span></span> | <span data-ttu-id="8b016-213">Bereitstellung der Konfiguration über&hellip;</span><span class="sxs-lookup"><span data-stu-id="8b016-213">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="8b016-214">[Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration) (Thema *Sicherheit*)</span><span class="sxs-lookup"><span data-stu-id="8b016-214">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="8b016-215">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8b016-215">Azure Key Vault</span></span> |
| <span data-ttu-id="8b016-216">[Azure App Configuration-Anbieter](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure-Dokumentation)</span><span class="sxs-lookup"><span data-stu-id="8b016-216">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="8b016-217">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="8b016-217">Azure App Configuration</span></span> |
| [<span data-ttu-id="8b016-218">Befehlszeilen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8b016-218">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="8b016-219">Befehlszeilenparameter</span><span class="sxs-lookup"><span data-stu-id="8b016-219">Command-line parameters</span></span> |
| [<span data-ttu-id="8b016-220">Benutzerdefinierter Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8b016-220">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="8b016-221">Benutzerdefinierte Quelle</span><span class="sxs-lookup"><span data-stu-id="8b016-221">Custom source</span></span> |
| [<span data-ttu-id="8b016-222">Umgebungsvariablen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8b016-222">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="8b016-223">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="8b016-223">Environment variables</span></span> |
| [<span data-ttu-id="8b016-224">Dateikonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8b016-224">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="8b016-225">Dateien (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="8b016-225">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="8b016-226">Schlüssel-pro-Datei-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8b016-226">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="8b016-227">Verzeichnisdateien</span><span class="sxs-lookup"><span data-stu-id="8b016-227">Directory files</span></span> |
| [<span data-ttu-id="8b016-228">Speicherkonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8b016-228">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="8b016-229">In-Memory-Sammlungen</span><span class="sxs-lookup"><span data-stu-id="8b016-229">In-memory collections</span></span> |
| <span data-ttu-id="8b016-230">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) (Thema *Sicherheit*)</span><span class="sxs-lookup"><span data-stu-id="8b016-230">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="8b016-231">Datei im Benutzerprofilverzeichnis</span><span class="sxs-lookup"><span data-stu-id="8b016-231">File in the user profile directory</span></span> |

<span data-ttu-id="8b016-232">Konfigurationsquellen werden beim Start in der Reihenfolge gelesen, in der ihre Konfigurationsanbieter angegeben sind.</span><span class="sxs-lookup"><span data-stu-id="8b016-232">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="8b016-233">Die in diesem Thema beschriebenen Konfigurationsanbieter werden in alphabetischer Reihenfolge beschrieben, nicht in der Reihenfolge, in der der Code sie anordnet.</span><span class="sxs-lookup"><span data-stu-id="8b016-233">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="8b016-234">Ordnen Sie die Konfigurationsanbieter im Code so an, dass sie den Prioritäten für die zugrunde liegenden Konfigurationsquellen entsprechen, die für die App erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="8b016-234">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="8b016-235">Eine typische Konfigurationsanbietersequenz ist:</span><span class="sxs-lookup"><span data-stu-id="8b016-235">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="8b016-236">Dateien (*appsettings.json*, *appsettings.{Environment}.json*, wobei `{Environment}` die aktuelle Hostingumgebung der App ist)</span><span class="sxs-lookup"><span data-stu-id="8b016-236">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="8b016-237">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="8b016-237">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="8b016-238">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) (nur Entwicklungsumgebung)</span><span class="sxs-lookup"><span data-stu-id="8b016-238">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="8b016-239">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="8b016-239">Environment variables</span></span>
1. <span data-ttu-id="8b016-240">Befehlszeilenargumente</span><span class="sxs-lookup"><span data-stu-id="8b016-240">Command-line arguments</span></span>

<span data-ttu-id="8b016-241">In der Regel werden Befehlszeilen-Konfigurationsanbieter in einer Reihe von Anbietern an letzter Stelle positioniert, damit Befehlszeilenargumente die von anderen Anbietern festgelegte Konfiguration überschreiben können.</span><span class="sxs-lookup"><span data-stu-id="8b016-241">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="8b016-242">Die vorhergehende Anbieterfolge wird verwendet, wenn Sie einen neuen Host-Generator mit `CreateDefaultBuilder` initialisieren.</span><span class="sxs-lookup"><span data-stu-id="8b016-242">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8b016-243">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="8b016-243">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="configure-the-host-builder-with-configurehostconfiguration"></a><span data-ttu-id="8b016-244">Konfigurieren des Host-Generators mit ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="8b016-244">Configure the host builder with ConfigureHostConfiguration</span></span>

<span data-ttu-id="8b016-245">Um den Host-Generator zu konfigurieren, rufen Sie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> auf und geben die Konfiguration an.</span><span class="sxs-lookup"><span data-stu-id="8b016-245">To configure the host builder, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> and supply the configuration.</span></span> <span data-ttu-id="8b016-246">`ConfigureHostConfiguration` wird verwendet, um das <xref:Microsoft.Extensions.Hosting.IHostEnvironment> für die spätere Verwendung im Buildprozess zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="8b016-246">`ConfigureHostConfiguration` is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> for use later in the build process.</span></span> <span data-ttu-id="8b016-247">`ConfigureHostConfiguration` kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="8b016-247">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span>

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

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="8b016-248">Konfigurieren des Host-Generators mit UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="8b016-248">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="8b016-249">Um den Host-Generator zu konfigurieren, rufen Sie <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> mit der Konfiguration für den Host-Generator auf.</span><span class="sxs-lookup"><span data-stu-id="8b016-249">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

::: moniker-end

## <a name="configureappconfiguration"></a><span data-ttu-id="8b016-250">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="8b016-250">ConfigureAppConfiguration</span></span>

<span data-ttu-id="8b016-251">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfigurationsanbieter der App zusätzlich zu denen anzugeben, die automatisch von `CreateDefaultBuilder` hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="8b016-251">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="8b016-252">Überschreiben der vorherigen Konfiguration mit Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="8b016-252">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="8b016-253">Um die App-Konfiguration bereitzustellen, die mit Befehlszeilenargumenten überschrieben werden kann, rufen Sie `AddCommandLine` zuletzt auf:</span><span class="sxs-lookup"><span data-stu-id="8b016-253">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="8b016-254">Entfernen von durch CreateDefaultBuilder hinzugefügten Anbietern</span><span class="sxs-lookup"><span data-stu-id="8b016-254">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="8b016-255">Rufen Sie zunächst [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) in [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) auf, um die durch `CreateDefaultBuilder` hinzugefügten Anbieter zu entfernen:</span><span class="sxs-lookup"><span data-stu-id="8b016-255">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="8b016-256">Verarbeiten der Konfiguration während des App-Starts</span><span class="sxs-lookup"><span data-stu-id="8b016-256">Consume configuration during app startup</span></span>

<span data-ttu-id="8b016-257">Die für die App in der `ConfigureAppConfiguration`-Methode angegebene Konfiguration, einschließlich `Startup.ConfigureServices`, ist während des Starts der App verfügbar.</span><span class="sxs-lookup"><span data-stu-id="8b016-257">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8b016-258">Weitere Informationen finden Sie im Abschnitt [Hinzufügen von Konfigurationen aus einer externen Assembly](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="8b016-258">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="8b016-259">Befehlszeilen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8b016-259">Command-line Configuration Provider</span></span>

<span data-ttu-id="8b016-260"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren des Befehlszeilenarguments.</span><span class="sxs-lookup"><span data-stu-id="8b016-260">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="8b016-261">Um die Befehlszeilenkonfiguration zu aktivieren, wird die <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>-Erweiterungsmethode für eine Instanz von <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="8b016-261">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8b016-262">`AddCommandLine` wird automatisch aufgerufen, wenn `CreateDefaultBuilder(string [])` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="8b016-262">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="8b016-263">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="8b016-263">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8b016-264">`CreateDefaultBuilder` lädt außerdem:</span><span class="sxs-lookup"><span data-stu-id="8b016-264">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="8b016-265">Optionale Konfiguration aus den Dateien *appsettings.json* und *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="8b016-265">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="8b016-266">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) in der Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="8b016-266">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8b016-267">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="8b016-267">Environment variables.</span></span>

<span data-ttu-id="8b016-268">`CreateDefaultBuilder` fügt den Befehlszeilen-Konfigurationsanbieter zuletzt hinzu.</span><span class="sxs-lookup"><span data-stu-id="8b016-268">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="8b016-269">Während der Laufzeit übergebene Befehlszeilenargumente überschreiben die von anderen Anbietern festgelegte Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="8b016-269">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="8b016-270">`CreateDefaultBuilder` wird aktiv, wenn der Host erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="8b016-270">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="8b016-271">Deswegen kann die durch `CreateDefaultBuilder` aktivierte Befehlszeilenkonfiguration die Konfiguration des Hosts beeinflussen.</span><span class="sxs-lookup"><span data-stu-id="8b016-271">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="8b016-272">Für Apps, die auf den ASP.NET Core-Vorlagen basieren, wurde `AddCommandLine` bereits von `CreateDefaultBuilder` aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="8b016-272">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8b016-273">Um weitere Konfigurationsanbieter hinzuzufügen und die Möglichkeit einer Überschreibung der Konfiguration dieser Anbieter durch Befehlszeilenargumente beizubehalten, rufen Sie die zusätzlichen Anbieter in `ConfigureAppConfiguration` und zuletzt `AddCommandLine` auf.</span><span class="sxs-lookup"><span data-stu-id="8b016-273">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="8b016-274">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="8b016-274">**Example**</span></span>

<span data-ttu-id="8b016-275">Die Beispiel-App nutzt zum Erstellen des Hosts die statische Hilfsmethode `CreateDefaultBuilder`, die einen Aufruf von <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> enthält.</span><span class="sxs-lookup"><span data-stu-id="8b016-275">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="8b016-276">Öffnen Sie im Projektverzeichnis eine Eingabeaufforderung.</span><span class="sxs-lookup"><span data-stu-id="8b016-276">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="8b016-277">Geben Sie ein Befehlszeilenargument für den `dotnet run`-Befehl an, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="8b016-277">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="8b016-278">Wenn die App ausgeführt wird, öffnen Sie einen Browser für die App unter `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="8b016-278">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="8b016-279">Beachten Sie, dass die Ausgabe das Schlüssel-Wert-Paar für das an `dotnet run` übergebene Konfigurations-Befehlszeilenargument enthält.</span><span class="sxs-lookup"><span data-stu-id="8b016-279">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="8b016-280">Argumente</span><span class="sxs-lookup"><span data-stu-id="8b016-280">Arguments</span></span>

<span data-ttu-id="8b016-281">Der Wert muss einem Gleichheitszeichen (`=`) folgen, oder der Schlüssel muss ein Präfix (`--` oder `/`) haben, wenn der Wert einem Leerzeichen folgt.</span><span class="sxs-lookup"><span data-stu-id="8b016-281">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="8b016-282">Der Wert ist nicht erforderlich, wenn ein Gleichheitszeichen verwendet wird (z. B. `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="8b016-282">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="8b016-283">Schlüsselpräfix</span><span class="sxs-lookup"><span data-stu-id="8b016-283">Key prefix</span></span>               | <span data-ttu-id="8b016-284">Beispiel</span><span class="sxs-lookup"><span data-stu-id="8b016-284">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="8b016-285">Ohne Präfix</span><span class="sxs-lookup"><span data-stu-id="8b016-285">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="8b016-286">Zwei Gedankenstriche (`--`)</span><span class="sxs-lookup"><span data-stu-id="8b016-286">Two dashes (`--`)</span></span>        | <span data-ttu-id="8b016-287">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="8b016-287">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="8b016-288">Schrägstrich (`/`)</span><span class="sxs-lookup"><span data-stu-id="8b016-288">Forward slash (`/`)</span></span>      | <span data-ttu-id="8b016-289">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="8b016-289">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="8b016-290">Kombinieren Sie in einem Befehl nicht Schlüssel-Wert-Paare, die ein Gleichheitszeichen verwenden, mit Schlüssel-Wert-Paaren, die ein Leerzeichen verwenden.</span><span class="sxs-lookup"><span data-stu-id="8b016-290">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="8b016-291">Beispielbefehle:</span><span class="sxs-lookup"><span data-stu-id="8b016-291">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="8b016-292">Switchmappings</span><span class="sxs-lookup"><span data-stu-id="8b016-292">Switch mappings</span></span>

<span data-ttu-id="8b016-293">Switchmappings erlauben das Angeben einer Logik zum Ersetzen von Schlüsselnamen.</span><span class="sxs-lookup"><span data-stu-id="8b016-293">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="8b016-294">Wenn Sie die Konfiguration manuell mit <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> erstellen, können Sie ein Wörterbuch der Switchersetzungen für die <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>-Methode bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="8b016-294">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="8b016-295">Wenn das Switchmappingwörterbuch verwendet wird, wird das Wörterbuch für Schlüssel, die dem von einem Befehlszeilenargument angegebenen Schlüssel entsprechen, überprüft.</span><span class="sxs-lookup"><span data-stu-id="8b016-295">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="8b016-296">Wenn der Befehlszeilenschlüssel im Wörterbuch gefunden wird, wird der Wörterbuchwert (der Schlüsselersatz) zum Festlegen des Schlüssel-Wert-Paares in der App-Konfiguration zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="8b016-296">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="8b016-297">Ein Switchmapping ist für jeden Befehlszeilenschlüssel erforderlich, dem ein einzelner Gedankenstrich (`-`) vorangestellt ist.</span><span class="sxs-lookup"><span data-stu-id="8b016-297">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="8b016-298">Regeln für Schlüssel von Switchmappingwörterbüchern:</span><span class="sxs-lookup"><span data-stu-id="8b016-298">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="8b016-299">Switchmappings müssen mit einem Gedankenstrich (`-`) oder zwei Gedankenstrichen (`--`) beginnen.</span><span class="sxs-lookup"><span data-stu-id="8b016-299">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="8b016-300">Das Switchmappingwörterbuch darf keine doppelten Schlüssel enthalten.</span><span class="sxs-lookup"><span data-stu-id="8b016-300">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="8b016-301">Erstellen Sie ein Switchmappingwörterbuch.</span><span class="sxs-lookup"><span data-stu-id="8b016-301">Create a switch mappings dictionary.</span></span> <span data-ttu-id="8b016-302">Im folgenden Beispiel werden zwei Switchmappings erstellt:</span><span class="sxs-lookup"><span data-stu-id="8b016-302">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="8b016-303">Wenn der Host erstellt wird, rufen Sie `AddCommandLine` mit dem Switchmappingwörterbuch auf:</span><span class="sxs-lookup"><span data-stu-id="8b016-303">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="8b016-304">Bei Apps, die Switchmappings verwenden, sollten im `CreateDefaultBuilder`-Aufruf keine Argumente übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="8b016-304">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="8b016-305">Der Aufruf von `CreateDefaultBuilder` der `AddCommandLine`-Methode umfasst keine zugeordneten Switches, und das Switchmappingwörterbuch kann nicht an `CreateDefaultBuilder` übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="8b016-305">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8b016-306">Die Lösung besteht nicht darin, die Argumente an `CreateDefaultBuilder` zu übergeben, sondern der Methode `AddCommandLine` der `ConfigurationBuilder`-Methode zu erlauben, sowohl die Argumente als auch das Switchmappingwörterbuch zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="8b016-306">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="8b016-307">Das erstellte Switchmappingwörterbuch enthält die in der folgenden Tabelle gezeigten Daten.</span><span class="sxs-lookup"><span data-stu-id="8b016-307">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="8b016-308">Key</span><span class="sxs-lookup"><span data-stu-id="8b016-308">Key</span></span>       | <span data-ttu-id="8b016-309">Wert</span><span class="sxs-lookup"><span data-stu-id="8b016-309">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="8b016-310">Wenn Switches zugeordnete Schlüssel beim Start der App verwendet werden, erhält die Konfiguration den Konfigurationswert auf dem vom Wörterbuch bereitgestellten Schlüssel:</span><span class="sxs-lookup"><span data-stu-id="8b016-310">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="8b016-311">Nach dem Ausführen des obigen Befehls enthält die Konfiguration die in der folgenden Tabelle angegebenen Werte.</span><span class="sxs-lookup"><span data-stu-id="8b016-311">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="8b016-312">Key</span><span class="sxs-lookup"><span data-stu-id="8b016-312">Key</span></span>               | <span data-ttu-id="8b016-313">Wert</span><span class="sxs-lookup"><span data-stu-id="8b016-313">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="8b016-314">Umgebungsvariablen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8b016-314">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="8b016-315"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="8b016-315">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="8b016-316">Um die Umgebungsvariablenkonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="8b016-316">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="8b016-317">[Azure App Service](https://azure.microsoft.com/services/app-service/) ermöglicht Ihnen Umgebungsvariablen im Azure-Portal festzulegen, die die App-Konfiguration mit dem Konfigurationsanbieter für Umgebungsvariablen überschreiben können.</span><span class="sxs-lookup"><span data-stu-id="8b016-317">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="8b016-318">Weitere Informationen finden Sie unter [Azure-Apps: Überschreiben der App-Konfiguration im Azure-Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="8b016-318">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8b016-319">`AddEnvironmentVariables` wird zum Laden von Umgebungsvariablen verwendet, die das Präfix `DOTNET_` für die [Hostkonfiguration](#host-versus-app-configuration) aufweisen, wenn ein neuer Host-Generator mit dem [generischen Host](xref:fundamentals/host/generic-host) initialisiert und `CreateDefaultBuilder` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="8b016-319">`AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Generic Host](xref:fundamentals/host/generic-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="8b016-320">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="8b016-320">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8b016-321">`AddEnvironmentVariables` wird zum Laden von Umgebungsvariablen verwendet, die das Präfix `ASPNETCORE_` für die [Hostkonfiguration](#host-versus-app-configuration) aufweisen, wenn ein neuer Host-Generator mit dem [Webhost](xref:fundamentals/host/web-host) initialisiert und `CreateDefaultBuilder` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="8b016-321">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="8b016-322">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="8b016-322">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

<span data-ttu-id="8b016-323">`CreateDefaultBuilder` lädt außerdem:</span><span class="sxs-lookup"><span data-stu-id="8b016-323">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="8b016-324">App-Konfiguration über Umgebungsvariablen ohne Präfix durch Aufruf von `AddEnvironmentVariables` ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="8b016-324">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="8b016-325">Optionale Konfiguration aus den Dateien *appsettings.json* und *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="8b016-325">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="8b016-326">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) in der Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="8b016-326">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8b016-327">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="8b016-327">Command-line arguments.</span></span>

<span data-ttu-id="8b016-328">Der Umgebungsvariablen-Konfigurationsanbieter wird aufgerufen, nachdem die Konfiguration aus Benutzergeheimnissen und *appsettings*-Dateien erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="8b016-328">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="8b016-329">Das Aufrufen des Anbieters an dieser Stelle ermöglicht den während der Laufzeit gelesenen Umgebungsvariablen, die von Benutzergeheimnissen und *appsettings*-Dateien festgelegte Konfiguration zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="8b016-329">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="8b016-330">Wenn Sie App-Konfigurationen aus zusätzlichen Umgebungsvariablen bereitstellen müssen, rufen Sie die zusätzlichen Anbieter der App in `ConfigureAppConfiguration` auf, und rufen Sie `AddEnvironmentVariables` mit dem Präfix auf:</span><span class="sxs-lookup"><span data-stu-id="8b016-330">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="8b016-331">Rufen Sie `AddEnvironmentVariables` zuletzt auf, um Umgebungsvariablen mit dem angegebenen Präfix zuzulassen, um Werte von anderen Anbietern zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="8b016-331">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="8b016-332">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="8b016-332">**Example**</span></span>

<span data-ttu-id="8b016-333">Die Beispiel-App nutzt zum Erstellen des Hosts die statische Hilfsmethode `CreateDefaultBuilder`, die einen Aufruf von `AddEnvironmentVariables` enthält.</span><span class="sxs-lookup"><span data-stu-id="8b016-333">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="8b016-334">Führen Sie die Beispiel-App aus.</span><span class="sxs-lookup"><span data-stu-id="8b016-334">Run the sample app.</span></span> <span data-ttu-id="8b016-335">Öffnen Sie einen Browser für die App unter `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="8b016-335">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="8b016-336">Beachten Sie, dass die Ausgabe das Schlüssel-Wert-Paar für die Umgebungsvariable `ENVIRONMENT` enthält.</span><span class="sxs-lookup"><span data-stu-id="8b016-336">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="8b016-337">Der Wert entspricht der Umgebung, in der die App ausgeführt wird. In der Regel ist das `Development` bei lokaler Ausführung.</span><span class="sxs-lookup"><span data-stu-id="8b016-337">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="8b016-338">Um die Liste der von der App gerenderten Umgebungsvariablen kurz zu halten, filtert die App Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="8b016-338">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="8b016-339">Weitere Informationen finden Sie in der Datei *Pages/Index.cshtml.cs* der Beispiel-App.</span><span class="sxs-lookup"><span data-stu-id="8b016-339">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="8b016-340">Wenn Sie alle für die App verfügbaren Umgebungsvariablen verfügbar machen möchten, ändern Sie `FilteredConfiguration` in *Pages/Index.cshtml.cs* wie folgt:</span><span class="sxs-lookup"><span data-stu-id="8b016-340">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="8b016-341">Präfixe</span><span class="sxs-lookup"><span data-stu-id="8b016-341">Prefixes</span></span>

<span data-ttu-id="8b016-342">Umgebungsvariablen, die in die Konfiguration der App geladen werden, werden gefiltert, wenn Sie ein Präfix für die Methode `AddEnvironmentVariables` angeben.</span><span class="sxs-lookup"><span data-stu-id="8b016-342">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="8b016-343">Um beispielsweise Umgebungsvariablen nach dem Präfix `CUSTOM_` zu filtern, geben Sie das Präfix für den Konfigurationsanbieter an:</span><span class="sxs-lookup"><span data-stu-id="8b016-343">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="8b016-344">Das Präfix wird beim Erstellen der Schlüssel-Wert-Paare der Konfiguration entfernt.</span><span class="sxs-lookup"><span data-stu-id="8b016-344">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="8b016-345">Beim Erstellen des Host-Generators wird die Hostkonfiguration durch Umgebungsvariablen bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="8b016-345">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="8b016-346">Weitere Informationen zu dem Präfix, das für diese Umgebungsvariablen verwendet wird, finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="8b016-346">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8b016-347">**Präfixe für Verbindungszeichenfolgen**</span><span class="sxs-lookup"><span data-stu-id="8b016-347">**Connection string prefixes**</span></span>

<span data-ttu-id="8b016-348">Die Konfigurations-API verfügt über spezielle Verarbeitungsregeln für vier Umgebungsvariablen für Verbindungszeichenfolgen, die beim Konfigurieren von Azure-Verbindungszeichenfolgen für die App-Umgebung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="8b016-348">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="8b016-349">Umgebungsvariablen mit Präfixen, die in der Tabelle aufgeführt werden, werden in die App geladen, wenn für `AddEnvironmentVariables` kein Präfix angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="8b016-349">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="8b016-350">Präfix für Verbindungszeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="8b016-350">Connection string prefix</span></span> | <span data-ttu-id="8b016-351">Anbieter</span><span class="sxs-lookup"><span data-stu-id="8b016-351">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="8b016-352">Benutzerdefinierter Anbieter</span><span class="sxs-lookup"><span data-stu-id="8b016-352">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="8b016-353">MySQL</span><span class="sxs-lookup"><span data-stu-id="8b016-353">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="8b016-354">Azure SQL-Datenbank</span><span class="sxs-lookup"><span data-stu-id="8b016-354">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="8b016-355">SQL Server</span><span class="sxs-lookup"><span data-stu-id="8b016-355">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="8b016-356">Wenn eine Umgebungsvariable entdeckt und mit einem der vier Präfixe aus der Tabelle in die Konfiguration geladen wird, tritt Folgendes ein:</span><span class="sxs-lookup"><span data-stu-id="8b016-356">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="8b016-357">Der Konfigurationsschlüssel wird durch Entfernen des Umgebungsvariablenpräfixes und Hinzufügen eines Konfigurationsschlüsselabschnitts (`ConnectionStrings`) erstellt.</span><span class="sxs-lookup"><span data-stu-id="8b016-357">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="8b016-358">Ein neues Konfigurations-Schlüssel-Wert-Paar wird erstellt, das den Datenbankverbindungsanbieter repräsentiert (mit Ausnahme des Präfixes `CUSTOMCONNSTR_`, für das kein Anbieter angegeben ist).</span><span class="sxs-lookup"><span data-stu-id="8b016-358">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="8b016-359">Umgebungsvariablenschlüssel</span><span class="sxs-lookup"><span data-stu-id="8b016-359">Environment variable key</span></span> | <span data-ttu-id="8b016-360">Konvertierter Konfigurationsschlüssel</span><span class="sxs-lookup"><span data-stu-id="8b016-360">Converted configuration key</span></span> | <span data-ttu-id="8b016-361">Anbieterkonfigurationseintrag</span><span class="sxs-lookup"><span data-stu-id="8b016-361">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | <span data-ttu-id="8b016-362">Konfigurationseintrag wurde nicht erstellt.</span><span class="sxs-lookup"><span data-stu-id="8b016-362">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | <span data-ttu-id="8b016-363">Schlüssel: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="8b016-363">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="8b016-364">Wert: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="8b016-364">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | <span data-ttu-id="8b016-365">Schlüssel: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="8b016-365">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="8b016-366">Wert: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="8b016-366">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | <span data-ttu-id="8b016-367">Schlüssel: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="8b016-367">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="8b016-368">Wert: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="8b016-368">Value: `System.Data.SqlClient`</span></span>  |

## <a name="file-configuration-provider"></a><span data-ttu-id="8b016-369">Dateikonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8b016-369">File Configuration Provider</span></span>

<span data-ttu-id="8b016-370"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> ist die Basisklasse für das Laden einer Konfiguration aus dem Dateisystem.</span><span class="sxs-lookup"><span data-stu-id="8b016-370"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="8b016-371">Die folgenden Konfigurationsanbieter sind für bestimmte Dateitypen vorgesehen:</span><span class="sxs-lookup"><span data-stu-id="8b016-371">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="8b016-372">INI-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8b016-372">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="8b016-373">JSON-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8b016-373">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="8b016-374">XML-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8b016-374">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="8b016-375">INI-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8b016-375">INI Configuration Provider</span></span>

<span data-ttu-id="8b016-376"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der INI-Datei.</span><span class="sxs-lookup"><span data-stu-id="8b016-376">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="8b016-377">Um die INI-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="8b016-377">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8b016-378">Der Doppelpunkt kann in einer INI-Dateikonfiguration als Abschnittstrennzeichen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="8b016-378">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="8b016-379">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="8b016-379">Overloads permit specifying:</span></span>

* <span data-ttu-id="8b016-380">Ob die Datei optional ist</span><span class="sxs-lookup"><span data-stu-id="8b016-380">Whether the file is optional.</span></span>
* <span data-ttu-id="8b016-381">Ob die Konfiguration bei Dateiänderungen neu geladen wird</span><span class="sxs-lookup"><span data-stu-id="8b016-381">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="8b016-382">Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird</span><span class="sxs-lookup"><span data-stu-id="8b016-382">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="8b016-383">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="8b016-383">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="8b016-384">Ein allgemeines Beispiel einer INI-Konfigurationsdatei:</span><span class="sxs-lookup"><span data-stu-id="8b016-384">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="8b016-385">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="8b016-385">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8b016-386">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8b016-386">section0:key0</span></span>
* <span data-ttu-id="8b016-387">section0:key1</span><span class="sxs-lookup"><span data-stu-id="8b016-387">section0:key1</span></span>
* <span data-ttu-id="8b016-388">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="8b016-388">section1:subsection:key</span></span>
* <span data-ttu-id="8b016-389">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="8b016-389">section2:subsection0:key</span></span>
* <span data-ttu-id="8b016-390">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="8b016-390">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="8b016-391">JSON-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8b016-391">JSON Configuration Provider</span></span>

<span data-ttu-id="8b016-392"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der JSON-Datei.</span><span class="sxs-lookup"><span data-stu-id="8b016-392">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="8b016-393">Um die JSON-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="8b016-393">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8b016-394">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="8b016-394">Overloads permit specifying:</span></span>

* <span data-ttu-id="8b016-395">Ob die Datei optional ist</span><span class="sxs-lookup"><span data-stu-id="8b016-395">Whether the file is optional.</span></span>
* <span data-ttu-id="8b016-396">Ob die Konfiguration bei Dateiänderungen neu geladen wird</span><span class="sxs-lookup"><span data-stu-id="8b016-396">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="8b016-397">Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird</span><span class="sxs-lookup"><span data-stu-id="8b016-397">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="8b016-398">`AddJsonFile` wird automatisch zweimal aufgerufen, wenn Sie einen neuen Host-Generator mit `CreateDefaultBuilder` initialisieren.</span><span class="sxs-lookup"><span data-stu-id="8b016-398">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="8b016-399">Die Methode wird aufgerufen, um die Konfiguration aus folgenden Dateien zu laden:</span><span class="sxs-lookup"><span data-stu-id="8b016-399">The method is called to load configuration from:</span></span>

* <span data-ttu-id="8b016-400">*appsettings.json* &ndash; Diese Datei wird zuerst gelesen.</span><span class="sxs-lookup"><span data-stu-id="8b016-400">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="8b016-401">Die Umgebungsversion der Datei kann die Werte der Datei *appsettings.json* überschreiben.</span><span class="sxs-lookup"><span data-stu-id="8b016-401">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="8b016-402">*appsettings.{Environment}.json* &ndash; Die Umgebungsversion der Datei wird basierend auf [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) geladen.</span><span class="sxs-lookup"><span data-stu-id="8b016-402">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="8b016-403">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="8b016-403">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="8b016-404">`CreateDefaultBuilder` lädt außerdem:</span><span class="sxs-lookup"><span data-stu-id="8b016-404">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="8b016-405">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="8b016-405">Environment variables.</span></span>
* <span data-ttu-id="8b016-406">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) in der Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="8b016-406">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="8b016-407">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="8b016-407">Command-line arguments.</span></span>

<span data-ttu-id="8b016-408">Der JSON-Konfigurationsanbieter wird zuerst eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="8b016-408">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="8b016-409">Daher überschreiben Benutzergeheimnisse, Umgebungsvariablen und Befehlszeilenargumente die von *appsettings*-Dateien festgelegte Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="8b016-409">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="8b016-410">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um Konfiguration der App für andere Dateien als *appsettings.json* und *appsettings.{Environment}.json* anzugeben:</span><span class="sxs-lookup"><span data-stu-id="8b016-410">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="8b016-411">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="8b016-411">**Example**</span></span>

<span data-ttu-id="8b016-412">Die Beispiel-App nutzt zum Erstellen des Hosts die statische Hilfsmethode `CreateDefaultBuilder`, die zwei Aufrufe von `AddJsonFile` enthält:</span><span class="sxs-lookup"><span data-stu-id="8b016-412">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="8b016-413">Der erste Aufruf auf `AddJsonFile` lädt die Konfiguration aus *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="8b016-413">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="8b016-414">Der zweite Aufruf auf `AddJsonFile` lädt die Konfiguration aus *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="8b016-414">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="8b016-415">Die folgende Datei wird für *appsettings.Development.json* in der Beispiel-App geladen:</span><span class="sxs-lookup"><span data-stu-id="8b016-415">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/3.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="8b016-416">Führen Sie die Beispiel-App aus.</span><span class="sxs-lookup"><span data-stu-id="8b016-416">Run the sample app.</span></span> <span data-ttu-id="8b016-417">Öffnen Sie einen Browser für die App unter `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="8b016-417">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="8b016-418">Die Ausgabe enthält Schlüssel-Wert-Paare für die Konfiguration basierend auf der Umgebung der App.</span><span class="sxs-lookup"><span data-stu-id="8b016-418">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="8b016-419">Die Protokollebene für den Schlüssel `Logging:LogLevel:Default` ist `Debug`, wenn die App in der Entwicklungsumgebung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="8b016-419">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="8b016-420">Führen Sie die Beispiel-App noch mal in der Produktionsumgebung aus:</span><span class="sxs-lookup"><span data-stu-id="8b016-420">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="8b016-421">Öffnen Sie die Datei *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="8b016-421">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="8b016-422">Ändern Sie im `ConfigurationSample`-Profil den Wert der Umgebungsvariablen `ASPNETCORE_ENVIRONMENT` in `Production`.</span><span class="sxs-lookup"><span data-stu-id="8b016-422">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="8b016-423">Speichern Sie die Datei, und führen Sie die App mit `dotnet run` in einer Befehlsshell aus.</span><span class="sxs-lookup"><span data-stu-id="8b016-423">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="8b016-424">Die Einstellungen in der *appsettings.Development.json*-Datei setzen die Einstellungen in der Datei *appsettings.json* nicht länger außer Kraft.</span><span class="sxs-lookup"><span data-stu-id="8b016-424">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="8b016-425">Die Protokollebene für den Schlüssel `Logging:LogLevel:Default` lautet `Information`.</span><span class="sxs-lookup"><span data-stu-id="8b016-425">The log level for the key `Logging:LogLevel:Default` is `Information`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="8b016-426">Der erste Aufruf auf `AddJsonFile` lädt die Konfiguration aus *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="8b016-426">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="8b016-427">Der zweite Aufruf auf `AddJsonFile` lädt die Konfiguration aus *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="8b016-427">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="8b016-428">Die folgende Datei wird für *appsettings.Development.json* in der Beispiel-App geladen:</span><span class="sxs-lookup"><span data-stu-id="8b016-428">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="8b016-429">Führen Sie die Beispiel-App aus.</span><span class="sxs-lookup"><span data-stu-id="8b016-429">Run the sample app.</span></span> <span data-ttu-id="8b016-430">Öffnen Sie einen Browser für die App unter `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="8b016-430">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="8b016-431">Die Ausgabe enthält Schlüssel-Wert-Paare für die Konfiguration basierend auf der Umgebung der App.</span><span class="sxs-lookup"><span data-stu-id="8b016-431">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="8b016-432">Die Protokollebene für den Schlüssel `Logging:LogLevel:Default` ist `Debug`, wenn die App in der Entwicklungsumgebung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="8b016-432">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="8b016-433">Führen Sie die Beispiel-App noch mal in der Produktionsumgebung aus:</span><span class="sxs-lookup"><span data-stu-id="8b016-433">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="8b016-434">Öffnen Sie die Datei *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="8b016-434">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="8b016-435">Ändern Sie im `ConfigurationSample`-Profil den Wert der Umgebungsvariablen `ASPNETCORE_ENVIRONMENT` in `Production`.</span><span class="sxs-lookup"><span data-stu-id="8b016-435">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="8b016-436">Speichern Sie die Datei, und führen Sie die App mit `dotnet run` in einer Befehlsshell aus.</span><span class="sxs-lookup"><span data-stu-id="8b016-436">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="8b016-437">Die Einstellungen in der *appsettings.Development.json*-Datei setzen die Einstellungen in der Datei *appsettings.json* nicht länger außer Kraft.</span><span class="sxs-lookup"><span data-stu-id="8b016-437">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="8b016-438">Die Protokollebene für den Schlüssel `Logging:LogLevel:Default` lautet `Warning`.</span><span class="sxs-lookup"><span data-stu-id="8b016-438">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

::: moniker-end

### <a name="xml-configuration-provider"></a><span data-ttu-id="8b016-439">XML-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8b016-439">XML Configuration Provider</span></span>

<span data-ttu-id="8b016-440"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der XML-Datei.</span><span class="sxs-lookup"><span data-stu-id="8b016-440">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="8b016-441">Um die XML-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="8b016-441">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8b016-442">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="8b016-442">Overloads permit specifying:</span></span>

* <span data-ttu-id="8b016-443">Ob die Datei optional ist</span><span class="sxs-lookup"><span data-stu-id="8b016-443">Whether the file is optional.</span></span>
* <span data-ttu-id="8b016-444">Ob die Konfiguration bei Dateiänderungen neu geladen wird</span><span class="sxs-lookup"><span data-stu-id="8b016-444">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="8b016-445">Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird</span><span class="sxs-lookup"><span data-stu-id="8b016-445">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="8b016-446">Der Stammknoten der Konfigurationsdatei wird beim Erstellen der Konfigurations-Schlüssel-Wert-Paare ignoriert.</span><span class="sxs-lookup"><span data-stu-id="8b016-446">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="8b016-447">Geben Sie keine Dokumenttypdefinition (DTD) und keinen Namespace in der Datei an.</span><span class="sxs-lookup"><span data-stu-id="8b016-447">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="8b016-448">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="8b016-448">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="8b016-449">XML-Konfigurationsdateien können unterschiedliche Elementnamen für wiederholte Abschnitte verwenden:</span><span class="sxs-lookup"><span data-stu-id="8b016-449">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="8b016-450">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="8b016-450">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8b016-451">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8b016-451">section0:key0</span></span>
* <span data-ttu-id="8b016-452">section0:key1</span><span class="sxs-lookup"><span data-stu-id="8b016-452">section0:key1</span></span>
* <span data-ttu-id="8b016-453">section1:key0</span><span class="sxs-lookup"><span data-stu-id="8b016-453">section1:key0</span></span>
* <span data-ttu-id="8b016-454">section1:key1</span><span class="sxs-lookup"><span data-stu-id="8b016-454">section1:key1</span></span>

<span data-ttu-id="8b016-455">Wiederholte Elemente mit den gleichen Elementnamen funktionieren, wenn das `name`-Attribut zur Unterscheidung der Elemente verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="8b016-455">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="8b016-456">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="8b016-456">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8b016-457">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="8b016-457">section:section0:key:key0</span></span>
* <span data-ttu-id="8b016-458">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="8b016-458">section:section0:key:key1</span></span>
* <span data-ttu-id="8b016-459">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="8b016-459">section:section1:key:key0</span></span>
* <span data-ttu-id="8b016-460">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="8b016-460">section:section1:key:key1</span></span>

<span data-ttu-id="8b016-461">Mit Attributen können Werte bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="8b016-461">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="8b016-462">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="8b016-462">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="8b016-463">key:attribute</span><span class="sxs-lookup"><span data-stu-id="8b016-463">key:attribute</span></span>
* <span data-ttu-id="8b016-464">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="8b016-464">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="8b016-465">Schlüssel-pro-Datei-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8b016-465">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="8b016-466"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> verwendet Verzeichnisdateien als Konfigurations-Schlüssel-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="8b016-466">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="8b016-467">Der Schlüssel ist der Dateiname.</span><span class="sxs-lookup"><span data-stu-id="8b016-467">The key is the file name.</span></span> <span data-ttu-id="8b016-468">Der Wert enthält den Inhalt der Datei.</span><span class="sxs-lookup"><span data-stu-id="8b016-468">The value contains the file's contents.</span></span> <span data-ttu-id="8b016-469">Der Schlüssel-pro-Datei-Konfigurationsanbieter wird in Docker-Hostingszenarien verwendet.</span><span class="sxs-lookup"><span data-stu-id="8b016-469">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="8b016-470">Um die Schlüssel-pro-Datei-Konfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="8b016-470">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="8b016-471">Der `directoryPath` zu den Dateien muss ein absoluter Pfad sein.</span><span class="sxs-lookup"><span data-stu-id="8b016-471">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="8b016-472">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="8b016-472">Overloads permit specifying:</span></span>

* <span data-ttu-id="8b016-473">Einen `Action<KeyPerFileConfigurationSource>`-Delegat, der die Quelle konfiguriert</span><span class="sxs-lookup"><span data-stu-id="8b016-473">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="8b016-474">Ob das Verzeichnis optional ist; und den Pfad zum Verzeichnis</span><span class="sxs-lookup"><span data-stu-id="8b016-474">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="8b016-475">Der doppelte Unterstrich (`__`) wird als Trennzeichen für Konfigurationsschlüssel in Dateinamen verwendet.</span><span class="sxs-lookup"><span data-stu-id="8b016-475">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="8b016-476">Der Dateiname `Logging__LogLevel__System` erzeugt z.B. den Konfigurationsschlüssel `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="8b016-476">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="8b016-477">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="8b016-477">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="8b016-478">Speicherkonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8b016-478">Memory Configuration Provider</span></span>

<span data-ttu-id="8b016-479"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> verwendet eine In-Memory-Sammlung für Konfigurations-Schlüssel-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="8b016-479">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="8b016-480">Um die In-Memory-Sammlungskonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="8b016-480">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="8b016-481">Der Konfigurationsanbieter kann mit `IEnumerable<KeyValuePair<String,String>>` initialisiert werden.</span><span class="sxs-lookup"><span data-stu-id="8b016-481">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="8b016-482">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben.</span><span class="sxs-lookup"><span data-stu-id="8b016-482">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="8b016-483">Im folgenden Beispiel wird ein Konfigurationswörterbuch erstellt:</span><span class="sxs-lookup"><span data-stu-id="8b016-483">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="8b016-484">Das Wörterbuch wird mit einem Aufruf von `AddInMemoryCollection` verwendet, um die Konfiguration bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="8b016-484">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="8b016-485">GetValue</span><span class="sxs-lookup"><span data-stu-id="8b016-485">GetValue</span></span>

<span data-ttu-id="8b016-486">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahiert einen einzelnen Wert aus der Konfiguration mit einem angegebenen Schlüssel und konvertiert ihn in den angegebenen Typ, der kein Auflistungstyp ist.</span><span class="sxs-lookup"><span data-stu-id="8b016-486">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="8b016-487">Eine Überladung akzeptiert einen Standardwert.</span><span class="sxs-lookup"><span data-stu-id="8b016-487">An overload accepts a default value.</span></span>

<span data-ttu-id="8b016-488">Im Beispiel unten geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="8b016-488">The following example:</span></span>

* <span data-ttu-id="8b016-489">Der Zeichenfolgenwert aus der Konfiguration wird mit dem Schlüssel `NumberKey` extrahiert.</span><span class="sxs-lookup"><span data-stu-id="8b016-489">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="8b016-490">Wenn `NumberKey` nicht in den Konfigurationsschlüsseln gefunden wird, wird der Standardwert `99` verwendet.</span><span class="sxs-lookup"><span data-stu-id="8b016-490">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="8b016-491">Der Typ für den Wert wird als `int` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="8b016-491">Types the value as an `int`.</span></span>
* <span data-ttu-id="8b016-492">Der Wert wird in der `NumberConfig`-Eigenschaft für die Verwendung durch die Seite gespeichert.</span><span class="sxs-lookup"><span data-stu-id="8b016-492">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="8b016-493">GetSection, GetChildren und Exists</span><span class="sxs-lookup"><span data-stu-id="8b016-493">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="8b016-494">Betrachten Sie für die folgenden Beispiele die JSON-Datei unten.</span><span class="sxs-lookup"><span data-stu-id="8b016-494">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="8b016-495">Vier Schlüssel befinden sich in zwei Abschnitten, von denen einer ein Paar Unterabschnitte enthält:</span><span class="sxs-lookup"><span data-stu-id="8b016-495">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="8b016-496">Wenn die Datei in die Konfiguration gelesen wird, werden die folgenden eindeutigen hierarchischen Schlüssel erstellt, um die Konfigurationswerte zu enthalten:</span><span class="sxs-lookup"><span data-stu-id="8b016-496">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="8b016-497">section0:key0</span><span class="sxs-lookup"><span data-stu-id="8b016-497">section0:key0</span></span>
* <span data-ttu-id="8b016-498">section0:key1</span><span class="sxs-lookup"><span data-stu-id="8b016-498">section0:key1</span></span>
* <span data-ttu-id="8b016-499">section1:key0</span><span class="sxs-lookup"><span data-stu-id="8b016-499">section1:key0</span></span>
* <span data-ttu-id="8b016-500">section1:key1</span><span class="sxs-lookup"><span data-stu-id="8b016-500">section1:key1</span></span>
* <span data-ttu-id="8b016-501">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="8b016-501">section2:subsection0:key0</span></span>
* <span data-ttu-id="8b016-502">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="8b016-502">section2:subsection0:key1</span></span>
* <span data-ttu-id="8b016-503">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="8b016-503">section2:subsection1:key0</span></span>
* <span data-ttu-id="8b016-504">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="8b016-504">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="8b016-505">GetSection</span><span class="sxs-lookup"><span data-stu-id="8b016-505">GetSection</span></span>

<span data-ttu-id="8b016-506">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrahiert einen Konfigurationsunterabschnitt mit dem angegebenen Unterabschnittsschlüssel.</span><span class="sxs-lookup"><span data-stu-id="8b016-506">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="8b016-507">Um ein <xref:Microsoft.Extensions.Configuration.IConfigurationSection>-Element zurückzugeben, das nur die Schlüssel-Wert-Paare in `section1` enthält, rufen Sie `GetSection` auf, und geben Sie den Abschnittsnamen an:</span><span class="sxs-lookup"><span data-stu-id="8b016-507">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="8b016-508">Das Element `configSection` weist keinen Wert auf, nur einen Schlüssel und einen Pfad.</span><span class="sxs-lookup"><span data-stu-id="8b016-508">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="8b016-509">Um die Werte für Schlüssel in `section2:subsection0` zu erhalten, rufen Sie `GetSection` auf, und geben Sie den Abschnittspfad an:</span><span class="sxs-lookup"><span data-stu-id="8b016-509">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="8b016-510">`GetSection` gibt nie `null` zurück.</span><span class="sxs-lookup"><span data-stu-id="8b016-510">`GetSection` never returns `null`.</span></span> <span data-ttu-id="8b016-511">Wenn kein entsprechender Abschnitt gefunden wird, wird ein leeres `IConfigurationSection`-Element zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="8b016-511">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="8b016-512">Wenn `GetSection` einen entsprechenden Abschnitt zurückgibt, wird <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> nicht aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="8b016-512">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="8b016-513">Eine Eigenschaft <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> und <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> werden zurückgegeben, wenn der Abschnitt vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="8b016-513">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="8b016-514">GetChildren</span><span class="sxs-lookup"><span data-stu-id="8b016-514">GetChildren</span></span>

<span data-ttu-id="8b016-515">Ein Aufruf von [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) auf `section2` erhält `IEnumerable<IConfigurationSection>` mit folgenden Elementen:</span><span class="sxs-lookup"><span data-stu-id="8b016-515">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="8b016-516">Vorhanden</span><span class="sxs-lookup"><span data-stu-id="8b016-516">Exists</span></span>

<span data-ttu-id="8b016-517">Verwenden Sie [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) um zu bestimmen, ob ein Konfigurationsabschnitt vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="8b016-517">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="8b016-518">Im Falle der Beispieldaten ist `sectionExists``false`, weil in den Konfigurationsdaten kein Abschnitt `section2:subsection2` vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="8b016-518">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="8b016-519">Binden an eine Klasse</span><span class="sxs-lookup"><span data-stu-id="8b016-519">Bind to a class</span></span>

<span data-ttu-id="8b016-520">Die Konfiguration kann mit *Optionsmustern* an Klassen gebunden werden, die Gruppen von verwandten Einstellungen darstellen.</span><span class="sxs-lookup"><span data-stu-id="8b016-520">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="8b016-521">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="8b016-521">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="8b016-522">Konfigurationswerte werden als Zeichenfolgen zurückgegeben, doch das Aufrufen von <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> ermöglicht die Erstellung von [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object)-Objekten.</span><span class="sxs-lookup"><span data-stu-id="8b016-522">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span>

<span data-ttu-id="8b016-523">Die Beispiel-App enthält ein `Starship`-Modell (*Models/Starship.cs*):</span><span class="sxs-lookup"><span data-stu-id="8b016-523">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="8b016-524">Der Abschnitt `starship` der Datei *starship.json* erstellt die Konfiguration, wenn die Beispiel-App den JSON-Konfigurationsanbieter zum Laden der Konfiguration verwendet:</span><span class="sxs-lookup"><span data-stu-id="8b016-524">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/starship.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

::: moniker-end

<span data-ttu-id="8b016-525">Die folgenden Schlüssel-Wert-Paare werden für die Konfiguration erstellt:</span><span class="sxs-lookup"><span data-stu-id="8b016-525">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="8b016-526">Key</span><span class="sxs-lookup"><span data-stu-id="8b016-526">Key</span></span>                   | <span data-ttu-id="8b016-527">Wert</span><span class="sxs-lookup"><span data-stu-id="8b016-527">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="8b016-528">starship:name</span><span class="sxs-lookup"><span data-stu-id="8b016-528">starship:name</span></span>         | <span data-ttu-id="8b016-529">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="8b016-529">USS Enterprise</span></span>                                    |
| <span data-ttu-id="8b016-530">starship:registry</span><span class="sxs-lookup"><span data-stu-id="8b016-530">starship:registry</span></span>     | <span data-ttu-id="8b016-531">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="8b016-531">NCC-1701</span></span>                                          |
| <span data-ttu-id="8b016-532">starship:class</span><span class="sxs-lookup"><span data-stu-id="8b016-532">starship:class</span></span>        | <span data-ttu-id="8b016-533">Constitution</span><span class="sxs-lookup"><span data-stu-id="8b016-533">Constitution</span></span>                                      |
| <span data-ttu-id="8b016-534">starship:length</span><span class="sxs-lookup"><span data-stu-id="8b016-534">starship:length</span></span>       | <span data-ttu-id="8b016-535">304,8</span><span class="sxs-lookup"><span data-stu-id="8b016-535">304.8</span></span>                                             |
| <span data-ttu-id="8b016-536">starship:commissioned</span><span class="sxs-lookup"><span data-stu-id="8b016-536">starship:commissioned</span></span> | <span data-ttu-id="8b016-537">False</span><span class="sxs-lookup"><span data-stu-id="8b016-537">False</span></span>                                             |
| <span data-ttu-id="8b016-538">trademark</span><span class="sxs-lookup"><span data-stu-id="8b016-538">trademark</span></span>             | <span data-ttu-id="8b016-539">Paramount Pictures Corp. https://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="8b016-539">Paramount Pictures Corp. https://www.paramount.com</span></span> |

<span data-ttu-id="8b016-540">Die Beispiel-App ruft `GetSection` mit dem `starship`-Schlüssel auf.</span><span class="sxs-lookup"><span data-stu-id="8b016-540">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="8b016-541">Die `starship`-Schlüssel-Wert-Paaren sind isoliert.</span><span class="sxs-lookup"><span data-stu-id="8b016-541">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="8b016-542">Die `Bind`-Methode wird auf dem Unterabschnitt aufgerufen, der in einer Instanz der `Starship`-Klasse übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="8b016-542">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="8b016-543">Nach dem Binden der Instanzwerte wird die Instanz einer Eigenschaft zum Rendern zugewiesen:</span><span class="sxs-lookup"><span data-stu-id="8b016-543">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="8b016-544">Binden an ein Objektdiagramm</span><span class="sxs-lookup"><span data-stu-id="8b016-544">Bind to an object graph</span></span>

<span data-ttu-id="8b016-545"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> kann ein ganzes POCO-Objektdiagramm binden.</span><span class="sxs-lookup"><span data-stu-id="8b016-545"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span>

<span data-ttu-id="8b016-546">Das Beispiel enthält ein `TvShow`-Modell, dessen Objektdiagramm die Klassen `Metadata` und `Actors` enthält (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="8b016-546">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="8b016-547">Die Beispiel-App verfügt über eine *tvshow.xml*-Datei mit den Konfigurationsdaten:</span><span class="sxs-lookup"><span data-stu-id="8b016-547">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-xml[](index/samples/3.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

<span data-ttu-id="8b016-548">Die Konfiguration wird mit der `Bind`-Methode an das gesamte `TvShow`-Objektdiagramm gebunden.</span><span class="sxs-lookup"><span data-stu-id="8b016-548">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="8b016-549">Die gebundene Instanz ist einer Eigenschaft zum Rendern zugewiesen:</span><span class="sxs-lookup"><span data-stu-id="8b016-549">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="8b016-550">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) bindet den angegebenen Typ und gibt ihn zurück.</span><span class="sxs-lookup"><span data-stu-id="8b016-550">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="8b016-551">`Get<T>` ist praktischer als `Bind`.</span><span class="sxs-lookup"><span data-stu-id="8b016-551">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="8b016-552">Der folgende Code zeigt die Verwendung von `Get<T>` mit dem vorherigen Beispiel, sodass die gebundene Instanz direkt der Eigenschaft zum Rendern zugewiesen werden kann:</span><span class="sxs-lookup"><span data-stu-id="8b016-552">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="8b016-553">Binden eines Arrays an eine Klasse</span><span class="sxs-lookup"><span data-stu-id="8b016-553">Bind an array to a class</span></span>

<span data-ttu-id="8b016-554">*Die Beispiel-App veranschaulicht die in diesem Abschnitt erläuterten Konzepte.*</span><span class="sxs-lookup"><span data-stu-id="8b016-554">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="8b016-555"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> unterstützt das Binden von Arrays an Objekte mit Arrayindizes in Konfigurationsschlüsseln.</span><span class="sxs-lookup"><span data-stu-id="8b016-555">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="8b016-556">Jedes Arrayformat, das ein numerisches Schlüsselsegment (`:0:`, `:1:`, &hellip; `:{n}:`) verfügbar macht, kann ein Array an ein POCO-Klassenarray binden.</span><span class="sxs-lookup"><span data-stu-id="8b016-556">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="8b016-557">Das Binden ist standardmäßig möglich.</span><span class="sxs-lookup"><span data-stu-id="8b016-557">Binding is provided by convention.</span></span> <span data-ttu-id="8b016-558">Benutzerdefinierte Konfigurationsanbieter sind nicht erforderlich, um Arraybindung zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="8b016-558">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="8b016-559">**In-Memory-Arrayverarbeitung**</span><span class="sxs-lookup"><span data-stu-id="8b016-559">**In-memory array processing**</span></span>

<span data-ttu-id="8b016-560">Betrachten Sie die Konfigurationsschlüssel und -werte in der folgenden Tabelle.</span><span class="sxs-lookup"><span data-stu-id="8b016-560">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="8b016-561">Key</span><span class="sxs-lookup"><span data-stu-id="8b016-561">Key</span></span>             | <span data-ttu-id="8b016-562">Wert</span><span class="sxs-lookup"><span data-stu-id="8b016-562">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="8b016-563">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="8b016-563">array:entries:0</span></span> | <span data-ttu-id="8b016-564">value0</span><span class="sxs-lookup"><span data-stu-id="8b016-564">value0</span></span> |
| <span data-ttu-id="8b016-565">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="8b016-565">array:entries:1</span></span> | <span data-ttu-id="8b016-566">value1</span><span class="sxs-lookup"><span data-stu-id="8b016-566">value1</span></span> |
| <span data-ttu-id="8b016-567">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="8b016-567">array:entries:2</span></span> | <span data-ttu-id="8b016-568">value2</span><span class="sxs-lookup"><span data-stu-id="8b016-568">value2</span></span> |
| <span data-ttu-id="8b016-569">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="8b016-569">array:entries:4</span></span> | <span data-ttu-id="8b016-570">value4</span><span class="sxs-lookup"><span data-stu-id="8b016-570">value4</span></span> |
| <span data-ttu-id="8b016-571">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="8b016-571">array:entries:5</span></span> | <span data-ttu-id="8b016-572">value5</span><span class="sxs-lookup"><span data-stu-id="8b016-572">value5</span></span> |

<span data-ttu-id="8b016-573">Diese Schlüssel und Werte werden mit dem Speicherkonfigurationsanbieter in die Beispiel-App geladen:</span><span class="sxs-lookup"><span data-stu-id="8b016-573">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

::: moniker-end

<span data-ttu-id="8b016-574">Das Array überspringt einen Wert für Index &num;3.</span><span class="sxs-lookup"><span data-stu-id="8b016-574">The array skips a value for index &num;3.</span></span> <span data-ttu-id="8b016-575">Der Konfigurationsbinder kann weder NULL-Werte binden noch NULL-Einträge in gebundenen Objekten erstellen. Dies wird deutlich, wenn das Ergebnis der Bindung dieses Arrays an ein Objekt demonstriert wird.</span><span class="sxs-lookup"><span data-stu-id="8b016-575">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="8b016-576">In der Beispiel-App ist eine POCO-Klasse verfügbar, um die gebundenen Konfigurationsdaten zu enthalten:</span><span class="sxs-lookup"><span data-stu-id="8b016-576">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="8b016-577">Die Konfigurationsdaten werden an das Objekt gebunden:</span><span class="sxs-lookup"><span data-stu-id="8b016-577">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="8b016-578">Die [ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)-Syntax kann auch verwendet werden, wodurch ein kompakterer Code entsteht:</span><span class="sxs-lookup"><span data-stu-id="8b016-578">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

<span data-ttu-id="8b016-579">Das gebundene Objekt, eine Instanz von `ArrayExample`, empfängt die Arraydaten aus der Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="8b016-579">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="8b016-580">`ArrayExample.Entries`-Index</span><span class="sxs-lookup"><span data-stu-id="8b016-580">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="8b016-581">`ArrayExample.Entries`-Wert</span><span class="sxs-lookup"><span data-stu-id="8b016-581">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="8b016-582">0</span><span class="sxs-lookup"><span data-stu-id="8b016-582">0</span></span>                            | <span data-ttu-id="8b016-583">value0</span><span class="sxs-lookup"><span data-stu-id="8b016-583">value0</span></span>                       |
| <span data-ttu-id="8b016-584">1</span><span class="sxs-lookup"><span data-stu-id="8b016-584">1</span></span>                            | <span data-ttu-id="8b016-585">value1</span><span class="sxs-lookup"><span data-stu-id="8b016-585">value1</span></span>                       |
| <span data-ttu-id="8b016-586">2</span><span class="sxs-lookup"><span data-stu-id="8b016-586">2</span></span>                            | <span data-ttu-id="8b016-587">value2</span><span class="sxs-lookup"><span data-stu-id="8b016-587">value2</span></span>                       |
| <span data-ttu-id="8b016-588">3</span><span class="sxs-lookup"><span data-stu-id="8b016-588">3</span></span>                            | <span data-ttu-id="8b016-589">value4</span><span class="sxs-lookup"><span data-stu-id="8b016-589">value4</span></span>                       |
| <span data-ttu-id="8b016-590">4</span><span class="sxs-lookup"><span data-stu-id="8b016-590">4</span></span>                            | <span data-ttu-id="8b016-591">value5</span><span class="sxs-lookup"><span data-stu-id="8b016-591">value5</span></span>                       |

<span data-ttu-id="8b016-592">Index &num;3 im gebundenen Objekt enthält die Konfigurationsdaten für den `array:4`-Konfigurationsschlüssel und die Wert für `value4`.</span><span class="sxs-lookup"><span data-stu-id="8b016-592">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="8b016-593">Beim Binden von Konfigurationsdaten, die ein Array enthalten, werden die Arrayindizes in den Konfigurationsschlüsseln lediglich zum Durchlaufen der Konfigurationsdaten beim Erstellen des Objekts verwendet.</span><span class="sxs-lookup"><span data-stu-id="8b016-593">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="8b016-594">Ein NULL-Wert kann in den Konfigurationsdaten nicht beibehalten werden, und ein NULL-Eintrag wird nicht in einem gebundenen Objekt erstellt, wenn ein Array in Konfigurationsschlüsseln mindestens einen Index überspringt.</span><span class="sxs-lookup"><span data-stu-id="8b016-594">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="8b016-595">Das fehlende Konfigurationselement für Index &num;3 kann vor dem Binden an die `ArrayExample`Instanz von jedem Konfigurationsanbieter bereitgestellt werden, der das richtige Schlüssel-Wert-Paar in der Konfiguration erzeugt.</span><span class="sxs-lookup"><span data-stu-id="8b016-595">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="8b016-596">Wenn das Beispiel einen zusätzlichen JSON-Konfigurationsanbieter mit dem fehlenden Schlüssel-Wert-Paar enthält, entspricht `ArrayExample.Entries` dem kompletten Konfigurationsarray:</span><span class="sxs-lookup"><span data-stu-id="8b016-596">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="8b016-597">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="8b016-597">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="8b016-598">In `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="8b016-598">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="8b016-599">Das Schlüssel-Wert-Paar in der Tabelle wird in die Konfiguration geladen.</span><span class="sxs-lookup"><span data-stu-id="8b016-599">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="8b016-600">Key</span><span class="sxs-lookup"><span data-stu-id="8b016-600">Key</span></span>             | <span data-ttu-id="8b016-601">Wert</span><span class="sxs-lookup"><span data-stu-id="8b016-601">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="8b016-602">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="8b016-602">array:entries:3</span></span> | <span data-ttu-id="8b016-603">value3</span><span class="sxs-lookup"><span data-stu-id="8b016-603">value3</span></span> |

<span data-ttu-id="8b016-604">Wenn die `ArrayExample`-Klasseninstanz gebunden wird, nachdem der JSON-Konfigurationsanbieter den Eintrag für Index &num;3 enthält, enthält das `ArrayExample.Entries`-Array den Wert.</span><span class="sxs-lookup"><span data-stu-id="8b016-604">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="8b016-605">`ArrayExample.Entries`-Index</span><span class="sxs-lookup"><span data-stu-id="8b016-605">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="8b016-606">`ArrayExample.Entries`-Wert</span><span class="sxs-lookup"><span data-stu-id="8b016-606">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="8b016-607">0</span><span class="sxs-lookup"><span data-stu-id="8b016-607">0</span></span>                            | <span data-ttu-id="8b016-608">value0</span><span class="sxs-lookup"><span data-stu-id="8b016-608">value0</span></span>                       |
| <span data-ttu-id="8b016-609">1</span><span class="sxs-lookup"><span data-stu-id="8b016-609">1</span></span>                            | <span data-ttu-id="8b016-610">value1</span><span class="sxs-lookup"><span data-stu-id="8b016-610">value1</span></span>                       |
| <span data-ttu-id="8b016-611">2</span><span class="sxs-lookup"><span data-stu-id="8b016-611">2</span></span>                            | <span data-ttu-id="8b016-612">value2</span><span class="sxs-lookup"><span data-stu-id="8b016-612">value2</span></span>                       |
| <span data-ttu-id="8b016-613">3</span><span class="sxs-lookup"><span data-stu-id="8b016-613">3</span></span>                            | <span data-ttu-id="8b016-614">value3</span><span class="sxs-lookup"><span data-stu-id="8b016-614">value3</span></span>                       |
| <span data-ttu-id="8b016-615">4</span><span class="sxs-lookup"><span data-stu-id="8b016-615">4</span></span>                            | <span data-ttu-id="8b016-616">value4</span><span class="sxs-lookup"><span data-stu-id="8b016-616">value4</span></span>                       |
| <span data-ttu-id="8b016-617">5</span><span class="sxs-lookup"><span data-stu-id="8b016-617">5</span></span>                            | <span data-ttu-id="8b016-618">value5</span><span class="sxs-lookup"><span data-stu-id="8b016-618">value5</span></span>                       |

<span data-ttu-id="8b016-619">**JSON-Arrayverarbeitung**</span><span class="sxs-lookup"><span data-stu-id="8b016-619">**JSON array processing**</span></span>

<span data-ttu-id="8b016-620">Wenn eine JSON-Datei ein Array enthält, werden Konfigurationsschlüssel für die Arrayelemente mit einem nullbasierten Abschnittsindex erstellt.</span><span class="sxs-lookup"><span data-stu-id="8b016-620">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="8b016-621">In der folgenden Konfigurationsdatei ist `subsection` ein Array:</span><span class="sxs-lookup"><span data-stu-id="8b016-621">In the following configuration file, `subsection` is an array:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/json_array.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

::: moniker-end

<span data-ttu-id="8b016-622">Der JSON-Konfigurationsanbieter liest die Konfigurationsdaten in die folgenden Schlüssel-Wert-Paare:</span><span class="sxs-lookup"><span data-stu-id="8b016-622">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="8b016-623">Key</span><span class="sxs-lookup"><span data-stu-id="8b016-623">Key</span></span>                     | <span data-ttu-id="8b016-624">Wert</span><span class="sxs-lookup"><span data-stu-id="8b016-624">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="8b016-625">json_array:key</span><span class="sxs-lookup"><span data-stu-id="8b016-625">json_array:key</span></span>          | <span data-ttu-id="8b016-626">valueA</span><span class="sxs-lookup"><span data-stu-id="8b016-626">valueA</span></span> |
| <span data-ttu-id="8b016-627">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="8b016-627">json_array:subsection:0</span></span> | <span data-ttu-id="8b016-628">valueB</span><span class="sxs-lookup"><span data-stu-id="8b016-628">valueB</span></span> |
| <span data-ttu-id="8b016-629">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="8b016-629">json_array:subsection:1</span></span> | <span data-ttu-id="8b016-630">valueC</span><span class="sxs-lookup"><span data-stu-id="8b016-630">valueC</span></span> |
| <span data-ttu-id="8b016-631">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="8b016-631">json_array:subsection:2</span></span> | <span data-ttu-id="8b016-632">valueD</span><span class="sxs-lookup"><span data-stu-id="8b016-632">valueD</span></span> |

<span data-ttu-id="8b016-633">In der Beispiel-App können die Konfigurations-Schlüssel-Wert-Paare mit dieser POCO-Klasse gebunden werden:</span><span class="sxs-lookup"><span data-stu-id="8b016-633">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="8b016-634">Nach dem Binden enthält `JsonArrayExample.Key` den Wert `valueA`.</span><span class="sxs-lookup"><span data-stu-id="8b016-634">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="8b016-635">Die Unterabschnittwerte werden in der POCO-Arrayeigenschaft `Subsection` gespeichert.</span><span class="sxs-lookup"><span data-stu-id="8b016-635">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="8b016-636">`JsonArrayExample.Subsection`-Index</span><span class="sxs-lookup"><span data-stu-id="8b016-636">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="8b016-637">`JsonArrayExample.Subsection`-Wert</span><span class="sxs-lookup"><span data-stu-id="8b016-637">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="8b016-638">0</span><span class="sxs-lookup"><span data-stu-id="8b016-638">0</span></span>                                   | <span data-ttu-id="8b016-639">valueB</span><span class="sxs-lookup"><span data-stu-id="8b016-639">valueB</span></span>                              |
| <span data-ttu-id="8b016-640">1</span><span class="sxs-lookup"><span data-stu-id="8b016-640">1</span></span>                                   | <span data-ttu-id="8b016-641">valueC</span><span class="sxs-lookup"><span data-stu-id="8b016-641">valueC</span></span>                              |
| <span data-ttu-id="8b016-642">2</span><span class="sxs-lookup"><span data-stu-id="8b016-642">2</span></span>                                   | <span data-ttu-id="8b016-643">valueD</span><span class="sxs-lookup"><span data-stu-id="8b016-643">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="8b016-644">Benutzerdefinierter Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="8b016-644">Custom configuration provider</span></span>

<span data-ttu-id="8b016-645">Die Beispiel-App veranschaulicht, wie ein Standardkonfigurationsanbieter erstellt wird, der Konfigurations-Schlüssel-Wert-Paare aus einer Datenbank mit [Entity Framework (EF)](/ef/core/) liest.</span><span class="sxs-lookup"><span data-stu-id="8b016-645">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="8b016-646">Der Anbieter weist die folgenden Merkmale auf:</span><span class="sxs-lookup"><span data-stu-id="8b016-646">The provider has the following characteristics:</span></span>

* <span data-ttu-id="8b016-647">Die EF-In-Memory-Datenbank wird zu Demonstrationszwecken verwendet.</span><span class="sxs-lookup"><span data-stu-id="8b016-647">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="8b016-648">Um eine Datenbank zu verwenden, die eine Verbindungszeichenfolge benötigt, implementieren Sie einen sekundären `ConfigurationBuilder`, um die Verbindungszeichenfolge aus einem anderen Konfigurationsanbieter anzugeben.</span><span class="sxs-lookup"><span data-stu-id="8b016-648">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="8b016-649">Der Anbieter liest eine Datenbanktabelle beim Start in die Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="8b016-649">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="8b016-650">Der Anbieter fragt die Datenbank nicht pro Schlüssel ab.</span><span class="sxs-lookup"><span data-stu-id="8b016-650">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="8b016-651">Das erneute Laden bei Änderung ist nicht implementiert. Das heißt, das Aktualisieren der Datenbank nach App-Start hat keine Auswirkungen auf die App-Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="8b016-651">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="8b016-652">Definieren Sie eine `EFConfigurationValue`-Entität zum Speichern von Konfigurationswerten in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="8b016-652">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8b016-653">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="8b016-653">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="8b016-654">Fügen Sie `EFConfigurationContext` hinzu, um die konfigurierten Werte zu speichern und auf diese zugreifen.</span><span class="sxs-lookup"><span data-stu-id="8b016-654">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="8b016-655">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="8b016-655">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="8b016-656">Erstellen Sie eine Klasse, die das <xref:Microsoft.Extensions.Configuration.IConfigurationSource> implementiert.</span><span class="sxs-lookup"><span data-stu-id="8b016-656">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="8b016-657">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="8b016-657">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="8b016-658">Erstellen Sie den benutzerdefinierten Konfigurationsanbieter durch Vererbung von <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="8b016-658">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="8b016-659">Der Konfigurationsanbieter initialisiert die Datenbank, wenn diese leer ist.</span><span class="sxs-lookup"><span data-stu-id="8b016-659">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="8b016-660">Da [Konfigurationsschlüssel die Groß-/Kleinschreibung nicht beachten](#keys), wird das zum Initialisieren der Datenbank verwendete Wörterbuch mit der Vergleichsfunktion, die die Groß-/Kleinschreibung nicht beachtet, erstellt ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="8b016-660">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="8b016-661">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="8b016-661">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="8b016-662">Mit einer `AddEFConfiguration`-Erweiterungsmethode kann die Konfigurationsquelle `ConfigurationBuilder` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="8b016-662">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="8b016-663">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="8b016-663">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="8b016-664">Der folgende Code veranschaulicht die Verwendung des benutzerdefinierten Anbieters `EFConfigurationProvider` in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="8b016-664">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8b016-665">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="8b016-665">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="8b016-666">Fügen Sie `EFConfigurationContext` hinzu, um die konfigurierten Werte zu speichern und auf diese zugreifen.</span><span class="sxs-lookup"><span data-stu-id="8b016-666">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="8b016-667">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="8b016-667">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="8b016-668">Erstellen Sie eine Klasse, die das <xref:Microsoft.Extensions.Configuration.IConfigurationSource> implementiert.</span><span class="sxs-lookup"><span data-stu-id="8b016-668">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="8b016-669">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="8b016-669">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="8b016-670">Erstellen Sie den benutzerdefinierten Konfigurationsanbieter durch Vererbung von <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="8b016-670">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="8b016-671">Der Konfigurationsanbieter initialisiert die Datenbank, wenn diese leer ist.</span><span class="sxs-lookup"><span data-stu-id="8b016-671">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="8b016-672">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="8b016-672">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="8b016-673">Mit einer `AddEFConfiguration`-Erweiterungsmethode kann die Konfigurationsquelle `ConfigurationBuilder` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="8b016-673">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="8b016-674">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="8b016-674">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="8b016-675">Der folgende Code veranschaulicht die Verwendung des benutzerdefinierten Anbieters `EFConfigurationProvider` in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="8b016-675">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

::: moniker-end

## <a name="access-configuration-during-startup"></a><span data-ttu-id="8b016-676">Zugreifen auf die Konfiguration während des Starts</span><span class="sxs-lookup"><span data-stu-id="8b016-676">Access configuration during startup</span></span>

<span data-ttu-id="8b016-677">Fügen Sie `IConfiguration` in den `Startup`-Konstruktor ein, um auf Konfigurationswerte in `Startup.ConfigureServices` zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="8b016-677">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8b016-678">Um auf die Konfiguration in `Startup.Configure` zuzugreifen, fügen Sie entweder `IConfiguration` direkt in die Methode ein, oder verwenden Sie die Instanz aus dem Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="8b016-678">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="8b016-679">Ein Beispiel für den Zugriff auf die Konfiguration mit den Starthilfsmethoden finden Sie unter [Anwendungsstart: Hilfsmethoden](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="8b016-679">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="8b016-680">Zugreifen auf die Konfiguration auf einer Razor Pages-Seite oder in einer MVC-Ansicht</span><span class="sxs-lookup"><span data-stu-id="8b016-680">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="8b016-681">Um auf die Konfigurationseinstellungen auf einer Razor Pages-Seite oder in einer MVC-Ansicht zuzugreifen, fügen Sie eine [using-Anweisung](xref:mvc/views/razor#using) ([C#-Referenz: using-Anweisung](/dotnet/csharp/language-reference/keywords/using-directive)) für den [Microsoft.Extensions.Configuration-Namespace ](xref:Microsoft.Extensions.Configuration) hinzu, und fügen Sie <xref:Microsoft.Extensions.Configuration.IConfiguration> auf der Seite bzw. in der Ansicht ein.</span><span class="sxs-lookup"><span data-stu-id="8b016-681">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="8b016-682">Auf einer Razor Pages-Seite:</span><span class="sxs-lookup"><span data-stu-id="8b016-682">In a Razor Pages page:</span></span>

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

<span data-ttu-id="8b016-683">In einer MVC-Ansicht:</span><span class="sxs-lookup"><span data-stu-id="8b016-683">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="8b016-684">Hinzufügen von Konfigurationen aus einer externen Assembly</span><span class="sxs-lookup"><span data-stu-id="8b016-684">Add configuration from an external assembly</span></span>

<span data-ttu-id="8b016-685">Eine <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>-Implementierung ermöglicht das Hinzufügen von Erweiterungen zu einer App beim Start von einer externen Assembly außerhalb der `Startup`-Klasse der App aus.</span><span class="sxs-lookup"><span data-stu-id="8b016-685">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="8b016-686">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="8b016-686">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8b016-687">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="8b016-687">Additional resources</span></span>

* <xref:fundamentals/configuration/options>
