---
title: Konfiguration in ASP.NET Core
author: guardrex
description: In diesem Artikel erfahren Sie, wie Sie mit der Konfigurations-API eine ASP.NET Core-App konfigurieren.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/04/2019
uid: fundamentals/configuration/index
ms.openlocfilehash: 9f0ad2791e504a0ff46daad07054b6bf909a546a
ms.sourcegitcommit: 897d4abff58505dae86b2947c5fe3d1b80d927f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73634080"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="5438b-103">Konfiguration in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5438b-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="5438b-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5438b-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5438b-105">Die App-Konfiguration in ASP.NET Core basiert auf Schlüssel-Wert-Paaren, die von *Konfigurationsanbietern* erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="5438b-105">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="5438b-106">Konfigurationsanbieter lesen Konfigurationsdaten in Schlüssel-Wert-Paare aus verschiedenen Konfigurationsquellen:</span><span class="sxs-lookup"><span data-stu-id="5438b-106">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="5438b-107">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="5438b-107">Azure Key Vault</span></span>
* <span data-ttu-id="5438b-108">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="5438b-108">Azure App Configuration</span></span>
* <span data-ttu-id="5438b-109">Befehlszeilenargumente</span><span class="sxs-lookup"><span data-stu-id="5438b-109">Command-line arguments</span></span>
* <span data-ttu-id="5438b-110">Benutzerdefinierte Anbieter (installiert oder erstellt)</span><span class="sxs-lookup"><span data-stu-id="5438b-110">Custom providers (installed or created)</span></span>
* <span data-ttu-id="5438b-111">Verzeichnisdateien</span><span class="sxs-lookup"><span data-stu-id="5438b-111">Directory files</span></span>
* <span data-ttu-id="5438b-112">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="5438b-112">Environment variables</span></span>
* <span data-ttu-id="5438b-113">Speicherinterne .NET Objekte</span><span class="sxs-lookup"><span data-stu-id="5438b-113">In-memory .NET objects</span></span>
* <span data-ttu-id="5438b-114">Einstellungsdateien</span><span class="sxs-lookup"><span data-stu-id="5438b-114">Settings files</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5438b-115">Konfigurationspakete für gängige Konfigurationsanbieterszenarien ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) sind über das Framework implizit enthalten.</span><span class="sxs-lookup"><span data-stu-id="5438b-115">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included implicitly by the framework.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5438b-116">Konfigurationspakete für gängige Konfigurationsanbieterszenarien ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) sind im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="5438b-116">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="5438b-117">Codebeispiele, die den <xref:Microsoft.Extensions.Configuration>-Namespace befolgen und in der Beispiel-App verwenden:</span><span class="sxs-lookup"><span data-stu-id="5438b-117">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="5438b-118">Das *Optionsmuster* ist eine Erweiterung der in diesem Thema beschriebenen Konfigurationskonzepte.</span><span class="sxs-lookup"><span data-stu-id="5438b-118">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="5438b-119">Optionen verwenden Klassen, um Gruppen von zusammengehörigen Einstellungen darzustellen.</span><span class="sxs-lookup"><span data-stu-id="5438b-119">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="5438b-120">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="5438b-120">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="5438b-121">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5438b-121">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="5438b-122">Hostkonfiguration und App-Konfiguration im Vergleich</span><span class="sxs-lookup"><span data-stu-id="5438b-122">Host versus app configuration</span></span>

<span data-ttu-id="5438b-123">Bevor die App konfiguriert und gestartet wird, wird ein *Host* konfiguriert und gestartet.</span><span class="sxs-lookup"><span data-stu-id="5438b-123">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="5438b-124">Der Host ist verantwortlich für das Starten der App und das Verwalten der Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="5438b-124">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="5438b-125">Die App und der Host werden mit den in diesem Thema beschriebenen Konfigurationsanbietern konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="5438b-125">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="5438b-126">Schlüssel-Wert-Paare der Hostkonfiguration sind ebenfalls in der globalen App-Konfiguration enthalten.</span><span class="sxs-lookup"><span data-stu-id="5438b-126">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="5438b-127">Weitere Informationen dazu, wie Konfigurationsanbieter beim Erstellen des Hosts verwendet werden, und wie sich Konfigurationsquellen auf die Hostkonfiguration auswirken, finden Sie unter <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="5438b-127">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="5438b-128">Standardkonfiguration</span><span class="sxs-lookup"><span data-stu-id="5438b-128">Default configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5438b-129">Web-Apps, die auf den [dotnet new](/dotnet/core/tools/dotnet-new)-Vorlagen von ASP.NET Core basieren, rufen beim Erstellen eines Hosts <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> auf.</span><span class="sxs-lookup"><span data-stu-id="5438b-129">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="5438b-130">`CreateDefaultBuilder` legt die Standardkonfiguration für die App in der folgenden Reihenfolge fest:</span><span class="sxs-lookup"><span data-stu-id="5438b-130">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="5438b-131">Folgendes gilt für Apps, die den [generischen Host](xref:fundamentals/host/generic-host) verwenden.</span><span class="sxs-lookup"><span data-stu-id="5438b-131">The following applies to apps using the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="5438b-132">Ausführliche Informationen zur Standardkonfiguration bei Verwendung des [Webhosts](xref:fundamentals/host/web-host) finden Sie in der [ASP.NET Core 2.2-Version dieses Themas](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="5438b-132">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="5438b-133">Die Konfiguration des Hosts wird durch Folgendes festgelegt:</span><span class="sxs-lookup"><span data-stu-id="5438b-133">Host configuration is provided from:</span></span>
  * <span data-ttu-id="5438b-134">Umgebungsvariablen mit dem Präfix `DOTNET_` (zum Beispiel `DOTNET_ENVIRONMENT`) mithilfe des [Umgebungsvariablen-Konfigurationsanbieters](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5438b-134">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="5438b-135">Das Präfix (`DOTNET_`) wird beim Laden der Schlüssel-Wert-Paare der Konfiguration entfernt.</span><span class="sxs-lookup"><span data-stu-id="5438b-135">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="5438b-136">Befehlszeilenargumente mithilfe des [Befehlszeilen-Konfigurationsanbieters](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5438b-136">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="5438b-137">Die Webhost-Standardkonfiguration wird eingerichtet (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="5438b-137">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="5438b-138">Als Webserver wird Kestrel verwendet und mithilfe der Konfigurationsanbieter der App konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="5438b-138">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="5438b-139">Fügen Sie Middleware zum Filtern von Hosts hinzu.</span><span class="sxs-lookup"><span data-stu-id="5438b-139">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="5438b-140">Fügen Sie Middleware für weitergeleitete Header hinzu, wenn die Umgebungsvariable `ASPNETCORE_FORWARDEDHEADERS_ENABLED` auf `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="5438b-140">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="5438b-141">Aktivieren Sie die IIS-Integration.</span><span class="sxs-lookup"><span data-stu-id="5438b-141">Enable IIS integration.</span></span>
* <span data-ttu-id="5438b-142">Die App-Konfiguration wird durch Folgendes festgelegt:</span><span class="sxs-lookup"><span data-stu-id="5438b-142">App configuration is provided from:</span></span>
  * <span data-ttu-id="5438b-143">*appsettings.json* mithilfe des [Dateikonfigurationsanbieters](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5438b-143">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="5438b-144">*appsettings.{Umgebung}.json* mithilfe des [Dateikonfigurationsanbieters](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5438b-144">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="5438b-145">[Geheimnis-Manager](xref:security/app-secrets), wenn die App in der `Development`-Umgebung mit der Einstiegsassembly ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="5438b-145">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="5438b-146">Umgebungsvariablen mithilfe des [Umgebungsvariablen-Konfigurationsanbieters](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5438b-146">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="5438b-147">Befehlszeilenargumente mithilfe des [Befehlszeilen-Konfigurationsanbieters](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5438b-147">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5438b-148">Web-Apps, die auf den [dotnet new](/dotnet/core/tools/dotnet-new)-Vorlagen von ASP.NET Core basieren, rufen beim Erstellen eines Hosts <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> auf.</span><span class="sxs-lookup"><span data-stu-id="5438b-148">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="5438b-149">`CreateDefaultBuilder` legt die Standardkonfiguration für die App in der folgenden Reihenfolge fest:</span><span class="sxs-lookup"><span data-stu-id="5438b-149">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="5438b-150">Folgendes gilt für Apps, die den [Webhost](xref:fundamentals/host/web-host) verwenden.</span><span class="sxs-lookup"><span data-stu-id="5438b-150">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="5438b-151">Ausführliche Informationen zur Standardkonfiguration bei Verwendung des [generischen Hosts](xref:fundamentals/host/generic-host) finden Sie in der [neuesten Version dieses Themas](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="5438b-151">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="5438b-152">Die Konfiguration des Hosts wird durch Folgendes festgelegt:</span><span class="sxs-lookup"><span data-stu-id="5438b-152">Host configuration is provided from:</span></span>
  * <span data-ttu-id="5438b-153">Umgebungsvariablen mit dem Präfix `ASPNETCORE_` (zum Beispiel `ASPNETCORE_ENVIRONMENT`) mithilfe des [Umgebungsvariablen-Konfigurationsanbieters](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5438b-153">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="5438b-154">Das Präfix (`ASPNETCORE_`) wird beim Laden der Schlüssel-Wert-Paare der Konfiguration entfernt.</span><span class="sxs-lookup"><span data-stu-id="5438b-154">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="5438b-155">Befehlszeilenargumente mithilfe des [Befehlszeilen-Konfigurationsanbieters](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5438b-155">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="5438b-156">Die App-Konfiguration wird durch Folgendes festgelegt:</span><span class="sxs-lookup"><span data-stu-id="5438b-156">App configuration is provided from:</span></span>
  * <span data-ttu-id="5438b-157">*appsettings.json* mithilfe des [Dateikonfigurationsanbieters](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5438b-157">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="5438b-158">*appsettings.{Umgebung}.json* mithilfe des [Dateikonfigurationsanbieters](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5438b-158">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="5438b-159">[Geheimnis-Manager](xref:security/app-secrets), wenn die App in der `Development`-Umgebung mit der Einstiegsassembly ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="5438b-159">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="5438b-160">Umgebungsvariablen mithilfe des [Umgebungsvariablen-Konfigurationsanbieters](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5438b-160">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="5438b-161">Befehlszeilenargumente mithilfe des [Befehlszeilen-Konfigurationsanbieters](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="5438b-161">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

::: moniker-end

## <a name="security"></a><span data-ttu-id="5438b-162">Sicherheit</span><span class="sxs-lookup"><span data-stu-id="5438b-162">Security</span></span>

<span data-ttu-id="5438b-163">Wenden Sie die folgenden Verfahren an, um vertrauliche Konfigurationsdaten zu schützen:</span><span class="sxs-lookup"><span data-stu-id="5438b-163">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="5438b-164">Speichern Sie nie Kennwörter oder andere vertrauliche Daten im Konfigurationsanbietercode oder in Nur-Text-Konfigurationsdateien.</span><span class="sxs-lookup"><span data-stu-id="5438b-164">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="5438b-165">Verwenden Sie keine Produktionsgeheimnisse in Entwicklungs- oder Testumgebungen.</span><span class="sxs-lookup"><span data-stu-id="5438b-165">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="5438b-166">Geben Sie Geheimnisse außerhalb des Projekts an, damit sie nicht versehentlich in ein Quellcoderepository übernommen werden können.</span><span class="sxs-lookup"><span data-stu-id="5438b-166">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="5438b-167">Weitere Informationen finden Sie unter den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="5438b-167">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="5438b-168"><xref:security/app-secrets> &ndash; Enthält Hinweise zur Verwendung von Umgebungsvariablen zum Speichern sensibler Daten.</span><span class="sxs-lookup"><span data-stu-id="5438b-168"><xref:security/app-secrets> &ndash; Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="5438b-169">Der Geheimnis-Manager verwendet den Dateikonfigurationsanbieter, um Benutzergeheimnisse in einer JSON-Datei im lokalen System zu speichern.</span><span class="sxs-lookup"><span data-stu-id="5438b-169">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="5438b-170">Der Dateikonfigurationsanbieter wird später in diesem Thema beschrieben.</span><span class="sxs-lookup"><span data-stu-id="5438b-170">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="5438b-171">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) speichert App-Geheimnisse für ASP.NET Core-Apps auf sichere Weise.</span><span class="sxs-lookup"><span data-stu-id="5438b-171">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="5438b-172">Weitere Informationen finden Sie unter <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="5438b-172">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="5438b-173">Hierarchische Konfigurationsdaten</span><span class="sxs-lookup"><span data-stu-id="5438b-173">Hierarchical configuration data</span></span>

<span data-ttu-id="5438b-174">Die Konfigurations-API kann hierarchische Konfigurationsdaten erhalten, indem sie die hierarchischen Daten mit einem Trennzeichen in den Konfigurationsschlüsseln vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="5438b-174">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="5438b-175">Die folgende JSON-Datei enthält vier Schlüssel in einer strukturierten Hierarchie von zwei Abschnitten:</span><span class="sxs-lookup"><span data-stu-id="5438b-175">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="5438b-176">Wenn die Datei in die Konfiguration gelesen wird, werden eindeutige Schlüssel erstellt, um die ursprüngliche hierarchische Datenstruktur der Konfigurationsquelle zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="5438b-176">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="5438b-177">Die Abschnitte und Schlüssel werden mithilfe eines Doppelpunkts (`:`) vereinfacht, um die Originalstruktur zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="5438b-177">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="5438b-178">section0:key0</span><span class="sxs-lookup"><span data-stu-id="5438b-178">section0:key0</span></span>
* <span data-ttu-id="5438b-179">section0:key1</span><span class="sxs-lookup"><span data-stu-id="5438b-179">section0:key1</span></span>
* <span data-ttu-id="5438b-180">section1:key0</span><span class="sxs-lookup"><span data-stu-id="5438b-180">section1:key0</span></span>
* <span data-ttu-id="5438b-181">section1:key1</span><span class="sxs-lookup"><span data-stu-id="5438b-181">section1:key1</span></span>

<span data-ttu-id="5438b-182">Mit den Methoden <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> und <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> können Abschnitte und untergeordnete Abschnittelemente in den Konfigurationsdaten isoliert werden.</span><span class="sxs-lookup"><span data-stu-id="5438b-182"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="5438b-183">Diese Methoden werden später im Abschnitt [GetSection, GetChildren und Exists](#getsection-getchildren-and-exists) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="5438b-183">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="5438b-184">Konventionen</span><span class="sxs-lookup"><span data-stu-id="5438b-184">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="5438b-185">Quellen und Anbieter</span><span class="sxs-lookup"><span data-stu-id="5438b-185">Sources and providers</span></span>

<span data-ttu-id="5438b-186">Beim Starten der Anwendung werden Konfigurationsquellen in der Reihenfolge gelesen, in der ihre Konfigurationsanbieter angegeben sind.</span><span class="sxs-lookup"><span data-stu-id="5438b-186">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="5438b-187">Konfigurationsanbieter, die Änderungserkennung implementieren, können Konfigurationen erneut laden, wenn zugrunde liegende Einstellungen geändert werden.</span><span class="sxs-lookup"><span data-stu-id="5438b-187">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="5438b-188">Der Dateikonfigurationsanbieter (weiter unten in diesem Thema beschrieben) und der [Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration) implementieren beispielsweise die Änderungserkennung.</span><span class="sxs-lookup"><span data-stu-id="5438b-188">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="5438b-189"><xref:Microsoft.Extensions.Configuration.IConfiguration> steht im App-Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="5438b-189"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="5438b-190"><xref:Microsoft.Extensions.Configuration.IConfiguration> kann in eine <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel>-Klasse von Razor Pages eingefügt werden, um die Konfiguration für die Klasse abzurufen:</span><span class="sxs-lookup"><span data-stu-id="5438b-190"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> to obtain configuration for the class:</span></span>

```csharp
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

<span data-ttu-id="5438b-191">Konfigurationsanbieter können die Abhängigkeitsinjektion nicht verwenden, weil sie nicht verfügbar ist, wenn sie vom Host eingerichtet werden.</span><span class="sxs-lookup"><span data-stu-id="5438b-191">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="5438b-192">Tasten</span><span class="sxs-lookup"><span data-stu-id="5438b-192">Keys</span></span>

<span data-ttu-id="5438b-193">Konfigurationsschlüssel entsprechen den folgenden Konventionen:</span><span class="sxs-lookup"><span data-stu-id="5438b-193">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="5438b-194">Bei Schlüsseln wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="5438b-194">Keys are case-insensitive.</span></span> <span data-ttu-id="5438b-195">Beispielsweise verweisen `ConnectionString` und `connectionstring` auf denselben Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="5438b-195">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="5438b-196">Wenn ein Wert für denselben Schlüssel von denselben oder unterschiedlichen Konfigurationsanbietern festgelegt wird, wird der zuletzt für den Schlüssel bestimmte Wert verwendet.</span><span class="sxs-lookup"><span data-stu-id="5438b-196">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="5438b-197">Hierarchische Schlüssel</span><span class="sxs-lookup"><span data-stu-id="5438b-197">Hierarchical keys</span></span>
  * <span data-ttu-id="5438b-198">Innerhalb der Konfigurations-API funktioniert ein Doppelpunkt (`:`) als Trennzeichen auf allen Plattformen.</span><span class="sxs-lookup"><span data-stu-id="5438b-198">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="5438b-199">In Umgebungsvariablen funktioniert ein Doppelpunkt als Trennzeichen ggf. nicht auf allen Plattformen.</span><span class="sxs-lookup"><span data-stu-id="5438b-199">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="5438b-200">Ein doppelter Unterstrich (`__`) wird auf allen Plattformen unterstützt und automatisch in einen Doppelpunkt konvertiert.</span><span class="sxs-lookup"><span data-stu-id="5438b-200">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="5438b-201">In Azure Key Vault verwenden hierarchische Schlüssel zwei Bindestriche (`--`) als Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="5438b-201">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="5438b-202">Sie müssen einen Code bereitstellen, um die Bindestriche durch einen Doppelpunkt zu ersetzen, wenn die Geheimnisse in die App-Konfiguration geladen werden.</span><span class="sxs-lookup"><span data-stu-id="5438b-202">You must provide code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="5438b-203"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> unterstützt das Binden von Arrays an Objekte mit Arrayindizes in Konfigurationsschlüsseln.</span><span class="sxs-lookup"><span data-stu-id="5438b-203">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="5438b-204">Die Arraybindung wird im Abschnitt [Binden eines Arrays an eine Klasse](#bind-an-array-to-a-class) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="5438b-204">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="5438b-205">Werte</span><span class="sxs-lookup"><span data-stu-id="5438b-205">Values</span></span>

<span data-ttu-id="5438b-206">Konfigurationswerte entsprechen den folgenden Konventionen:</span><span class="sxs-lookup"><span data-stu-id="5438b-206">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="5438b-207">Die Werte sind Zeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="5438b-207">Values are strings.</span></span>
* <span data-ttu-id="5438b-208">NULL-Werte können nicht in einer Konfiguration gespeichert oder an Objekte gebunden werden.</span><span class="sxs-lookup"><span data-stu-id="5438b-208">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="5438b-209">Anbieter</span><span class="sxs-lookup"><span data-stu-id="5438b-209">Providers</span></span>

<span data-ttu-id="5438b-210">Die folgende Tabelle zeigt die für ASP.NET Core-Apps verfügbaren Konfigurationsanbieter.</span><span class="sxs-lookup"><span data-stu-id="5438b-210">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="5438b-211">Anbieter</span><span class="sxs-lookup"><span data-stu-id="5438b-211">Provider</span></span> | <span data-ttu-id="5438b-212">Bereitstellung der Konfiguration über&hellip;</span><span class="sxs-lookup"><span data-stu-id="5438b-212">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="5438b-213">[Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration) (Thema *Sicherheit*)</span><span class="sxs-lookup"><span data-stu-id="5438b-213">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="5438b-214">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="5438b-214">Azure Key Vault</span></span> |
| <span data-ttu-id="5438b-215">[Azure App Configuration-Anbieter](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure-Dokumentation)</span><span class="sxs-lookup"><span data-stu-id="5438b-215">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="5438b-216">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="5438b-216">Azure App Configuration</span></span> |
| [<span data-ttu-id="5438b-217">Befehlszeilen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5438b-217">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="5438b-218">Befehlszeilenparameter</span><span class="sxs-lookup"><span data-stu-id="5438b-218">Command-line parameters</span></span> |
| [<span data-ttu-id="5438b-219">Benutzerdefinierter Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5438b-219">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="5438b-220">Benutzerdefinierte Quelle</span><span class="sxs-lookup"><span data-stu-id="5438b-220">Custom source</span></span> |
| [<span data-ttu-id="5438b-221">Umgebungsvariablen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5438b-221">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="5438b-222">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="5438b-222">Environment variables</span></span> |
| [<span data-ttu-id="5438b-223">Dateikonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5438b-223">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="5438b-224">Dateien (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="5438b-224">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="5438b-225">Schlüssel-pro-Datei-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5438b-225">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="5438b-226">Verzeichnisdateien</span><span class="sxs-lookup"><span data-stu-id="5438b-226">Directory files</span></span> |
| [<span data-ttu-id="5438b-227">Speicherkonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5438b-227">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="5438b-228">In-Memory-Sammlungen</span><span class="sxs-lookup"><span data-stu-id="5438b-228">In-memory collections</span></span> |
| <span data-ttu-id="5438b-229">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) (Thema *Sicherheit*)</span><span class="sxs-lookup"><span data-stu-id="5438b-229">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="5438b-230">Datei im Benutzerprofilverzeichnis</span><span class="sxs-lookup"><span data-stu-id="5438b-230">File in the user profile directory</span></span> |

<span data-ttu-id="5438b-231">Konfigurationsquellen werden beim Start in der Reihenfolge gelesen, in der ihre Konfigurationsanbieter angegeben sind.</span><span class="sxs-lookup"><span data-stu-id="5438b-231">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="5438b-232">Die in diesem Thema beschriebenen Konfigurationsanbieter werden in alphabetischer Reihenfolge beschrieben, nicht in der Reihenfolge, in der Ihr Code sie anordnet.</span><span class="sxs-lookup"><span data-stu-id="5438b-232">The configuration providers described in this topic are described in alphabetical order, not in the order that your code may arrange them.</span></span> <span data-ttu-id="5438b-233">Ordnen Sie die Konfigurationsanbieter in Ihrem Code so an, dass sie den Prioritäten für die zugrunde liegenden Konfigurationsquellen entsprechen.</span><span class="sxs-lookup"><span data-stu-id="5438b-233">Order configuration providers in your code to suit your priorities for the underlying configuration sources.</span></span>

<span data-ttu-id="5438b-234">Eine typische Konfigurationsanbietersequenz ist:</span><span class="sxs-lookup"><span data-stu-id="5438b-234">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="5438b-235">Dateien (*appsettings.json*, *appsettings.{Environment}.json*, wobei `{Environment}` die aktuelle Hostingumgebung der App ist)</span><span class="sxs-lookup"><span data-stu-id="5438b-235">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="5438b-236">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="5438b-236">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="5438b-237">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) (nur Entwicklungsumgebung)</span><span class="sxs-lookup"><span data-stu-id="5438b-237">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="5438b-238">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="5438b-238">Environment variables</span></span>
1. <span data-ttu-id="5438b-239">Befehlszeilenargumente</span><span class="sxs-lookup"><span data-stu-id="5438b-239">Command-line arguments</span></span>

<span data-ttu-id="5438b-240">In der Regel werden Befehlszeilen-Konfigurationsanbieter in einer Reihe von Anbietern an letzter Stelle positioniert, damit Befehlszeilenargumente die von anderen Anbietern festgelegte Konfiguration überschreiben können.</span><span class="sxs-lookup"><span data-stu-id="5438b-240">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="5438b-241">Die vorhergehende Anbieterfolge wird verwendet, wenn Sie einen neuen Host-Generator mit `CreateDefaultBuilder` initialisieren.</span><span class="sxs-lookup"><span data-stu-id="5438b-241">The preceding sequence of providers is used when you initialize a new host builder with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5438b-242">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="5438b-242">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="configure-the-host-builder-with-configurehostconfiguration"></a><span data-ttu-id="5438b-243">Konfigurieren des Host-Generators mit ConfigureHostConfiguration</span><span class="sxs-lookup"><span data-stu-id="5438b-243">Configure the host builder with ConfigureHostConfiguration</span></span>

<span data-ttu-id="5438b-244">Um den Host-Generator zu konfigurieren, rufen Sie <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> auf und geben die Konfiguration an.</span><span class="sxs-lookup"><span data-stu-id="5438b-244">To configure the host builder, call <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*> and supply the configuration.</span></span> <span data-ttu-id="5438b-245">`ConfigureHostConfiguration` wird verwendet, um das <xref:Microsoft.Extensions.Hosting.IHostEnvironment> für die spätere Verwendung im Buildprozess zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="5438b-245">`ConfigureHostConfiguration` is used to initialize the <xref:Microsoft.Extensions.Hosting.IHostEnvironment> for use later in the build process.</span></span> <span data-ttu-id="5438b-246">`ConfigureHostConfiguration` kann mehrfach mit additiven Ergebnissen aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="5438b-246">`ConfigureHostConfiguration` can be called multiple times with additive results.</span></span>

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

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="5438b-247">Konfigurieren des Host-Generators mit UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="5438b-247">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="5438b-248">Um den Host-Generator zu konfigurieren, rufen Sie <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> mit der Konfiguration für den Host-Generator auf.</span><span class="sxs-lookup"><span data-stu-id="5438b-248">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="5438b-249">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="5438b-249">ConfigureAppConfiguration</span></span>

<span data-ttu-id="5438b-250">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfigurationsanbieter der App zusätzlich zu denen anzugeben, die automatisch von `CreateDefaultBuilder` hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="5438b-250">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

::: moniker-end

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="5438b-251">Überschreiben der vorherigen Konfiguration mit Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="5438b-251">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="5438b-252">Um die App-Konfiguration bereitzustellen, die mit Befehlszeilenargumenten überschrieben werden kann, rufen Sie `AddCommandLine` zuletzt auf:</span><span class="sxs-lookup"><span data-stu-id="5438b-252">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="5438b-253">Entfernen von durch CreateDefaultBuilder hinzugefügten Anbietern</span><span class="sxs-lookup"><span data-stu-id="5438b-253">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="5438b-254">Rufen Sie zunächst [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) in [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) auf, um die durch `CreateDefaultBuilder` hinzugefügten Anbieter zu entfernen:</span><span class="sxs-lookup"><span data-stu-id="5438b-254">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="5438b-255">Verarbeiten der Konfiguration während des App-Starts</span><span class="sxs-lookup"><span data-stu-id="5438b-255">Consume configuration during app startup</span></span>

<span data-ttu-id="5438b-256">Die für die App in der `ConfigureAppConfiguration`-Methode angegebene Konfiguration, einschließlich `Startup.ConfigureServices`, ist während des Starts der App verfügbar.</span><span class="sxs-lookup"><span data-stu-id="5438b-256">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5438b-257">Weitere Informationen finden Sie im Abschnitt [Hinzufügen von Konfigurationen aus einer externen Assembly](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="5438b-257">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="5438b-258">Befehlszeilen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5438b-258">Command-line Configuration Provider</span></span>

<span data-ttu-id="5438b-259"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren des Befehlszeilenarguments.</span><span class="sxs-lookup"><span data-stu-id="5438b-259">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="5438b-260">Um die Befehlszeilenkonfiguration zu aktivieren, wird die <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>-Erweiterungsmethode für eine Instanz von <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="5438b-260">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="5438b-261">`AddCommandLine` wird automatisch aufgerufen, wenn `CreateDefaultBuilder(string [])` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="5438b-261">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="5438b-262">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="5438b-262">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="5438b-263">`CreateDefaultBuilder` lädt außerdem:</span><span class="sxs-lookup"><span data-stu-id="5438b-263">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="5438b-264">Optionale Konfiguration aus den Dateien *appsettings.json* und *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="5438b-264">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="5438b-265">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) in der Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="5438b-265">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="5438b-266">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="5438b-266">Environment variables.</span></span>

<span data-ttu-id="5438b-267">`CreateDefaultBuilder` fügt den Befehlszeilen-Konfigurationsanbieter zuletzt hinzu.</span><span class="sxs-lookup"><span data-stu-id="5438b-267">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="5438b-268">Während der Laufzeit übergebene Befehlszeilenargumente überschreiben die von anderen Anbietern festgelegte Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="5438b-268">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="5438b-269">`CreateDefaultBuilder` wird aktiv, wenn der Host erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="5438b-269">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="5438b-270">Deswegen kann die durch `CreateDefaultBuilder` aktivierte Befehlszeilenkonfiguration die Konfiguration des Hosts beeinflussen.</span><span class="sxs-lookup"><span data-stu-id="5438b-270">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="5438b-271">Für Apps, die auf den ASP.NET Core-Vorlagen basieren, wurde `AddCommandLine` bereits von `CreateDefaultBuilder` aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="5438b-271">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5438b-272">Um weitere Konfigurationsanbieter hinzuzufügen und die Möglichkeit einer Überschreibung der Konfiguration dieser Anbieter durch Befehlszeilenargumente beizubehalten, rufen Sie die zusätzlichen Anbieter in `ConfigureAppConfiguration` und zuletzt `AddCommandLine` auf.</span><span class="sxs-lookup"><span data-stu-id="5438b-272">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="5438b-273">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="5438b-273">**Example**</span></span>

<span data-ttu-id="5438b-274">Die Beispiel-App nutzt zum Erstellen des Hosts die statische Hilfsmethode `CreateDefaultBuilder`, die einen Aufruf von <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> enthält.</span><span class="sxs-lookup"><span data-stu-id="5438b-274">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="5438b-275">Öffnen Sie im Projektverzeichnis eine Eingabeaufforderung.</span><span class="sxs-lookup"><span data-stu-id="5438b-275">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="5438b-276">Geben Sie ein Befehlszeilenargument für den `dotnet run`-Befehl an, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="5438b-276">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="5438b-277">Wenn die App ausgeführt wird, öffnen Sie einen Browser für die App unter `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="5438b-277">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="5438b-278">Beachten Sie, dass die Ausgabe das Schlüssel-Wert-Paar für das an `dotnet run` übergebene Konfigurations-Befehlszeilenargument enthält.</span><span class="sxs-lookup"><span data-stu-id="5438b-278">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="5438b-279">Argumente</span><span class="sxs-lookup"><span data-stu-id="5438b-279">Arguments</span></span>

<span data-ttu-id="5438b-280">Der Wert muss einem Gleichheitszeichen (`=`) folgen, oder der Schlüssel muss ein Präfix (`--` oder `/`) haben, wenn der Wert einem Leerzeichen folgt.</span><span class="sxs-lookup"><span data-stu-id="5438b-280">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="5438b-281">Der Wert ist nicht erforderlich, wenn ein Gleichheitszeichen verwendet wird (z. B. `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="5438b-281">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="5438b-282">Schlüsselpräfix</span><span class="sxs-lookup"><span data-stu-id="5438b-282">Key prefix</span></span>               | <span data-ttu-id="5438b-283">Beispiel</span><span class="sxs-lookup"><span data-stu-id="5438b-283">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="5438b-284">Ohne Präfix</span><span class="sxs-lookup"><span data-stu-id="5438b-284">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="5438b-285">Zwei Gedankenstriche (`--`)</span><span class="sxs-lookup"><span data-stu-id="5438b-285">Two dashes (`--`)</span></span>        | <span data-ttu-id="5438b-286">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="5438b-286">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="5438b-287">Schrägstrich (`/`)</span><span class="sxs-lookup"><span data-stu-id="5438b-287">Forward slash (`/`)</span></span>      | <span data-ttu-id="5438b-288">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="5438b-288">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="5438b-289">Kombinieren Sie in einem Befehl nicht Schlüssel-Wert-Paare, die ein Gleichheitszeichen verwenden, mit Schlüssel-Wert-Paaren, die ein Leerzeichen verwenden.</span><span class="sxs-lookup"><span data-stu-id="5438b-289">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="5438b-290">Beispielbefehle:</span><span class="sxs-lookup"><span data-stu-id="5438b-290">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="5438b-291">Switchmappings</span><span class="sxs-lookup"><span data-stu-id="5438b-291">Switch mappings</span></span>

<span data-ttu-id="5438b-292">Switchmappings erlauben das Angeben einer Logik zum Ersetzen von Schlüsselnamen.</span><span class="sxs-lookup"><span data-stu-id="5438b-292">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="5438b-293">Wenn Sie die Konfiguration manuell mit <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> erstellen, können Sie ein Wörterbuch der Switchersetzungen für die <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>-Methode bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="5438b-293">When you manually build configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, you can provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="5438b-294">Wenn das Switchmappingwörterbuch verwendet wird, wird das Wörterbuch für Schlüssel, die dem von einem Befehlszeilenargument angegebenen Schlüssel entsprechen, überprüft.</span><span class="sxs-lookup"><span data-stu-id="5438b-294">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="5438b-295">Wenn der Befehlszeilenschlüssel im Wörterbuch gefunden wird, wird der Wörterbuchwert (der Schlüsselersatz) zum Festlegen des Schlüssel-Wert-Paares in der App-Konfiguration zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="5438b-295">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="5438b-296">Ein Switchmapping ist für jeden Befehlszeilenschlüssel erforderlich, dem ein einzelner Gedankenstrich (`-`) vorangestellt ist.</span><span class="sxs-lookup"><span data-stu-id="5438b-296">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="5438b-297">Regeln für Schlüssel von Switchmappingwörterbüchern:</span><span class="sxs-lookup"><span data-stu-id="5438b-297">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="5438b-298">Switchmappings müssen mit einem Gedankenstrich (`-`) oder zwei Gedankenstrichen (`--`) beginnen.</span><span class="sxs-lookup"><span data-stu-id="5438b-298">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="5438b-299">Das Switchmappingwörterbuch darf keine doppelten Schlüssel enthalten.</span><span class="sxs-lookup"><span data-stu-id="5438b-299">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="5438b-300">Erstellen Sie ein Switchmappingwörterbuch.</span><span class="sxs-lookup"><span data-stu-id="5438b-300">Create a switch mappings dictionary.</span></span> <span data-ttu-id="5438b-301">Im folgenden Beispiel werden zwei Switchmappings erstellt:</span><span class="sxs-lookup"><span data-stu-id="5438b-301">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="5438b-302">Wenn der Host erstellt wird, rufen Sie `AddCommandLine` mit dem Switchmappingwörterbuch auf:</span><span class="sxs-lookup"><span data-stu-id="5438b-302">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="5438b-303">Bei Apps, die Switchmappings verwenden, sollten im `CreateDefaultBuilder`-Aufruf keine Argumente übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="5438b-303">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="5438b-304">Der Aufruf von `CreateDefaultBuilder` der `AddCommandLine`-Methode umfasst keine zugeordneten Switches, und das Switchmappingwörterbuch kann nicht an `CreateDefaultBuilder` übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="5438b-304">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5438b-305">Die Lösung besteht nicht darin, die Argumente an `CreateDefaultBuilder` zu übergeben, sondern der Methode `AddCommandLine` der `ConfigurationBuilder`-Methode zu erlauben, sowohl die Argumente als auch das Switchmappingwörterbuch zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="5438b-305">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="5438b-306">Das erstellte Switchmappingwörterbuch enthält die in der folgenden Tabelle gezeigten Daten.</span><span class="sxs-lookup"><span data-stu-id="5438b-306">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="5438b-307">Key</span><span class="sxs-lookup"><span data-stu-id="5438b-307">Key</span></span>       | <span data-ttu-id="5438b-308">Wert</span><span class="sxs-lookup"><span data-stu-id="5438b-308">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="5438b-309">Wenn Switches zugeordnete Schlüssel beim Start der App verwendet werden, erhält die Konfiguration den Konfigurationswert auf dem vom Wörterbuch bereitgestellten Schlüssel:</span><span class="sxs-lookup"><span data-stu-id="5438b-309">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="5438b-310">Nach dem Ausführen des obigen Befehls enthält die Konfiguration die in der folgenden Tabelle angegebenen Werte.</span><span class="sxs-lookup"><span data-stu-id="5438b-310">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="5438b-311">Key</span><span class="sxs-lookup"><span data-stu-id="5438b-311">Key</span></span>               | <span data-ttu-id="5438b-312">Wert</span><span class="sxs-lookup"><span data-stu-id="5438b-312">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="5438b-313">Umgebungsvariablen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5438b-313">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="5438b-314"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="5438b-314">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="5438b-315">Um die Umgebungsvariablenkonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="5438b-315">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="5438b-316">[Azure App Service](https://azure.microsoft.com/services/app-service/) ermöglicht Ihnen Umgebungsvariablen im Azure-Portal festzulegen, die die App-Konfiguration mit dem Umgebungsvariablen-Konfigurationsanbieter überschreiben können.</span><span class="sxs-lookup"><span data-stu-id="5438b-316">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits you to set environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="5438b-317">Weitere Informationen finden Sie unter [Azure-Apps: Überschreiben der App-Konfiguration im Azure-Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="5438b-317">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5438b-318">`AddEnvironmentVariables` wird zum Laden von Umgebungsvariablen verwendet, die das Präfix `DOTNET_` für die [Hostkonfiguration](#host-versus-app-configuration) aufweisen, wenn ein neuer Host-Generator mit dem [generischen Host](xref:fundamentals/host/generic-host) initialisiert und `CreateDefaultBuilder` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="5438b-318">`AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Generic Host](xref:fundamentals/host/generic-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="5438b-319">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="5438b-319">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5438b-320">`AddEnvironmentVariables` wird zum Laden von Umgebungsvariablen verwendet, die das Präfix `ASPNETCORE_` für die [Hostkonfiguration](#host-versus-app-configuration) aufweisen, wenn ein neuer Host-Generator mit dem [Webhost](xref:fundamentals/host/web-host) initialisiert und `CreateDefaultBuilder` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="5438b-320">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="5438b-321">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="5438b-321">For more information, see the [Default configuration](#default-configuration) section.</span></span>

::: moniker-end

<span data-ttu-id="5438b-322">`CreateDefaultBuilder` lädt außerdem:</span><span class="sxs-lookup"><span data-stu-id="5438b-322">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="5438b-323">App-Konfiguration über Umgebungsvariablen ohne Präfix durch Aufruf von `AddEnvironmentVariables` ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="5438b-323">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="5438b-324">Optionale Konfiguration aus den Dateien *appsettings.json* und *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="5438b-324">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="5438b-325">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) in der Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="5438b-325">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="5438b-326">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="5438b-326">Command-line arguments.</span></span>

<span data-ttu-id="5438b-327">Der Umgebungsvariablen-Konfigurationsanbieter wird aufgerufen, nachdem die Konfiguration aus Benutzergeheimnissen und *appsettings*-Dateien erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="5438b-327">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="5438b-328">Das Aufrufen des Anbieters an dieser Stelle ermöglicht den während der Laufzeit gelesenen Umgebungsvariablen, die von Benutzergeheimnissen und *appsettings*-Dateien festgelegte Konfiguration zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="5438b-328">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="5438b-329">Wenn Sie App-Konfigurationen aus zusätzlichen Umgebungsvariablen bereitstellen müssen, rufen Sie die zusätzlichen Anbieter der App in `ConfigureAppConfiguration` auf, und rufen Sie `AddEnvironmentVariables` mit dem Präfix auf.</span><span class="sxs-lookup"><span data-stu-id="5438b-329">If you need to provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call additional providers here as needed.
    // Call AddEnvironmentVariables last if you need to allow
    // environment variables to override values from other 
    // providers.
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
}
```

<span data-ttu-id="5438b-330">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="5438b-330">**Example**</span></span>

<span data-ttu-id="5438b-331">Die Beispiel-App nutzt zum Erstellen des Hosts die statische Hilfsmethode `CreateDefaultBuilder`, die einen Aufruf von `AddEnvironmentVariables` enthält.</span><span class="sxs-lookup"><span data-stu-id="5438b-331">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="5438b-332">Führen Sie die Beispiel-App aus.</span><span class="sxs-lookup"><span data-stu-id="5438b-332">Run the sample app.</span></span> <span data-ttu-id="5438b-333">Öffnen Sie einen Browser für die App unter `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="5438b-333">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="5438b-334">Beachten Sie, dass die Ausgabe das Schlüssel-Wert-Paar für die Umgebungsvariable `ENVIRONMENT` enthält.</span><span class="sxs-lookup"><span data-stu-id="5438b-334">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="5438b-335">Der Wert entspricht der Umgebung, in der die App ausgeführt wird. In der Regel ist das `Development` bei lokaler Ausführung.</span><span class="sxs-lookup"><span data-stu-id="5438b-335">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="5438b-336">Um die Liste der von der App gerenderten Umgebungsvariablen kurz zu halten, filtert die App Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="5438b-336">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="5438b-337">Weitere Informationen finden Sie in der Datei *Pages/Index.cshtml.cs* der Beispiel-App.</span><span class="sxs-lookup"><span data-stu-id="5438b-337">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="5438b-338">Wenn Sie alle für die App verfügbaren Umgebungsvariablen verfügbar machen möchten, ändern Sie `FilteredConfiguration` in *Pages/Index.cshtml.cs* wie folgt:</span><span class="sxs-lookup"><span data-stu-id="5438b-338">If you wish to expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="5438b-339">Präfixe</span><span class="sxs-lookup"><span data-stu-id="5438b-339">Prefixes</span></span>

<span data-ttu-id="5438b-340">Umgebungsvariablen, die in die Konfiguration der App geladen werden, werden gefiltert, wenn Sie ein Präfix für die Methode `AddEnvironmentVariables` angeben.</span><span class="sxs-lookup"><span data-stu-id="5438b-340">Environment variables loaded into the app's configuration are filtered when you supply a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="5438b-341">Um beispielsweise Umgebungsvariablen nach dem Präfix `CUSTOM_` zu filtern, geben Sie das Präfix für den Konfigurationsanbieter an:</span><span class="sxs-lookup"><span data-stu-id="5438b-341">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="5438b-342">Das Präfix wird beim Erstellen der Schlüssel-Wert-Paare der Konfiguration entfernt.</span><span class="sxs-lookup"><span data-stu-id="5438b-342">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="5438b-343">Beim Erstellen des Host-Generators wird die Hostkonfiguration durch Umgebungsvariablen bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="5438b-343">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="5438b-344">Weitere Informationen zu dem Präfix, das für diese Umgebungsvariablen verwendet wird, finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="5438b-344">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="5438b-345">**Präfixe für Verbindungszeichenfolgen**</span><span class="sxs-lookup"><span data-stu-id="5438b-345">**Connection string prefixes**</span></span>

<span data-ttu-id="5438b-346">Die Konfigurations-API verfügt über spezielle Verarbeitungsregeln für vier Umgebungsvariablen für Verbindungszeichenfolgen, die beim Konfigurieren von Azure-Verbindungszeichenfolgen für die App-Umgebung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5438b-346">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="5438b-347">Umgebungsvariablen mit Präfixen, die in der Tabelle aufgeführt werden, werden in die App geladen, wenn für `AddEnvironmentVariables` kein Präfix angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="5438b-347">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="5438b-348">Präfix für Verbindungszeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="5438b-348">Connection string prefix</span></span> | <span data-ttu-id="5438b-349">Anbieter</span><span class="sxs-lookup"><span data-stu-id="5438b-349">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="5438b-350">Benutzerdefinierter Anbieter</span><span class="sxs-lookup"><span data-stu-id="5438b-350">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="5438b-351">MySQL</span><span class="sxs-lookup"><span data-stu-id="5438b-351">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="5438b-352">Azure SQL-Datenbank</span><span class="sxs-lookup"><span data-stu-id="5438b-352">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="5438b-353">SQL Server</span><span class="sxs-lookup"><span data-stu-id="5438b-353">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="5438b-354">Wenn eine Umgebungsvariable entdeckt und mit einem der vier Präfixe aus der Tabelle in die Konfiguration geladen wird, tritt Folgendes ein:</span><span class="sxs-lookup"><span data-stu-id="5438b-354">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="5438b-355">Der Konfigurationsschlüssel wird durch Entfernen des Umgebungsvariablenpräfixes und Hinzufügen eines Konfigurationsschlüsselabschnitts (`ConnectionStrings`) erstellt.</span><span class="sxs-lookup"><span data-stu-id="5438b-355">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="5438b-356">Ein neues Konfigurations-Schlüssel-Wert-Paar wird erstellt, das den Datenbankverbindungsanbieter repräsentiert (mit Ausnahme des Präfixes `CUSTOMCONNSTR_`, für das kein Anbieter angegeben ist).</span><span class="sxs-lookup"><span data-stu-id="5438b-356">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="5438b-357">Umgebungsvariablenschlüssel</span><span class="sxs-lookup"><span data-stu-id="5438b-357">Environment variable key</span></span> | <span data-ttu-id="5438b-358">Konvertierter Konfigurationsschlüssel</span><span class="sxs-lookup"><span data-stu-id="5438b-358">Converted configuration key</span></span> | <span data-ttu-id="5438b-359">Anbieterkonfigurationseintrag</span><span class="sxs-lookup"><span data-stu-id="5438b-359">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_<KEY>`    | `ConnectionStrings:<KEY>`   | <span data-ttu-id="5438b-360">Konfigurationseintrag wurde nicht erstellt.</span><span class="sxs-lookup"><span data-stu-id="5438b-360">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_<KEY>`     | `ConnectionStrings:<KEY>`   | <span data-ttu-id="5438b-361">Schlüssel: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="5438b-361">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="5438b-362">Wert: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="5438b-362">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_<KEY>`  | `ConnectionStrings:<KEY>`   | <span data-ttu-id="5438b-363">Schlüssel: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="5438b-363">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="5438b-364">Wert: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="5438b-364">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_<KEY>`       | `ConnectionStrings:<KEY>`   | <span data-ttu-id="5438b-365">Schlüssel: `ConnectionStrings:<KEY>_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="5438b-365">Key: `ConnectionStrings:<KEY>_ProviderName`:</span></span><br><span data-ttu-id="5438b-366">Wert: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="5438b-366">Value: `System.Data.SqlClient`</span></span>  |

## <a name="file-configuration-provider"></a><span data-ttu-id="5438b-367">Dateikonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5438b-367">File Configuration Provider</span></span>

<span data-ttu-id="5438b-368"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> ist die Basisklasse für das Laden einer Konfiguration aus dem Dateisystem.</span><span class="sxs-lookup"><span data-stu-id="5438b-368"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="5438b-369">Die folgenden Konfigurationsanbieter sind für bestimmte Dateitypen vorgesehen:</span><span class="sxs-lookup"><span data-stu-id="5438b-369">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="5438b-370">INI-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5438b-370">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="5438b-371">JSON-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5438b-371">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="5438b-372">XML-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5438b-372">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="5438b-373">INI-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5438b-373">INI Configuration Provider</span></span>

<span data-ttu-id="5438b-374"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der INI-Datei.</span><span class="sxs-lookup"><span data-stu-id="5438b-374">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="5438b-375">Um die INI-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="5438b-375">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="5438b-376">Der Doppelpunkt kann in einer INI-Dateikonfiguration als Abschnittstrennzeichen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5438b-376">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="5438b-377">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="5438b-377">Overloads permit specifying:</span></span>

* <span data-ttu-id="5438b-378">Ob die Datei optional ist</span><span class="sxs-lookup"><span data-stu-id="5438b-378">Whether the file is optional.</span></span>
* <span data-ttu-id="5438b-379">Ob die Konfiguration bei Dateiänderungen neu geladen wird</span><span class="sxs-lookup"><span data-stu-id="5438b-379">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="5438b-380">Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird</span><span class="sxs-lookup"><span data-stu-id="5438b-380">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="5438b-381">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="5438b-381">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="5438b-382">Ein allgemeines Beispiel einer INI-Konfigurationsdatei:</span><span class="sxs-lookup"><span data-stu-id="5438b-382">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="5438b-383">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="5438b-383">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="5438b-384">section0:key0</span><span class="sxs-lookup"><span data-stu-id="5438b-384">section0:key0</span></span>
* <span data-ttu-id="5438b-385">section0:key1</span><span class="sxs-lookup"><span data-stu-id="5438b-385">section0:key1</span></span>
* <span data-ttu-id="5438b-386">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="5438b-386">section1:subsection:key</span></span>
* <span data-ttu-id="5438b-387">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="5438b-387">section2:subsection0:key</span></span>
* <span data-ttu-id="5438b-388">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="5438b-388">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="5438b-389">JSON-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5438b-389">JSON Configuration Provider</span></span>

<span data-ttu-id="5438b-390"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der JSON-Datei.</span><span class="sxs-lookup"><span data-stu-id="5438b-390">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="5438b-391">Um die JSON-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="5438b-391">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="5438b-392">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="5438b-392">Overloads permit specifying:</span></span>

* <span data-ttu-id="5438b-393">Ob die Datei optional ist</span><span class="sxs-lookup"><span data-stu-id="5438b-393">Whether the file is optional.</span></span>
* <span data-ttu-id="5438b-394">Ob die Konfiguration bei Dateiänderungen neu geladen wird</span><span class="sxs-lookup"><span data-stu-id="5438b-394">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="5438b-395">Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird</span><span class="sxs-lookup"><span data-stu-id="5438b-395">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="5438b-396">`AddJsonFile` wird automatisch zweimal aufgerufen, wenn Sie einen neuen Host-Generator mit `CreateDefaultBuilder` initialisieren.</span><span class="sxs-lookup"><span data-stu-id="5438b-396">`AddJsonFile` is automatically called twice when you initialize a new host builder with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="5438b-397">Die Methode wird aufgerufen, um die Konfiguration aus folgenden Dateien zu laden:</span><span class="sxs-lookup"><span data-stu-id="5438b-397">The method is called to load configuration from:</span></span>

* <span data-ttu-id="5438b-398">*appsettings.json* &ndash; Diese Datei wird zuerst gelesen.</span><span class="sxs-lookup"><span data-stu-id="5438b-398">*appsettings.json* &ndash; This file is read first.</span></span> <span data-ttu-id="5438b-399">Die Umgebungsversion der Datei kann die Werte der Datei *appsettings.json* überschreiben.</span><span class="sxs-lookup"><span data-stu-id="5438b-399">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="5438b-400">*appsettings.{Environment}.json* &ndash; Die Umgebungsversion der Datei wird basierend auf [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) geladen.</span><span class="sxs-lookup"><span data-stu-id="5438b-400">*appsettings.{Environment}.json* &ndash; The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="5438b-401">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="5438b-401">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="5438b-402">`CreateDefaultBuilder` lädt außerdem:</span><span class="sxs-lookup"><span data-stu-id="5438b-402">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="5438b-403">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="5438b-403">Environment variables.</span></span>
* <span data-ttu-id="5438b-404">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) in der Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="5438b-404">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="5438b-405">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="5438b-405">Command-line arguments.</span></span>

<span data-ttu-id="5438b-406">Der JSON-Konfigurationsanbieter wird zuerst eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="5438b-406">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="5438b-407">Daher überschreiben Benutzergeheimnisse, Umgebungsvariablen und Befehlszeilenargumente die von *appsettings*-Dateien festgelegte Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="5438b-407">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="5438b-408">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um Konfiguration der App für andere Dateien als *appsettings.json* und *appsettings.{Environment}.json* anzugeben:</span><span class="sxs-lookup"><span data-stu-id="5438b-408">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="5438b-409">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="5438b-409">**Example**</span></span>

<span data-ttu-id="5438b-410">Die Beispiel-App nutzt zum Erstellen des Hosts die statische Hilfsmethode `CreateDefaultBuilder`, die zwei Aufrufe von `AddJsonFile` enthält.</span><span class="sxs-lookup"><span data-stu-id="5438b-410">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`.</span></span> <span data-ttu-id="5438b-411">Die Konfiguration wird aus *appsettings.json* und *appsettings.{Environment}.json* geladen.</span><span class="sxs-lookup"><span data-stu-id="5438b-411">Configuration is loaded from *appsettings.json* and *appsettings.{Environment}.json*.</span></span>

1. <span data-ttu-id="5438b-412">Führen Sie die Beispiel-App aus.</span><span class="sxs-lookup"><span data-stu-id="5438b-412">Run the sample app.</span></span> <span data-ttu-id="5438b-413">Öffnen Sie einen Browser für die App unter `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="5438b-413">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="5438b-414">Die Ausgabe enthält je nach Umgebung Schlüssel-Wert-Paare für die in der Tabelle dargestellte Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="5438b-414">Observe that the output contains key-value pairs for the configuration shown in the table depending on the environment.</span></span> <span data-ttu-id="5438b-415">Protokollierungskonfigurationsschlüssel verwenden den Doppelpunkt (`:`) als hierarchisches Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="5438b-415">Logging configuration keys use the colon (`:`) as a hierarchical separator.</span></span>

| <span data-ttu-id="5438b-416">Key</span><span class="sxs-lookup"><span data-stu-id="5438b-416">Key</span></span>                        | <span data-ttu-id="5438b-417">Entwicklungswert</span><span class="sxs-lookup"><span data-stu-id="5438b-417">Development Value</span></span> | <span data-ttu-id="5438b-418">Produktionswert</span><span class="sxs-lookup"><span data-stu-id="5438b-418">Production Value</span></span> |
| -------------------------- | :---------------: | :--------------: |
| <span data-ttu-id="5438b-419">Logging:LogLevel:System</span><span class="sxs-lookup"><span data-stu-id="5438b-419">Logging:LogLevel:System</span></span>    | <span data-ttu-id="5438b-420">Information</span><span class="sxs-lookup"><span data-stu-id="5438b-420">Information</span></span>       | <span data-ttu-id="5438b-421">Information</span><span class="sxs-lookup"><span data-stu-id="5438b-421">Information</span></span>      |
| <span data-ttu-id="5438b-422">Logging:LogLevel:Microsoft</span><span class="sxs-lookup"><span data-stu-id="5438b-422">Logging:LogLevel:Microsoft</span></span> | <span data-ttu-id="5438b-423">Information</span><span class="sxs-lookup"><span data-stu-id="5438b-423">Information</span></span>       | <span data-ttu-id="5438b-424">Information</span><span class="sxs-lookup"><span data-stu-id="5438b-424">Information</span></span>      |
| <span data-ttu-id="5438b-425">Logging:LogLevel:Default</span><span class="sxs-lookup"><span data-stu-id="5438b-425">Logging:LogLevel:Default</span></span>   | <span data-ttu-id="5438b-426">Debug</span><span class="sxs-lookup"><span data-stu-id="5438b-426">Debug</span></span>             | <span data-ttu-id="5438b-427">Fehler</span><span class="sxs-lookup"><span data-stu-id="5438b-427">Error</span></span>            |
| <span data-ttu-id="5438b-428">AllowedHosts</span><span class="sxs-lookup"><span data-stu-id="5438b-428">AllowedHosts</span></span>               | *                 | *                |

### <a name="xml-configuration-provider"></a><span data-ttu-id="5438b-429">XML-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5438b-429">XML Configuration Provider</span></span>

<span data-ttu-id="5438b-430"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der XML-Datei.</span><span class="sxs-lookup"><span data-stu-id="5438b-430">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="5438b-431">Um die XML-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="5438b-431">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="5438b-432">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="5438b-432">Overloads permit specifying:</span></span>

* <span data-ttu-id="5438b-433">Ob die Datei optional ist</span><span class="sxs-lookup"><span data-stu-id="5438b-433">Whether the file is optional.</span></span>
* <span data-ttu-id="5438b-434">Ob die Konfiguration bei Dateiänderungen neu geladen wird</span><span class="sxs-lookup"><span data-stu-id="5438b-434">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="5438b-435">Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird</span><span class="sxs-lookup"><span data-stu-id="5438b-435">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="5438b-436">Der Stammknoten der Konfigurationsdatei wird beim Erstellen der Konfigurations-Schlüssel-Wert-Paare ignoriert.</span><span class="sxs-lookup"><span data-stu-id="5438b-436">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="5438b-437">Geben Sie keine Dokumenttypdefinition (DTD) und keinen Namespace in der Datei an.</span><span class="sxs-lookup"><span data-stu-id="5438b-437">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="5438b-438">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="5438b-438">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="5438b-439">XML-Konfigurationsdateien können unterschiedliche Elementnamen für wiederholte Abschnitte verwenden:</span><span class="sxs-lookup"><span data-stu-id="5438b-439">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="5438b-440">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="5438b-440">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="5438b-441">section0:key0</span><span class="sxs-lookup"><span data-stu-id="5438b-441">section0:key0</span></span>
* <span data-ttu-id="5438b-442">section0:key1</span><span class="sxs-lookup"><span data-stu-id="5438b-442">section0:key1</span></span>
* <span data-ttu-id="5438b-443">section1:key0</span><span class="sxs-lookup"><span data-stu-id="5438b-443">section1:key0</span></span>
* <span data-ttu-id="5438b-444">section1:key1</span><span class="sxs-lookup"><span data-stu-id="5438b-444">section1:key1</span></span>

<span data-ttu-id="5438b-445">Wiederholte Elemente mit den gleichen Elementnamen funktionieren, wenn das `name`-Attribut zur Unterscheidung der Elemente verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="5438b-445">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="5438b-446">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="5438b-446">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="5438b-447">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="5438b-447">section:section0:key:key0</span></span>
* <span data-ttu-id="5438b-448">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="5438b-448">section:section0:key:key1</span></span>
* <span data-ttu-id="5438b-449">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="5438b-449">section:section1:key:key0</span></span>
* <span data-ttu-id="5438b-450">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="5438b-450">section:section1:key:key1</span></span>

<span data-ttu-id="5438b-451">Mit Attributen können Werte bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="5438b-451">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="5438b-452">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="5438b-452">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="5438b-453">key:attribute</span><span class="sxs-lookup"><span data-stu-id="5438b-453">key:attribute</span></span>
* <span data-ttu-id="5438b-454">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="5438b-454">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="5438b-455">Schlüssel-pro-Datei-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5438b-455">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="5438b-456"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> verwendet Verzeichnisdateien als Konfigurations-Schlüssel-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="5438b-456">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="5438b-457">Der Schlüssel ist der Dateiname.</span><span class="sxs-lookup"><span data-stu-id="5438b-457">The key is the file name.</span></span> <span data-ttu-id="5438b-458">Der Wert enthält den Inhalt der Datei.</span><span class="sxs-lookup"><span data-stu-id="5438b-458">The value contains the file's contents.</span></span> <span data-ttu-id="5438b-459">Der Schlüssel-pro-Datei-Konfigurationsanbieter wird in Docker-Hostingszenarien verwendet.</span><span class="sxs-lookup"><span data-stu-id="5438b-459">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="5438b-460">Um die Schlüssel-pro-Datei-Konfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="5438b-460">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="5438b-461">Der `directoryPath` zu den Dateien muss ein absoluter Pfad sein.</span><span class="sxs-lookup"><span data-stu-id="5438b-461">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="5438b-462">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="5438b-462">Overloads permit specifying:</span></span>

* <span data-ttu-id="5438b-463">Einen `Action<KeyPerFileConfigurationSource>`-Delegat, der die Quelle konfiguriert</span><span class="sxs-lookup"><span data-stu-id="5438b-463">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="5438b-464">Ob das Verzeichnis optional ist; und den Pfad zum Verzeichnis</span><span class="sxs-lookup"><span data-stu-id="5438b-464">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="5438b-465">Der doppelte Unterstrich (`__`) wird als Trennzeichen für Konfigurationsschlüssel in Dateinamen verwendet.</span><span class="sxs-lookup"><span data-stu-id="5438b-465">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="5438b-466">Der Dateiname `Logging__LogLevel__System` erzeugt z.B. den Konfigurationsschlüssel `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="5438b-466">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="5438b-467">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="5438b-467">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="5438b-468">Speicherkonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5438b-468">Memory Configuration Provider</span></span>

<span data-ttu-id="5438b-469"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> verwendet eine In-Memory-Sammlung für Konfigurations-Schlüssel-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="5438b-469">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="5438b-470">Um die In-Memory-Sammlungskonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="5438b-470">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="5438b-471">Der Konfigurationsanbieter kann mit `IEnumerable<KeyValuePair<String,String>>` initialisiert werden.</span><span class="sxs-lookup"><span data-stu-id="5438b-471">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="5438b-472">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben.</span><span class="sxs-lookup"><span data-stu-id="5438b-472">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="5438b-473">Im folgenden Beispiel wird ein Konfigurationswörterbuch erstellt:</span><span class="sxs-lookup"><span data-stu-id="5438b-473">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="5438b-474">Das Wörterbuch wird mit einem Aufruf von `AddInMemoryCollection` verwendet, um die Konfiguration bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="5438b-474">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="5438b-475">GetValue</span><span class="sxs-lookup"><span data-stu-id="5438b-475">GetValue</span></span>

<span data-ttu-id="5438b-476">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahiert einen einzelnen Wert aus der Konfiguration mit einem angegebenen Schlüssel und konvertiert ihn in den angegebenen Typ, der kein Auflistungstyp ist.</span><span class="sxs-lookup"><span data-stu-id="5438b-476">[ConfigurationBinder.GetValue\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="5438b-477">Eine Überladung akzeptiert einen Standardwert.</span><span class="sxs-lookup"><span data-stu-id="5438b-477">An overload accepts a default value.</span></span>

<span data-ttu-id="5438b-478">Im Beispiel unten geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="5438b-478">The following example:</span></span>

* <span data-ttu-id="5438b-479">Der Zeichenfolgenwert aus der Konfiguration wird mit dem Schlüssel `NumberKey` extrahiert.</span><span class="sxs-lookup"><span data-stu-id="5438b-479">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="5438b-480">Wenn `NumberKey` nicht in den Konfigurationsschlüsseln gefunden wird, wird der Standardwert `99` verwendet.</span><span class="sxs-lookup"><span data-stu-id="5438b-480">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="5438b-481">Der Typ für den Wert wird als `int` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="5438b-481">Types the value as an `int`.</span></span>
* <span data-ttu-id="5438b-482">Der Wert wird in der `NumberConfig`-Eigenschaft für die Verwendung durch die Seite gespeichert.</span><span class="sxs-lookup"><span data-stu-id="5438b-482">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="5438b-483">GetSection, GetChildren und Exists</span><span class="sxs-lookup"><span data-stu-id="5438b-483">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="5438b-484">Betrachten Sie für die folgenden Beispiele die JSON-Datei unten.</span><span class="sxs-lookup"><span data-stu-id="5438b-484">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="5438b-485">Vier Schlüssel befinden sich in zwei Abschnitten, von denen einer ein Paar Unterabschnitte enthält:</span><span class="sxs-lookup"><span data-stu-id="5438b-485">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="5438b-486">Wenn die Datei in die Konfiguration gelesen wird, werden die folgenden eindeutigen hierarchischen Schlüssel erstellt, um die Konfigurationswerte zu enthalten:</span><span class="sxs-lookup"><span data-stu-id="5438b-486">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="5438b-487">section0:key0</span><span class="sxs-lookup"><span data-stu-id="5438b-487">section0:key0</span></span>
* <span data-ttu-id="5438b-488">section0:key1</span><span class="sxs-lookup"><span data-stu-id="5438b-488">section0:key1</span></span>
* <span data-ttu-id="5438b-489">section1:key0</span><span class="sxs-lookup"><span data-stu-id="5438b-489">section1:key0</span></span>
* <span data-ttu-id="5438b-490">section1:key1</span><span class="sxs-lookup"><span data-stu-id="5438b-490">section1:key1</span></span>
* <span data-ttu-id="5438b-491">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="5438b-491">section2:subsection0:key0</span></span>
* <span data-ttu-id="5438b-492">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="5438b-492">section2:subsection0:key1</span></span>
* <span data-ttu-id="5438b-493">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="5438b-493">section2:subsection1:key0</span></span>
* <span data-ttu-id="5438b-494">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="5438b-494">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="5438b-495">GetSection</span><span class="sxs-lookup"><span data-stu-id="5438b-495">GetSection</span></span>

<span data-ttu-id="5438b-496">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrahiert einen Konfigurationsunterabschnitt mit dem angegebenen Unterabschnittsschlüssel.</span><span class="sxs-lookup"><span data-stu-id="5438b-496">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="5438b-497">Um ein <xref:Microsoft.Extensions.Configuration.IConfigurationSection>-Element zurückzugeben, das nur die Schlüssel-Wert-Paare in `section1` enthält, rufen Sie `GetSection` auf, und geben Sie den Abschnittsnamen an:</span><span class="sxs-lookup"><span data-stu-id="5438b-497">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="5438b-498">Das Element `configSection` weist keinen Wert auf, nur einen Schlüssel und einen Pfad.</span><span class="sxs-lookup"><span data-stu-id="5438b-498">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="5438b-499">Um die Werte für Schlüssel in `section2:subsection0` zu erhalten, rufen Sie `GetSection` auf, und geben Sie den Abschnittspfad an:</span><span class="sxs-lookup"><span data-stu-id="5438b-499">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="5438b-500">`GetSection` gibt nie `null` zurück.</span><span class="sxs-lookup"><span data-stu-id="5438b-500">`GetSection` never returns `null`.</span></span> <span data-ttu-id="5438b-501">Wenn kein entsprechender Abschnitt gefunden wird, wird ein leeres `IConfigurationSection`-Element zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="5438b-501">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="5438b-502">Wenn `GetSection` einen entsprechenden Abschnitt zurückgibt, wird <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> nicht aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="5438b-502">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="5438b-503">Eine Eigenschaft <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> und <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> werden zurückgegeben, wenn der Abschnitt vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="5438b-503">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="5438b-504">GetChildren</span><span class="sxs-lookup"><span data-stu-id="5438b-504">GetChildren</span></span>

<span data-ttu-id="5438b-505">Ein Aufruf von [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) auf `section2` erhält `IEnumerable<IConfigurationSection>` mit folgenden Elementen:</span><span class="sxs-lookup"><span data-stu-id="5438b-505">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="5438b-506">Vorhanden</span><span class="sxs-lookup"><span data-stu-id="5438b-506">Exists</span></span>

<span data-ttu-id="5438b-507">Verwenden Sie [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) um zu bestimmen, ob ein Konfigurationsabschnitt vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="5438b-507">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="5438b-508">Im Falle der Beispieldaten ist `sectionExists` `false`, weil in den Konfigurationsdaten kein Abschnitt `section2:subsection2` vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="5438b-508">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-a-class"></a><span data-ttu-id="5438b-509">Binden an eine Klasse</span><span class="sxs-lookup"><span data-stu-id="5438b-509">Bind to a class</span></span>

<span data-ttu-id="5438b-510">Die Konfiguration kann mit *Optionsmustern* an Klassen gebunden werden, die Gruppen von verwandten Einstellungen darstellen.</span><span class="sxs-lookup"><span data-stu-id="5438b-510">Configuration can be bound to classes that represent groups of related settings using the *options pattern*.</span></span> <span data-ttu-id="5438b-511">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="5438b-511">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="5438b-512">Konfigurationswerte werden als Zeichenfolgen zurückgegeben, doch das Aufrufen von <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> ermöglicht die Erstellung von [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object)-Objekten.</span><span class="sxs-lookup"><span data-stu-id="5438b-512">Configuration values are returned as strings, but calling <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> enables the construction of [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) objects.</span></span>

<span data-ttu-id="5438b-513">Die Beispiel-App enthält ein `Starship`-Modell (*Models/Starship.cs*):</span><span class="sxs-lookup"><span data-stu-id="5438b-513">The sample app contains a `Starship` model (*Models/Starship.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/Starship.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="5438b-514">Der Abschnitt `starship` der Datei *starship.json* erstellt die Konfiguration, wenn die Beispiel-App den JSON-Konfigurationsanbieter zum Laden der Konfiguration verwendet:</span><span class="sxs-lookup"><span data-stu-id="5438b-514">The `starship` section of the *starship.json* file creates the configuration when the sample app uses the JSON Configuration Provider to load the configuration:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/starship.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/starship.json)]

::: moniker-end

<span data-ttu-id="5438b-515">Die folgenden Schlüssel-Wert-Paare werden für die Konfiguration erstellt:</span><span class="sxs-lookup"><span data-stu-id="5438b-515">The following configuration key-value pairs are created:</span></span>

| <span data-ttu-id="5438b-516">Key</span><span class="sxs-lookup"><span data-stu-id="5438b-516">Key</span></span>                   | <span data-ttu-id="5438b-517">Wert</span><span class="sxs-lookup"><span data-stu-id="5438b-517">Value</span></span>                                             |
| --------------------- | ------------------------------------------------- |
| <span data-ttu-id="5438b-518">starship:name</span><span class="sxs-lookup"><span data-stu-id="5438b-518">starship:name</span></span>         | <span data-ttu-id="5438b-519">USS Enterprise</span><span class="sxs-lookup"><span data-stu-id="5438b-519">USS Enterprise</span></span>                                    |
| <span data-ttu-id="5438b-520">starship:registry</span><span class="sxs-lookup"><span data-stu-id="5438b-520">starship:registry</span></span>     | <span data-ttu-id="5438b-521">NCC-1701</span><span class="sxs-lookup"><span data-stu-id="5438b-521">NCC-1701</span></span>                                          |
| <span data-ttu-id="5438b-522">starship:class</span><span class="sxs-lookup"><span data-stu-id="5438b-522">starship:class</span></span>        | <span data-ttu-id="5438b-523">Constitution</span><span class="sxs-lookup"><span data-stu-id="5438b-523">Constitution</span></span>                                      |
| <span data-ttu-id="5438b-524">starship:length</span><span class="sxs-lookup"><span data-stu-id="5438b-524">starship:length</span></span>       | <span data-ttu-id="5438b-525">304,8</span><span class="sxs-lookup"><span data-stu-id="5438b-525">304.8</span></span>                                             |
| <span data-ttu-id="5438b-526">starship:commissioned</span><span class="sxs-lookup"><span data-stu-id="5438b-526">starship:commissioned</span></span> | <span data-ttu-id="5438b-527">False</span><span class="sxs-lookup"><span data-stu-id="5438b-527">False</span></span>                                             |
| <span data-ttu-id="5438b-528">trademark</span><span class="sxs-lookup"><span data-stu-id="5438b-528">trademark</span></span>             | <span data-ttu-id="5438b-529">Paramount Pictures Corp. https://www.paramount.com</span><span class="sxs-lookup"><span data-stu-id="5438b-529">Paramount Pictures Corp. https://www.paramount.com</span></span> |

<span data-ttu-id="5438b-530">Die Beispiel-App ruft `GetSection` mit dem `starship`-Schlüssel auf.</span><span class="sxs-lookup"><span data-stu-id="5438b-530">The sample app calls `GetSection` with the `starship` key.</span></span> <span data-ttu-id="5438b-531">Die `starship`-Schlüssel-Wert-Paaren sind isoliert.</span><span class="sxs-lookup"><span data-stu-id="5438b-531">The `starship` key-value pairs are isolated.</span></span> <span data-ttu-id="5438b-532">Die `Bind`-Methode wird auf dem Unterabschnitt aufgerufen, der in einer Instanz der `Starship`-Klasse übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="5438b-532">The `Bind` method is called on the subsection passing in an instance of the `Starship` class.</span></span> <span data-ttu-id="5438b-533">Nach dem Binden der Instanzwerte wird die Instanz einer Eigenschaft zum Rendern zugewiesen:</span><span class="sxs-lookup"><span data-stu-id="5438b-533">After binding the instance values, the instance is assigned to a property for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_starship)]

::: moniker-end

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="5438b-534">Binden an ein Objektdiagramm</span><span class="sxs-lookup"><span data-stu-id="5438b-534">Bind to an object graph</span></span>

<span data-ttu-id="5438b-535"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> kann ein ganzes POCO-Objektdiagramm binden.</span><span class="sxs-lookup"><span data-stu-id="5438b-535"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span>

<span data-ttu-id="5438b-536">Das Beispiel enthält ein `TvShow`-Modell, dessen Objektdiagramm die Klassen `Metadata` und `Actors` enthält (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="5438b-536">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="5438b-537">Die Beispiel-App verfügt über eine *tvshow.xml*-Datei mit den Konfigurationsdaten:</span><span class="sxs-lookup"><span data-stu-id="5438b-537">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-xml[](index/samples/3.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

::: moniker-end

<span data-ttu-id="5438b-538">Die Konfiguration wird mit der `Bind`-Methode an das gesamte `TvShow`-Objektdiagramm gebunden.</span><span class="sxs-lookup"><span data-stu-id="5438b-538">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="5438b-539">Die gebundene Instanz ist einer Eigenschaft zum Rendern zugewiesen:</span><span class="sxs-lookup"><span data-stu-id="5438b-539">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="5438b-540">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) bindet den angegebenen Typ und gibt ihn zurück.</span><span class="sxs-lookup"><span data-stu-id="5438b-540">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="5438b-541">`Get<T>` ist praktischer als `Bind`.</span><span class="sxs-lookup"><span data-stu-id="5438b-541">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="5438b-542">Der folgende Code zeigt die Verwendung von `Get<T>` mit dem vorherigen Beispiel, sodass die gebundene Instanz direkt der Eigenschaft zum Rendern zugewiesen werden kann:</span><span class="sxs-lookup"><span data-stu-id="5438b-542">The following code shows how to use `Get<T>` with the preceding example, which allows the bound instance to be directly assigned to the property used for rendering:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

::: moniker-end

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="5438b-543">Binden eines Arrays an eine Klasse</span><span class="sxs-lookup"><span data-stu-id="5438b-543">Bind an array to a class</span></span>

<span data-ttu-id="5438b-544">*Die Beispiel-App veranschaulicht die in diesem Abschnitt erläuterten Konzepte.*</span><span class="sxs-lookup"><span data-stu-id="5438b-544">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="5438b-545"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> unterstützt das Binden von Arrays an Objekte mit Arrayindizes in Konfigurationsschlüsseln.</span><span class="sxs-lookup"><span data-stu-id="5438b-545">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="5438b-546">Jedes Arrayformat, das ein numerisches Schlüsselsegment (`:0:`, `:1:`, &hellip; `:{n}:`) verfügbar macht, kann ein Array an ein POCO-Klassenarray binden.</span><span class="sxs-lookup"><span data-stu-id="5438b-546">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="5438b-547">Das Binden ist standardmäßig möglich.</span><span class="sxs-lookup"><span data-stu-id="5438b-547">Binding is provided by convention.</span></span> <span data-ttu-id="5438b-548">Benutzerdefinierte Konfigurationsanbieter sind nicht erforderlich, um Arraybindung zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="5438b-548">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="5438b-549">**In-Memory-Arrayverarbeitung**</span><span class="sxs-lookup"><span data-stu-id="5438b-549">**In-memory array processing**</span></span>

<span data-ttu-id="5438b-550">Betrachten Sie die Konfigurationsschlüssel und -werte in der folgenden Tabelle.</span><span class="sxs-lookup"><span data-stu-id="5438b-550">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="5438b-551">Key</span><span class="sxs-lookup"><span data-stu-id="5438b-551">Key</span></span>             | <span data-ttu-id="5438b-552">Wert</span><span class="sxs-lookup"><span data-stu-id="5438b-552">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="5438b-553">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="5438b-553">array:entries:0</span></span> | <span data-ttu-id="5438b-554">value0</span><span class="sxs-lookup"><span data-stu-id="5438b-554">value0</span></span> |
| <span data-ttu-id="5438b-555">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="5438b-555">array:entries:1</span></span> | <span data-ttu-id="5438b-556">value1</span><span class="sxs-lookup"><span data-stu-id="5438b-556">value1</span></span> |
| <span data-ttu-id="5438b-557">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="5438b-557">array:entries:2</span></span> | <span data-ttu-id="5438b-558">value2</span><span class="sxs-lookup"><span data-stu-id="5438b-558">value2</span></span> |
| <span data-ttu-id="5438b-559">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="5438b-559">array:entries:4</span></span> | <span data-ttu-id="5438b-560">value4</span><span class="sxs-lookup"><span data-stu-id="5438b-560">value4</span></span> |
| <span data-ttu-id="5438b-561">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="5438b-561">array:entries:5</span></span> | <span data-ttu-id="5438b-562">value5</span><span class="sxs-lookup"><span data-stu-id="5438b-562">value5</span></span> |

<span data-ttu-id="5438b-563">Diese Schlüssel und Werte werden mit dem Speicherkonfigurationsanbieter in die Beispiel-App geladen:</span><span class="sxs-lookup"><span data-stu-id="5438b-563">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

::: moniker-end

<span data-ttu-id="5438b-564">Das Array überspringt einen Wert für Index &num;3.</span><span class="sxs-lookup"><span data-stu-id="5438b-564">The array skips a value for index &num;3.</span></span> <span data-ttu-id="5438b-565">Der Konfigurationsbinder kann weder NULL-Werte binden noch NULL-Einträge in gebundenen Objekten erstellen. Dies wird deutlich, wenn das Ergebnis der Bindung dieses Arrays an ein Objekt demonstriert wird.</span><span class="sxs-lookup"><span data-stu-id="5438b-565">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="5438b-566">In der Beispiel-App ist eine POCO-Klasse verfügbar, um die gebundenen Konfigurationsdaten zu enthalten:</span><span class="sxs-lookup"><span data-stu-id="5438b-566">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="5438b-567">Die Konfigurationsdaten werden an das Objekt gebunden:</span><span class="sxs-lookup"><span data-stu-id="5438b-567">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="5438b-568">Die [ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)-Syntax kann auch verwendet werden, wodurch ein kompakterer Code entsteht:</span><span class="sxs-lookup"><span data-stu-id="5438b-568">[ConfigurationBinder.Get\<T>](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

::: moniker-end

<span data-ttu-id="5438b-569">Das gebundene Objekt, eine Instanz von `ArrayExample`, empfängt die Arraydaten aus der Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="5438b-569">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="5438b-570">`ArrayExample.Entries`-Index</span><span class="sxs-lookup"><span data-stu-id="5438b-570">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="5438b-571">`ArrayExample.Entries`-Wert</span><span class="sxs-lookup"><span data-stu-id="5438b-571">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="5438b-572">0</span><span class="sxs-lookup"><span data-stu-id="5438b-572">0</span></span>                            | <span data-ttu-id="5438b-573">value0</span><span class="sxs-lookup"><span data-stu-id="5438b-573">value0</span></span>                       |
| <span data-ttu-id="5438b-574">1</span><span class="sxs-lookup"><span data-stu-id="5438b-574">1</span></span>                            | <span data-ttu-id="5438b-575">value1</span><span class="sxs-lookup"><span data-stu-id="5438b-575">value1</span></span>                       |
| <span data-ttu-id="5438b-576">2</span><span class="sxs-lookup"><span data-stu-id="5438b-576">2</span></span>                            | <span data-ttu-id="5438b-577">value2</span><span class="sxs-lookup"><span data-stu-id="5438b-577">value2</span></span>                       |
| <span data-ttu-id="5438b-578">3</span><span class="sxs-lookup"><span data-stu-id="5438b-578">3</span></span>                            | <span data-ttu-id="5438b-579">value4</span><span class="sxs-lookup"><span data-stu-id="5438b-579">value4</span></span>                       |
| <span data-ttu-id="5438b-580">4</span><span class="sxs-lookup"><span data-stu-id="5438b-580">4</span></span>                            | <span data-ttu-id="5438b-581">value5</span><span class="sxs-lookup"><span data-stu-id="5438b-581">value5</span></span>                       |

<span data-ttu-id="5438b-582">Index &num;3 im gebundenen Objekt enthält die Konfigurationsdaten für den `array:4`-Konfigurationsschlüssel und die Wert für `value4`.</span><span class="sxs-lookup"><span data-stu-id="5438b-582">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="5438b-583">Beim Binden von Konfigurationsdaten, die ein Array enthalten, werden die Arrayindizes in den Konfigurationsschlüsseln lediglich zum Durchlaufen der Konfigurationsdaten beim Erstellen des Objekts verwendet.</span><span class="sxs-lookup"><span data-stu-id="5438b-583">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="5438b-584">Ein NULL-Wert kann in den Konfigurationsdaten nicht beibehalten werden, und ein NULL-Eintrag wird nicht in einem gebundenen Objekt erstellt, wenn ein Array in Konfigurationsschlüsseln mindestens einen Index überspringt.</span><span class="sxs-lookup"><span data-stu-id="5438b-584">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="5438b-585">Das fehlende Konfigurationselement für Index &num;3 kann vor dem Binden an die `ArrayExample`Instanz von jedem Konfigurationsanbieter bereitgestellt werden, der das richtige Schlüssel-Wert-Paar in der Konfiguration erzeugt.</span><span class="sxs-lookup"><span data-stu-id="5438b-585">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="5438b-586">Wenn das Beispiel einen zusätzlichen JSON-Konfigurationsanbieter mit dem fehlenden Schlüssel-Wert-Paar enthält, entspricht `ArrayExample.Entries` dem kompletten Konfigurationsarray:</span><span class="sxs-lookup"><span data-stu-id="5438b-586">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="5438b-587">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="5438b-587">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="5438b-588">In `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="5438b-588">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="5438b-589">Das Schlüssel-Wert-Paar in der Tabelle wird in die Konfiguration geladen.</span><span class="sxs-lookup"><span data-stu-id="5438b-589">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="5438b-590">Key</span><span class="sxs-lookup"><span data-stu-id="5438b-590">Key</span></span>             | <span data-ttu-id="5438b-591">Wert</span><span class="sxs-lookup"><span data-stu-id="5438b-591">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="5438b-592">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="5438b-592">array:entries:3</span></span> | <span data-ttu-id="5438b-593">value3</span><span class="sxs-lookup"><span data-stu-id="5438b-593">value3</span></span> |

<span data-ttu-id="5438b-594">Wenn die `ArrayExample`-Klasseninstanz gebunden wird, nachdem der JSON-Konfigurationsanbieter den Eintrag für Index &num;3 enthält, enthält das `ArrayExample.Entries`-Array den Wert.</span><span class="sxs-lookup"><span data-stu-id="5438b-594">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="5438b-595">`ArrayExample.Entries`-Index</span><span class="sxs-lookup"><span data-stu-id="5438b-595">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="5438b-596">`ArrayExample.Entries`-Wert</span><span class="sxs-lookup"><span data-stu-id="5438b-596">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="5438b-597">0</span><span class="sxs-lookup"><span data-stu-id="5438b-597">0</span></span>                            | <span data-ttu-id="5438b-598">value0</span><span class="sxs-lookup"><span data-stu-id="5438b-598">value0</span></span>                       |
| <span data-ttu-id="5438b-599">1</span><span class="sxs-lookup"><span data-stu-id="5438b-599">1</span></span>                            | <span data-ttu-id="5438b-600">value1</span><span class="sxs-lookup"><span data-stu-id="5438b-600">value1</span></span>                       |
| <span data-ttu-id="5438b-601">2</span><span class="sxs-lookup"><span data-stu-id="5438b-601">2</span></span>                            | <span data-ttu-id="5438b-602">value2</span><span class="sxs-lookup"><span data-stu-id="5438b-602">value2</span></span>                       |
| <span data-ttu-id="5438b-603">3</span><span class="sxs-lookup"><span data-stu-id="5438b-603">3</span></span>                            | <span data-ttu-id="5438b-604">value3</span><span class="sxs-lookup"><span data-stu-id="5438b-604">value3</span></span>                       |
| <span data-ttu-id="5438b-605">4</span><span class="sxs-lookup"><span data-stu-id="5438b-605">4</span></span>                            | <span data-ttu-id="5438b-606">value4</span><span class="sxs-lookup"><span data-stu-id="5438b-606">value4</span></span>                       |
| <span data-ttu-id="5438b-607">5</span><span class="sxs-lookup"><span data-stu-id="5438b-607">5</span></span>                            | <span data-ttu-id="5438b-608">value5</span><span class="sxs-lookup"><span data-stu-id="5438b-608">value5</span></span>                       |

<span data-ttu-id="5438b-609">**JSON-Arrayverarbeitung**</span><span class="sxs-lookup"><span data-stu-id="5438b-609">**JSON array processing**</span></span>

<span data-ttu-id="5438b-610">Wenn eine JSON-Datei ein Array enthält, werden Konfigurationsschlüssel für die Arrayelemente mit einem nullbasierten Abschnittsindex erstellt.</span><span class="sxs-lookup"><span data-stu-id="5438b-610">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="5438b-611">In der folgenden Konfigurationsdatei ist `subsection` ein Array:</span><span class="sxs-lookup"><span data-stu-id="5438b-611">In the following configuration file, `subsection` is an array:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/ConfigurationSample/json_array.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

::: moniker-end

<span data-ttu-id="5438b-612">Der JSON-Konfigurationsanbieter liest die Konfigurationsdaten in die folgenden Schlüssel-Wert-Paare:</span><span class="sxs-lookup"><span data-stu-id="5438b-612">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="5438b-613">Key</span><span class="sxs-lookup"><span data-stu-id="5438b-613">Key</span></span>                     | <span data-ttu-id="5438b-614">Wert</span><span class="sxs-lookup"><span data-stu-id="5438b-614">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="5438b-615">json_array:key</span><span class="sxs-lookup"><span data-stu-id="5438b-615">json_array:key</span></span>          | <span data-ttu-id="5438b-616">valueA</span><span class="sxs-lookup"><span data-stu-id="5438b-616">valueA</span></span> |
| <span data-ttu-id="5438b-617">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="5438b-617">json_array:subsection:0</span></span> | <span data-ttu-id="5438b-618">valueB</span><span class="sxs-lookup"><span data-stu-id="5438b-618">valueB</span></span> |
| <span data-ttu-id="5438b-619">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="5438b-619">json_array:subsection:1</span></span> | <span data-ttu-id="5438b-620">valueC</span><span class="sxs-lookup"><span data-stu-id="5438b-620">valueC</span></span> |
| <span data-ttu-id="5438b-621">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="5438b-621">json_array:subsection:2</span></span> | <span data-ttu-id="5438b-622">valueD</span><span class="sxs-lookup"><span data-stu-id="5438b-622">valueD</span></span> |

<span data-ttu-id="5438b-623">In der Beispiel-App können die Konfigurations-Schlüssel-Wert-Paare mit dieser POCO-Klasse gebunden werden:</span><span class="sxs-lookup"><span data-stu-id="5438b-623">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="5438b-624">Nach dem Binden enthält `JsonArrayExample.Key` den Wert `valueA`.</span><span class="sxs-lookup"><span data-stu-id="5438b-624">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="5438b-625">Die Unterabschnittwerte werden in der POCO-Arrayeigenschaft `Subsection` gespeichert.</span><span class="sxs-lookup"><span data-stu-id="5438b-625">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="5438b-626">`JsonArrayExample.Subsection`-Index</span><span class="sxs-lookup"><span data-stu-id="5438b-626">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="5438b-627">`JsonArrayExample.Subsection`-Wert</span><span class="sxs-lookup"><span data-stu-id="5438b-627">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="5438b-628">0</span><span class="sxs-lookup"><span data-stu-id="5438b-628">0</span></span>                                   | <span data-ttu-id="5438b-629">valueB</span><span class="sxs-lookup"><span data-stu-id="5438b-629">valueB</span></span>                              |
| <span data-ttu-id="5438b-630">1</span><span class="sxs-lookup"><span data-stu-id="5438b-630">1</span></span>                                   | <span data-ttu-id="5438b-631">valueC</span><span class="sxs-lookup"><span data-stu-id="5438b-631">valueC</span></span>                              |
| <span data-ttu-id="5438b-632">2</span><span class="sxs-lookup"><span data-stu-id="5438b-632">2</span></span>                                   | <span data-ttu-id="5438b-633">valueD</span><span class="sxs-lookup"><span data-stu-id="5438b-633">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="5438b-634">Benutzerdefinierter Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="5438b-634">Custom configuration provider</span></span>

<span data-ttu-id="5438b-635">Die Beispiel-App veranschaulicht, wie ein Standardkonfigurationsanbieter erstellt wird, der Konfigurations-Schlüssel-Wert-Paare aus einer Datenbank mit [Entity Framework (EF)](/ef/core/) liest.</span><span class="sxs-lookup"><span data-stu-id="5438b-635">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="5438b-636">Der Anbieter weist die folgenden Merkmale auf:</span><span class="sxs-lookup"><span data-stu-id="5438b-636">The provider has the following characteristics:</span></span>

* <span data-ttu-id="5438b-637">Die EF-In-Memory-Datenbank wird zu Demonstrationszwecken verwendet.</span><span class="sxs-lookup"><span data-stu-id="5438b-637">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="5438b-638">Um eine Datenbank zu verwenden, die eine Verbindungszeichenfolge benötigt, implementieren Sie einen sekundären `ConfigurationBuilder`, um die Verbindungszeichenfolge aus einem anderen Konfigurationsanbieter anzugeben.</span><span class="sxs-lookup"><span data-stu-id="5438b-638">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="5438b-639">Der Anbieter liest eine Datenbanktabelle beim Start in die Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="5438b-639">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="5438b-640">Der Anbieter fragt die Datenbank nicht pro Schlüssel ab.</span><span class="sxs-lookup"><span data-stu-id="5438b-640">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="5438b-641">Das erneute Laden bei Änderung ist nicht implementiert. Das heißt, das Aktualisieren der Datenbank nach App-Start hat keine Auswirkungen auf die App-Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="5438b-641">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="5438b-642">Definieren Sie eine `EFConfigurationValue`-Entität zum Speichern von Konfigurationswerten in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="5438b-642">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5438b-643">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="5438b-643">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="5438b-644">Fügen Sie `EFConfigurationContext` hinzu, um die konfigurierten Werte zu speichern und auf diese zugreifen.</span><span class="sxs-lookup"><span data-stu-id="5438b-644">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="5438b-645">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="5438b-645">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="5438b-646">Erstellen Sie eine Klasse, die das <xref:Microsoft.Extensions.Configuration.IConfigurationSource> implementiert.</span><span class="sxs-lookup"><span data-stu-id="5438b-646">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="5438b-647">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="5438b-647">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="5438b-648">Erstellen Sie den benutzerdefinierten Konfigurationsanbieter durch Vererbung von <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="5438b-648">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="5438b-649">Der Konfigurationsanbieter initialisiert die Datenbank, wenn diese leer ist.</span><span class="sxs-lookup"><span data-stu-id="5438b-649">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="5438b-650">Da [Konfigurationsschlüssel die Groß-/Kleinschreibung nicht beachten](#keys), wird das zum Initialisieren der Datenbank verwendete Wörterbuch mit der Vergleichsfunktion, die die Groß-/Kleinschreibung nicht beachtet, erstellt ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="5438b-650">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="5438b-651">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="5438b-651">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="5438b-652">Mit einer `AddEFConfiguration`-Erweiterungsmethode kann die Konfigurationsquelle `ConfigurationBuilder` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="5438b-652">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="5438b-653">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="5438b-653">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="5438b-654">Der folgende Code veranschaulicht die Verwendung des benutzerdefinierten Anbieters `EFConfigurationProvider` in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="5438b-654">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5438b-655">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="5438b-655">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="5438b-656">Fügen Sie `EFConfigurationContext` hinzu, um die konfigurierten Werte zu speichern und auf diese zugreifen.</span><span class="sxs-lookup"><span data-stu-id="5438b-656">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="5438b-657">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="5438b-657">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="5438b-658">Erstellen Sie eine Klasse, die das <xref:Microsoft.Extensions.Configuration.IConfigurationSource> implementiert.</span><span class="sxs-lookup"><span data-stu-id="5438b-658">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="5438b-659">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="5438b-659">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="5438b-660">Erstellen Sie den benutzerdefinierten Konfigurationsanbieter durch Vererbung von <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="5438b-660">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="5438b-661">Der Konfigurationsanbieter initialisiert die Datenbank, wenn diese leer ist.</span><span class="sxs-lookup"><span data-stu-id="5438b-661">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="5438b-662">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="5438b-662">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="5438b-663">Mit einer `AddEFConfiguration`-Erweiterungsmethode kann die Konfigurationsquelle `ConfigurationBuilder` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="5438b-663">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="5438b-664">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="5438b-664">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="5438b-665">Der folgende Code veranschaulicht die Verwendung des benutzerdefinierten Anbieters `EFConfigurationProvider` in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="5438b-665">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

::: moniker-end

## <a name="access-configuration-during-startup"></a><span data-ttu-id="5438b-666">Zugreifen auf die Konfiguration während des Starts</span><span class="sxs-lookup"><span data-stu-id="5438b-666">Access configuration during startup</span></span>

<span data-ttu-id="5438b-667">Fügen Sie `IConfiguration` in den `Startup`-Konstruktor ein, um auf Konfigurationswerte in `Startup.ConfigureServices` zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="5438b-667">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5438b-668">Um auf die Konfiguration in `Startup.Configure` zuzugreifen, fügen Sie entweder `IConfiguration` direkt in die Methode ein, oder verwenden Sie die Instanz aus dem Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="5438b-668">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="5438b-669">Ein Beispiel für den Zugriff auf die Konfiguration mit den Starthilfsmethoden finden Sie unter [Anwendungsstart: Hilfsmethoden](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="5438b-669">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="5438b-670">Zugreifen auf die Konfiguration auf einer Razor Pages-Seite oder in einer MVC-Ansicht</span><span class="sxs-lookup"><span data-stu-id="5438b-670">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="5438b-671">Um auf die Konfigurationseinstellungen auf einer Razor Pages-Seite oder in einer MVC-Ansicht zuzugreifen, fügen Sie eine [using-Anweisung](xref:mvc/views/razor#using) ([C#-Referenz: using-Anweisung](/dotnet/csharp/language-reference/keywords/using-directive)) für den [Microsoft.Extensions.Configuration-Namespace ](xref:Microsoft.Extensions.Configuration) hinzu, und fügen Sie <xref:Microsoft.Extensions.Configuration.IConfiguration> auf der Seite bzw. in der Ansicht ein.</span><span class="sxs-lookup"><span data-stu-id="5438b-671">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="5438b-672">Auf einer Razor Pages-Seite:</span><span class="sxs-lookup"><span data-stu-id="5438b-672">In a Razor Pages page:</span></span>

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

<span data-ttu-id="5438b-673">In einer MVC-Ansicht:</span><span class="sxs-lookup"><span data-stu-id="5438b-673">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="5438b-674">Hinzufügen von Konfigurationen aus einer externen Assembly</span><span class="sxs-lookup"><span data-stu-id="5438b-674">Add configuration from an external assembly</span></span>

<span data-ttu-id="5438b-675">Eine <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>-Implementierung ermöglicht das Hinzufügen von Erweiterungen zu einer App beim Start von einer externen Assembly außerhalb der `Startup`-Klasse der App aus.</span><span class="sxs-lookup"><span data-stu-id="5438b-675">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="5438b-676">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="5438b-676">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5438b-677">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="5438b-677">Additional resources</span></span>

* <xref:fundamentals/configuration/options>
