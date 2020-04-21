---
title: Sichere Speicherung von App-Geheimnissen in der Entwicklung in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie vertrauliche Informationen während der Entwicklung einer ASP.NET Core-App als App-Geheimnisse speichern und abrufen.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
uid: security/app-secrets
ms.openlocfilehash: 9d4e59c003afc253971ee64fce523c7188d3582a
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661801"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="6fc21-103">Sichere Speicherung von App-Geheimnissen in der Entwicklung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6fc21-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6fc21-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT), Kirk [Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27)und [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="6fc21-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="6fc21-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6fc21-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6fc21-106">In diesem Dokument werden Techniken zum Speichern und Abrufen vertraulicher Daten während der Entwicklung einer ASP.NET Core-App auf einem Entwicklungscomputer erläutert.</span><span class="sxs-lookup"><span data-stu-id="6fc21-106">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="6fc21-107">Speichern Sie niemals Kennwörter oder andere vertrauliche Daten im Quellcode.</span><span class="sxs-lookup"><span data-stu-id="6fc21-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="6fc21-108">Produktionsgeheimnisse sollten nicht für die Entwicklung oder den Test verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6fc21-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="6fc21-109">Geheimnisse sollten nicht mit der App bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="6fc21-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="6fc21-110">Stattdessen sollten Geheimnisse in der Produktionsumgebung über eine kontrollierte Mittelart wie Umgebungsvariablen, Azure Key Vault usw. zur Verfügung gestellt werden. Sie können Azure-Test- und Produktionsgeheimnisse beim [Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration)speichern und schützen.</span><span class="sxs-lookup"><span data-stu-id="6fc21-110">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="6fc21-111">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="6fc21-111">Environment variables</span></span>

<span data-ttu-id="6fc21-112">Umgebungsvariablen werden verwendet, um die Speicherung von App-Geheimnissen im Code oder in lokalen Konfigurationsdateien zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="6fc21-112">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="6fc21-113">Umgebungsvariablen überschreiben Konfigurationswerte für alle zuvor angegebenen Konfigurationsquellen.</span><span class="sxs-lookup"><span data-stu-id="6fc21-113">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="6fc21-114">Betrachten Sie eine ASP.NET Core-Web-App, in der die Sicherheit **einzelner Benutzerkonten** aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="6fc21-114">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="6fc21-115">Eine Standarddatenbankverbindungszeichenfolge ist in der Datei *appsettings.json* `DefaultConnection`des Projekts mit dem Schlüssel enthalten.</span><span class="sxs-lookup"><span data-stu-id="6fc21-115">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="6fc21-116">Die Standardverbindungszeichenfolge ist für LocalDB, die im Benutzermodus ausgeführt wird und kein Kennwort erfordert.</span><span class="sxs-lookup"><span data-stu-id="6fc21-116">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="6fc21-117">Während der App-Bereitstellung kann der Schlüsselwert mit dem `DefaultConnection` Wert einer Umgebungsvariablen überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="6fc21-117">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="6fc21-118">Die Umgebungsvariable kann die vollständige Verbindungszeichenfolge mit vertraulichen Anmeldeinformationen speichern.</span><span class="sxs-lookup"><span data-stu-id="6fc21-118">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="6fc21-119">Umgebungsvariablen werden in der Regel in einfachem, unverschlüsseltem Text gespeichert.</span><span class="sxs-lookup"><span data-stu-id="6fc21-119">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="6fc21-120">Wenn der Computer oder Prozess kompromittiert ist, können nicht vertrauenswürdige Parteien auf Umgebungsvariablen zugreifen.</span><span class="sxs-lookup"><span data-stu-id="6fc21-120">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="6fc21-121">Zusätzliche Maßnahmen zur Verhinderung der Offenlegung von Nutzergeheimnissen können erforderlich sein.</span><span class="sxs-lookup"><span data-stu-id="6fc21-121">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="6fc21-122">Secret Manager</span><span class="sxs-lookup"><span data-stu-id="6fc21-122">Secret Manager</span></span>

<span data-ttu-id="6fc21-123">Das Secret Manager-Tool speichert vertrauliche Daten während der Entwicklung eines ASP.NET Core-Projekts.</span><span class="sxs-lookup"><span data-stu-id="6fc21-123">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="6fc21-124">In diesem Zusammenhang ist ein Element sensibler Daten ein Geheimobjekt der App.</span><span class="sxs-lookup"><span data-stu-id="6fc21-124">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="6fc21-125">App-Geheimnisse werden an einem separaten Speicherort als der Projektbaum gespeichert.</span><span class="sxs-lookup"><span data-stu-id="6fc21-125">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="6fc21-126">Die App-Geheimnisse sind einem bestimmten Projekt zugeordnet oder für mehrere Projekte freigegeben.</span><span class="sxs-lookup"><span data-stu-id="6fc21-126">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="6fc21-127">Die App-Geheimnisse werden nicht in die Quellcodeverwaltung eingecheckt.</span><span class="sxs-lookup"><span data-stu-id="6fc21-127">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="6fc21-128">Das Secret Manager-Tool verschlüsselt die gespeicherten Geheimnisse nicht und sollte nicht als vertrauenswürdiger Speicher behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="6fc21-128">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="6fc21-129">Es ist nur für Entwicklungszwecke.</span><span class="sxs-lookup"><span data-stu-id="6fc21-129">It's for development purposes only.</span></span> <span data-ttu-id="6fc21-130">Die Schlüssel und Werte werden in einer JSON-Konfigurationsdatei im Benutzerprofilverzeichnis gespeichert.</span><span class="sxs-lookup"><span data-stu-id="6fc21-130">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="6fc21-131">Funktionsweise des Secret Manager-Tools</span><span class="sxs-lookup"><span data-stu-id="6fc21-131">How the Secret Manager tool works</span></span>

<span data-ttu-id="6fc21-132">Das Tool Secret Manager abstrahiert die Implementierungsdetails, z. B. wo und wie die Werte gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="6fc21-132">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="6fc21-133">Sie können das Tool verwenden, ohne diese Implementierungsdetails zu kennen.</span><span class="sxs-lookup"><span data-stu-id="6fc21-133">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="6fc21-134">Die Werte werden in einer JSON-Konfigurationsdatei in einem systemgeschützten Benutzerprofilordner auf dem lokalen Computer gespeichert:</span><span class="sxs-lookup"><span data-stu-id="6fc21-134">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="6fc21-135">Windows</span><span class="sxs-lookup"><span data-stu-id="6fc21-135">Windows</span></span>](#tab/windows)

<span data-ttu-id="6fc21-136">Dateisystempfad:</span><span class="sxs-lookup"><span data-stu-id="6fc21-136">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="6fc21-137">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="6fc21-137">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="6fc21-138">Dateisystempfad:</span><span class="sxs-lookup"><span data-stu-id="6fc21-138">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="6fc21-139">Ersetzen Sie in den `<user_secrets_id>` vorherigen `UserSecretsId` Dateipfaden durch den in der *.csproj-Datei* angegebenen Wert.</span><span class="sxs-lookup"><span data-stu-id="6fc21-139">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="6fc21-140">Schreiben Sie keinen Code, der vom Speicherort oder Demformat der mit dem Secret Manager-Tool gespeicherten Daten abhängt.</span><span class="sxs-lookup"><span data-stu-id="6fc21-140">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="6fc21-141">Diese Implementierungsdetails können sich ändern.</span><span class="sxs-lookup"><span data-stu-id="6fc21-141">These implementation details may change.</span></span> <span data-ttu-id="6fc21-142">Beispielsweise werden die geheimen Werte nicht verschlüsselt, sondern könnten in der Zukunft vorliegen.</span><span class="sxs-lookup"><span data-stu-id="6fc21-142">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="6fc21-143">Geheime Speicherung aktivieren</span><span class="sxs-lookup"><span data-stu-id="6fc21-143">Enable secret storage</span></span>

<span data-ttu-id="6fc21-144">Das Secret Manager-Tool arbeitet mit projektspezifischen Konfigurationseinstellungen, die in Ihrem Benutzerprofil gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="6fc21-144">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="6fc21-145">Das Secret Manager-Tool enthält einen `init` Befehl in .NET Core SDK 3.0.100 oder höher.</span><span class="sxs-lookup"><span data-stu-id="6fc21-145">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="6fc21-146">Um Benutzergeheimnisse zu verwenden, führen Sie den folgenden Befehl im Projektverzeichnis aus:</span><span class="sxs-lookup"><span data-stu-id="6fc21-146">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="6fc21-147">Der vorherige Befehl `UserSecretsId` fügt `PropertyGroup` ein Element innerhalb einer der *.csproj-Datei* hinzu.</span><span class="sxs-lookup"><span data-stu-id="6fc21-147">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="6fc21-148">Standardmäßig ist der innere `UserSecretsId` Text von eine GUID.</span><span class="sxs-lookup"><span data-stu-id="6fc21-148">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="6fc21-149">Der innere Text ist beliebig, aber für das Projekt eindeutig.</span><span class="sxs-lookup"><span data-stu-id="6fc21-149">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="6fc21-150">Klicken Sie in Visual Studio mit der rechten Maustaste auf das Projekt im Projektmappen-Explorer, und wählen Sie im Kontextmenü **Benutzergeheimnisse verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="6fc21-150">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="6fc21-151">Diese Geste `UserSecretsId` fügt der *.csproj-Datei* ein Element hinzu, das mit einer GUID gefüllt ist.</span><span class="sxs-lookup"><span data-stu-id="6fc21-151">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="6fc21-152">Ein Geheimnis setzen</span><span class="sxs-lookup"><span data-stu-id="6fc21-152">Set a secret</span></span>

<span data-ttu-id="6fc21-153">Definieren Sie einen App-Geheimnis, der aus einem Schlüssel und seinem Wert besteht.</span><span class="sxs-lookup"><span data-stu-id="6fc21-153">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="6fc21-154">Der geheime Schlüssel ist mit `UserSecretsId` dem Wert des Projekts verknüpft.</span><span class="sxs-lookup"><span data-stu-id="6fc21-154">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="6fc21-155">Führen Sie beispielsweise den folgenden Befehl aus dem Verzeichnis aus, in dem die *.csproj-Datei* vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="6fc21-155">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="6fc21-156">Im vorherigen Beispiel bezeichnet der `Movies` Doppelpunkt das Objektliteral mit einer `ServiceApiKey` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="6fc21-156">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="6fc21-157">Das Secret Manager-Tool kann auch von anderen Verzeichnissen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6fc21-157">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="6fc21-158">Verwenden `--project` Sie die Option, um den Dateisystempfad einzugeben, an dem die *.csproj-Datei* vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="6fc21-158">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="6fc21-159">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="6fc21-159">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="6fc21-160">JSON-Strukturabflachung in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6fc21-160">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="6fc21-161">Die Geste **Benutzergeheimnisse verwalten** von Visual Studio öffnet eine *secrets.json-Datei* im Texteditor.</span><span class="sxs-lookup"><span data-stu-id="6fc21-161">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="6fc21-162">Ersetzen Sie den Inhalt von *secrets.json* durch die zu speichernden Schlüssel-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="6fc21-162">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="6fc21-163">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="6fc21-163">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="6fc21-164">Die JSON-Struktur wird nach `dotnet user-secrets remove` Modifikationen über oder `dotnet user-secrets set`abgeflacht.</span><span class="sxs-lookup"><span data-stu-id="6fc21-164">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="6fc21-165">Durch ausführen `dotnet user-secrets remove "Movies:ConnectionString"` wird z. B. das `Movies` Objektliteral reduziert.</span><span class="sxs-lookup"><span data-stu-id="6fc21-165">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="6fc21-166">Die geänderte Datei ähnelt der folgenden:</span><span class="sxs-lookup"><span data-stu-id="6fc21-166">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="6fc21-167">Festlegen mehrerer Geheimnisse</span><span class="sxs-lookup"><span data-stu-id="6fc21-167">Set multiple secrets</span></span>

<span data-ttu-id="6fc21-168">Eine Charge von Geheimnissen kann festgelegt werden, indem JSON an den `set` Befehl gerohrt wird.</span><span class="sxs-lookup"><span data-stu-id="6fc21-168">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="6fc21-169">Im folgenden Beispiel wird der Inhalt der *datei input.json* an den `set` Befehl weitergegeben.</span><span class="sxs-lookup"><span data-stu-id="6fc21-169">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="6fc21-170">Windows</span><span class="sxs-lookup"><span data-stu-id="6fc21-170">Windows</span></span>](#tab/windows)

<span data-ttu-id="6fc21-171">Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="6fc21-171">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="6fc21-172">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="6fc21-172">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="6fc21-173">Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="6fc21-173">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="6fc21-174">Zugriff auf ein Geheimnis</span><span class="sxs-lookup"><span data-stu-id="6fc21-174">Access a secret</span></span>

<span data-ttu-id="6fc21-175">Die [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) bietet Zugriff auf Secret Manager-Geheimnisse.</span><span class="sxs-lookup"><span data-stu-id="6fc21-175">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="6fc21-176">In ASP.NET Core 2.0 oder höher wird die Konfigurationsquelle für Benutzergeheimnisse automatisch im Entwicklungsmodus hinzugefügt, wenn das Projekt aufruft, <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> um eine neue Instanz des Hosts mit vorkonfigurierten Standardeinstellungen zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="6fc21-176">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="6fc21-177">`CreateDefaultBuilder`Anrufe, <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> wenn <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>die ist:</span><span class="sxs-lookup"><span data-stu-id="6fc21-177">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="6fc21-178">Wenn `CreateDefaultBuilder` nicht aufgerufen wird, fügen Sie die Konfigurationsquelle `Startup` für Benutzergeheimnisse explizit hinzu, indem Sie den Konstruktor aufrufen. <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A></span><span class="sxs-lookup"><span data-stu-id="6fc21-178">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="6fc21-179">Rufen `AddUserSecrets` Sie nur auf, wenn die App in der Entwicklungsumgebung ausgeführt wird, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="6fc21-179">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="6fc21-180">Benutzergeheimnisse können über `Configuration` die API abgerufen werden:</span><span class="sxs-lookup"><span data-stu-id="6fc21-180">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]


## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="6fc21-181">Kartengeheimnisse zu einem POCO</span><span class="sxs-lookup"><span data-stu-id="6fc21-181">Map secrets to a POCO</span></span>

<span data-ttu-id="6fc21-182">Das Zuordnen eines gesamten Objektliterals zu einer POCO (eine einfache .NET-Klasse mit Eigenschaften) ist nützlich, um verwandte Eigenschaften zu aggregieren.</span><span class="sxs-lookup"><span data-stu-id="6fc21-182">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="6fc21-183">Um die vorherigen Geheimnisse einem POCO `Configuration` zuzuordnen, verwenden Sie die [Objektdiagrammbindungsfunktion](xref:fundamentals/configuration/index#bind-to-an-object-graph) der API.</span><span class="sxs-lookup"><span data-stu-id="6fc21-183">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="6fc21-184">Der folgende Code ist `MovieSettings` an eine benutzerdefinierte `ServiceApiKey` POCO gebunden und greift auf den Eigenschaftswert zu:</span><span class="sxs-lookup"><span data-stu-id="6fc21-184">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="6fc21-185">Die `Movies:ConnectionString` `Movies:ServiceApiKey` und Geheimnisse werden den jeweiligen `MovieSettings`Eigenschaften zugeordnet in:</span><span class="sxs-lookup"><span data-stu-id="6fc21-185">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="6fc21-186">String-Ersatz mit Geheimnissen</span><span class="sxs-lookup"><span data-stu-id="6fc21-186">String replacement with secrets</span></span>

<span data-ttu-id="6fc21-187">Das Speichern von Kennwörtern im Nur-Text ist unsicher.</span><span class="sxs-lookup"><span data-stu-id="6fc21-187">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="6fc21-188">Beispielsweise kann eine in *appsettings.json* gespeicherte Datenbankverbindungszeichenfolge ein Kennwort für den angegebenen Benutzer enthalten:</span><span class="sxs-lookup"><span data-stu-id="6fc21-188">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="6fc21-189">Ein sichererer Ansatz besteht darin, das Kennwort als geheim zu speichern.</span><span class="sxs-lookup"><span data-stu-id="6fc21-189">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="6fc21-190">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="6fc21-190">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="6fc21-191">Entfernen `Password` Sie das Schlüssel-Wert-Paar aus der Verbindungszeichenfolge in *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="6fc21-191">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="6fc21-192">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="6fc21-192">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="6fc21-193">Der Wert des Geheimen kann <xref:System.Data.SqlClient.SqlConnectionStringBuilder> für <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> die Eigenschaft eines Objekts festgelegt werden, um die Verbindungszeichenfolge abzuschließen:</span><span class="sxs-lookup"><span data-stu-id="6fc21-193">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="6fc21-194">Liste der Geheimnisse</span><span class="sxs-lookup"><span data-stu-id="6fc21-194">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="6fc21-195">Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die *.csproj-Datei* vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="6fc21-195">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="6fc21-196">Die folgende Ausgabe wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="6fc21-196">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="6fc21-197">Im vorherigen Beispiel bezeichnet ein Doppelpunkt in den Schlüsselnamen die Objekthierarchie in *secrets.json*.</span><span class="sxs-lookup"><span data-stu-id="6fc21-197">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="6fc21-198">Entfernen eines einzelnen geheimen Schlüssels</span><span class="sxs-lookup"><span data-stu-id="6fc21-198">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="6fc21-199">Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die *.csproj-Datei* vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="6fc21-199">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="6fc21-200">Die Datei *secrets.json* der App wurde geändert, um das `MoviesConnectionString` Schlüsselwertpaar zu entfernen, das dem Schlüssel zugeordnet ist:</span><span class="sxs-lookup"><span data-stu-id="6fc21-200">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="6fc21-201">`dotnet user-secrets list`zeigt die folgende Meldung an:</span><span class="sxs-lookup"><span data-stu-id="6fc21-201">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="6fc21-202">Entfernen Sie alle Geheimnisse</span><span class="sxs-lookup"><span data-stu-id="6fc21-202">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="6fc21-203">Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die *.csproj-Datei* vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="6fc21-203">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="6fc21-204">Alle Benutzergeheimnisse für die App wurden aus der *Datei secrets.json* gelöscht:</span><span class="sxs-lookup"><span data-stu-id="6fc21-204">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="6fc21-205">Beim `dotnet user-secrets list` Ausführen wird die folgende Meldung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="6fc21-205">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="6fc21-206">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="6fc21-206">Additional resources</span></span>

* <span data-ttu-id="6fc21-207">Informationen zum Zugriff auf Secret Manager von IIS finden Sie in [diesem Problem.](https://github.com/dotnet/AspNetCore.Docs/issues/16328)</span><span class="sxs-lookup"><span data-stu-id="6fc21-207">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6fc21-208">Von [Rick Anderson](https://twitter.com/RickAndMSFT), Daniel [Roth](https://github.com/danroth27)und [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="6fc21-208">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="6fc21-209">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6fc21-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="6fc21-210">In diesem Dokument werden Techniken zum Speichern und Abrufen vertraulicher Daten während der Entwicklung einer ASP.NET Core-App auf einem Entwicklungscomputer erläutert.</span><span class="sxs-lookup"><span data-stu-id="6fc21-210">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="6fc21-211">Speichern Sie niemals Kennwörter oder andere vertrauliche Daten im Quellcode.</span><span class="sxs-lookup"><span data-stu-id="6fc21-211">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="6fc21-212">Produktionsgeheimnisse sollten nicht für die Entwicklung oder den Test verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6fc21-212">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="6fc21-213">Geheimnisse sollten nicht mit der App bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="6fc21-213">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="6fc21-214">Stattdessen sollten Geheimnisse in der Produktionsumgebung über eine kontrollierte Mittelart wie Umgebungsvariablen, Azure Key Vault usw. zur Verfügung gestellt werden. Sie können Azure-Test- und Produktionsgeheimnisse beim [Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration)speichern und schützen.</span><span class="sxs-lookup"><span data-stu-id="6fc21-214">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="6fc21-215">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="6fc21-215">Environment variables</span></span>

<span data-ttu-id="6fc21-216">Umgebungsvariablen werden verwendet, um die Speicherung von App-Geheimnissen im Code oder in lokalen Konfigurationsdateien zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="6fc21-216">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="6fc21-217">Umgebungsvariablen überschreiben Konfigurationswerte für alle zuvor angegebenen Konfigurationsquellen.</span><span class="sxs-lookup"><span data-stu-id="6fc21-217">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="6fc21-218">Betrachten Sie eine ASP.NET Core-Web-App, in der die Sicherheit **einzelner Benutzerkonten** aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="6fc21-218">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="6fc21-219">Eine Standarddatenbankverbindungszeichenfolge ist in der Datei *appsettings.json* `DefaultConnection`des Projekts mit dem Schlüssel enthalten.</span><span class="sxs-lookup"><span data-stu-id="6fc21-219">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="6fc21-220">Die Standardverbindungszeichenfolge ist für LocalDB, die im Benutzermodus ausgeführt wird und kein Kennwort erfordert.</span><span class="sxs-lookup"><span data-stu-id="6fc21-220">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="6fc21-221">Während der App-Bereitstellung kann der Schlüsselwert mit dem `DefaultConnection` Wert einer Umgebungsvariablen überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="6fc21-221">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="6fc21-222">Die Umgebungsvariable kann die vollständige Verbindungszeichenfolge mit vertraulichen Anmeldeinformationen speichern.</span><span class="sxs-lookup"><span data-stu-id="6fc21-222">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="6fc21-223">Umgebungsvariablen werden in der Regel in einfachem, unverschlüsseltem Text gespeichert.</span><span class="sxs-lookup"><span data-stu-id="6fc21-223">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="6fc21-224">Wenn der Computer oder Prozess kompromittiert ist, können nicht vertrauenswürdige Parteien auf Umgebungsvariablen zugreifen.</span><span class="sxs-lookup"><span data-stu-id="6fc21-224">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="6fc21-225">Zusätzliche Maßnahmen zur Verhinderung der Offenlegung von Nutzergeheimnissen können erforderlich sein.</span><span class="sxs-lookup"><span data-stu-id="6fc21-225">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="6fc21-226">Secret Manager</span><span class="sxs-lookup"><span data-stu-id="6fc21-226">Secret Manager</span></span>

<span data-ttu-id="6fc21-227">Das Secret Manager-Tool speichert vertrauliche Daten während der Entwicklung eines ASP.NET Core-Projekts.</span><span class="sxs-lookup"><span data-stu-id="6fc21-227">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="6fc21-228">In diesem Zusammenhang ist ein Element sensibler Daten ein Geheimobjekt der App.</span><span class="sxs-lookup"><span data-stu-id="6fc21-228">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="6fc21-229">App-Geheimnisse werden an einem separaten Speicherort als der Projektbaum gespeichert.</span><span class="sxs-lookup"><span data-stu-id="6fc21-229">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="6fc21-230">Die App-Geheimnisse sind einem bestimmten Projekt zugeordnet oder für mehrere Projekte freigegeben.</span><span class="sxs-lookup"><span data-stu-id="6fc21-230">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="6fc21-231">Die App-Geheimnisse werden nicht in die Quellcodeverwaltung eingecheckt.</span><span class="sxs-lookup"><span data-stu-id="6fc21-231">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="6fc21-232">Das Secret Manager-Tool verschlüsselt die gespeicherten Geheimnisse nicht und sollte nicht als vertrauenswürdiger Speicher behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="6fc21-232">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="6fc21-233">Es ist nur für Entwicklungszwecke.</span><span class="sxs-lookup"><span data-stu-id="6fc21-233">It's for development purposes only.</span></span> <span data-ttu-id="6fc21-234">Die Schlüssel und Werte werden in einer JSON-Konfigurationsdatei im Benutzerprofilverzeichnis gespeichert.</span><span class="sxs-lookup"><span data-stu-id="6fc21-234">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="6fc21-235">Funktionsweise des Secret Manager-Tools</span><span class="sxs-lookup"><span data-stu-id="6fc21-235">How the Secret Manager tool works</span></span>

<span data-ttu-id="6fc21-236">Das Tool Secret Manager abstrahiert die Implementierungsdetails, z. B. wo und wie die Werte gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="6fc21-236">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="6fc21-237">Sie können das Tool verwenden, ohne diese Implementierungsdetails zu kennen.</span><span class="sxs-lookup"><span data-stu-id="6fc21-237">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="6fc21-238">Die Werte werden in einer JSON-Konfigurationsdatei in einem systemgeschützten Benutzerprofilordner auf dem lokalen Computer gespeichert:</span><span class="sxs-lookup"><span data-stu-id="6fc21-238">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="6fc21-239">Windows</span><span class="sxs-lookup"><span data-stu-id="6fc21-239">Windows</span></span>](#tab/windows)

<span data-ttu-id="6fc21-240">Dateisystempfad:</span><span class="sxs-lookup"><span data-stu-id="6fc21-240">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="6fc21-241">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="6fc21-241">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="6fc21-242">Dateisystempfad:</span><span class="sxs-lookup"><span data-stu-id="6fc21-242">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="6fc21-243">Ersetzen Sie in den `<user_secrets_id>` vorherigen `UserSecretsId` Dateipfaden durch den in der *.csproj-Datei* angegebenen Wert.</span><span class="sxs-lookup"><span data-stu-id="6fc21-243">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="6fc21-244">Schreiben Sie keinen Code, der vom Speicherort oder Demformat der mit dem Secret Manager-Tool gespeicherten Daten abhängt.</span><span class="sxs-lookup"><span data-stu-id="6fc21-244">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="6fc21-245">Diese Implementierungsdetails können sich ändern.</span><span class="sxs-lookup"><span data-stu-id="6fc21-245">These implementation details may change.</span></span> <span data-ttu-id="6fc21-246">Beispielsweise werden die geheimen Werte nicht verschlüsselt, sondern könnten in der Zukunft vorliegen.</span><span class="sxs-lookup"><span data-stu-id="6fc21-246">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="6fc21-247">Geheime Speicherung aktivieren</span><span class="sxs-lookup"><span data-stu-id="6fc21-247">Enable secret storage</span></span>

<span data-ttu-id="6fc21-248">Das Secret Manager-Tool arbeitet mit projektspezifischen Konfigurationseinstellungen, die in Ihrem Benutzerprofil gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="6fc21-248">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="6fc21-249">Um Benutzergeheimnisse zu `UserSecretsId` verwenden, `PropertyGroup` definieren Sie ein Element in einer *datei .csproj.*</span><span class="sxs-lookup"><span data-stu-id="6fc21-249">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="6fc21-250">Der innere `UserSecretsId` Text von ist willkürlich, aber einzigartig für das Projekt.</span><span class="sxs-lookup"><span data-stu-id="6fc21-250">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="6fc21-251">Entwickler generieren in der `UserSecretsId`Regel eine GUID für die .</span><span class="sxs-lookup"><span data-stu-id="6fc21-251">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="6fc21-252">Klicken Sie in Visual Studio mit der rechten Maustaste auf das Projekt im Projektmappen-Explorer, und wählen Sie im Kontextmenü **Benutzergeheimnisse verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="6fc21-252">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="6fc21-253">Diese Geste `UserSecretsId` fügt der *.csproj-Datei* ein Element hinzu, das mit einer GUID gefüllt ist.</span><span class="sxs-lookup"><span data-stu-id="6fc21-253">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="6fc21-254">Ein Geheimnis setzen</span><span class="sxs-lookup"><span data-stu-id="6fc21-254">Set a secret</span></span>

<span data-ttu-id="6fc21-255">Definieren Sie einen App-Geheimnis, der aus einem Schlüssel und seinem Wert besteht.</span><span class="sxs-lookup"><span data-stu-id="6fc21-255">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="6fc21-256">Der geheime Schlüssel ist mit `UserSecretsId` dem Wert des Projekts verknüpft.</span><span class="sxs-lookup"><span data-stu-id="6fc21-256">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="6fc21-257">Führen Sie beispielsweise den folgenden Befehl aus dem Verzeichnis aus, in dem die *.csproj-Datei* vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="6fc21-257">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="6fc21-258">Im vorherigen Beispiel bezeichnet der `Movies` Doppelpunkt das Objektliteral mit einer `ServiceApiKey` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="6fc21-258">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="6fc21-259">Das Secret Manager-Tool kann auch von anderen Verzeichnissen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6fc21-259">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="6fc21-260">Verwenden `--project` Sie die Option, um den Dateisystempfad einzugeben, an dem die *.csproj-Datei* vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="6fc21-260">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="6fc21-261">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="6fc21-261">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="6fc21-262">JSON-Strukturabflachung in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6fc21-262">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="6fc21-263">Die Geste **Benutzergeheimnisse verwalten** von Visual Studio öffnet eine *secrets.json-Datei* im Texteditor.</span><span class="sxs-lookup"><span data-stu-id="6fc21-263">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="6fc21-264">Ersetzen Sie den Inhalt von *secrets.json* durch die zu speichernden Schlüssel-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="6fc21-264">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="6fc21-265">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="6fc21-265">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="6fc21-266">Die JSON-Struktur wird nach `dotnet user-secrets remove` Modifikationen über oder `dotnet user-secrets set`abgeflacht.</span><span class="sxs-lookup"><span data-stu-id="6fc21-266">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="6fc21-267">Durch ausführen `dotnet user-secrets remove "Movies:ConnectionString"` wird z. B. das `Movies` Objektliteral reduziert.</span><span class="sxs-lookup"><span data-stu-id="6fc21-267">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="6fc21-268">Die geänderte Datei ähnelt der folgenden:</span><span class="sxs-lookup"><span data-stu-id="6fc21-268">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="6fc21-269">Festlegen mehrerer Geheimnisse</span><span class="sxs-lookup"><span data-stu-id="6fc21-269">Set multiple secrets</span></span>

<span data-ttu-id="6fc21-270">Eine Charge von Geheimnissen kann festgelegt werden, indem JSON an den `set` Befehl gerohrt wird.</span><span class="sxs-lookup"><span data-stu-id="6fc21-270">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="6fc21-271">Im folgenden Beispiel wird der Inhalt der *datei input.json* an den `set` Befehl weitergegeben.</span><span class="sxs-lookup"><span data-stu-id="6fc21-271">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="6fc21-272">Windows</span><span class="sxs-lookup"><span data-stu-id="6fc21-272">Windows</span></span>](#tab/windows)

<span data-ttu-id="6fc21-273">Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="6fc21-273">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="6fc21-274">Linux / macOS</span><span class="sxs-lookup"><span data-stu-id="6fc21-274">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="6fc21-275">Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="6fc21-275">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="6fc21-276">Zugriff auf ein Geheimnis</span><span class="sxs-lookup"><span data-stu-id="6fc21-276">Access a secret</span></span>

<span data-ttu-id="6fc21-277">Die [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) bietet Zugriff auf Secret Manager-Geheimnisse.</span><span class="sxs-lookup"><span data-stu-id="6fc21-277">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="6fc21-278">Wenn Ihr Projekt auf .NET Framework abzielt, installieren Sie das Paket [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet.</span><span class="sxs-lookup"><span data-stu-id="6fc21-278">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>


<span data-ttu-id="6fc21-279">In ASP.NET Core 2.0 oder höher wird die Konfigurationsquelle für Benutzergeheimnisse automatisch im Entwicklungsmodus hinzugefügt, wenn das Projekt aufruft, <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> um eine neue Instanz des Hosts mit vorkonfigurierten Standardeinstellungen zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="6fc21-279">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="6fc21-280">`CreateDefaultBuilder`Anrufe, <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> wenn <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>die ist:</span><span class="sxs-lookup"><span data-stu-id="6fc21-280">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]


<span data-ttu-id="6fc21-281">Wenn `CreateDefaultBuilder` nicht aufgerufen wird, fügen Sie die Konfigurationsquelle `Startup` für Benutzergeheimnisse explizit hinzu, indem Sie den Konstruktor aufrufen. <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A></span><span class="sxs-lookup"><span data-stu-id="6fc21-281">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="6fc21-282">Rufen `AddUserSecrets` Sie nur auf, wenn die App in der Entwicklungsumgebung ausgeführt wird, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="6fc21-282">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="6fc21-283">Benutzergeheimnisse können über `Configuration` die API abgerufen werden:</span><span class="sxs-lookup"><span data-stu-id="6fc21-283">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="6fc21-284">Kartengeheimnisse zu einem POCO</span><span class="sxs-lookup"><span data-stu-id="6fc21-284">Map secrets to a POCO</span></span>

<span data-ttu-id="6fc21-285">Das Zuordnen eines gesamten Objektliterals zu einer POCO (eine einfache .NET-Klasse mit Eigenschaften) ist nützlich, um verwandte Eigenschaften zu aggregieren.</span><span class="sxs-lookup"><span data-stu-id="6fc21-285">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="6fc21-286">Um die vorherigen Geheimnisse einem POCO `Configuration` zuzuordnen, verwenden Sie die [Objektdiagrammbindungsfunktion](xref:fundamentals/configuration/index#bind-to-an-object-graph) der API.</span><span class="sxs-lookup"><span data-stu-id="6fc21-286">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="6fc21-287">Der folgende Code ist `MovieSettings` an eine benutzerdefinierte `ServiceApiKey` POCO gebunden und greift auf den Eigenschaftswert zu:</span><span class="sxs-lookup"><span data-stu-id="6fc21-287">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="6fc21-288">Die `Movies:ConnectionString` `Movies:ServiceApiKey` und Geheimnisse werden den jeweiligen `MovieSettings`Eigenschaften zugeordnet in:</span><span class="sxs-lookup"><span data-stu-id="6fc21-288">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="6fc21-289">String-Ersatz mit Geheimnissen</span><span class="sxs-lookup"><span data-stu-id="6fc21-289">String replacement with secrets</span></span>

<span data-ttu-id="6fc21-290">Das Speichern von Kennwörtern im Nur-Text ist unsicher.</span><span class="sxs-lookup"><span data-stu-id="6fc21-290">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="6fc21-291">Beispielsweise kann eine in *appsettings.json* gespeicherte Datenbankverbindungszeichenfolge ein Kennwort für den angegebenen Benutzer enthalten:</span><span class="sxs-lookup"><span data-stu-id="6fc21-291">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="6fc21-292">Ein sichererer Ansatz besteht darin, das Kennwort als geheim zu speichern.</span><span class="sxs-lookup"><span data-stu-id="6fc21-292">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="6fc21-293">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="6fc21-293">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="6fc21-294">Entfernen `Password` Sie das Schlüssel-Wert-Paar aus der Verbindungszeichenfolge in *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="6fc21-294">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="6fc21-295">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="6fc21-295">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="6fc21-296">Der Wert des Geheimen kann <xref:System.Data.SqlClient.SqlConnectionStringBuilder> für <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> die Eigenschaft eines Objekts festgelegt werden, um die Verbindungszeichenfolge abzuschließen:</span><span class="sxs-lookup"><span data-stu-id="6fc21-296">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="6fc21-297">Liste der Geheimnisse</span><span class="sxs-lookup"><span data-stu-id="6fc21-297">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="6fc21-298">Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die *.csproj-Datei* vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="6fc21-298">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="6fc21-299">Die folgende Ausgabe wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="6fc21-299">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="6fc21-300">Im vorherigen Beispiel bezeichnet ein Doppelpunkt in den Schlüsselnamen die Objekthierarchie in *secrets.json*.</span><span class="sxs-lookup"><span data-stu-id="6fc21-300">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="6fc21-301">Entfernen eines einzelnen geheimen Schlüssels</span><span class="sxs-lookup"><span data-stu-id="6fc21-301">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="6fc21-302">Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die *.csproj-Datei* vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="6fc21-302">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="6fc21-303">Die Datei *secrets.json* der App wurde geändert, um das `MoviesConnectionString` Schlüsselwertpaar zu entfernen, das dem Schlüssel zugeordnet ist:</span><span class="sxs-lookup"><span data-stu-id="6fc21-303">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="6fc21-304">Beim `dotnet user-secrets list` Ausführen wird die folgende Meldung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="6fc21-304">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="6fc21-305">Entfernen Sie alle Geheimnisse</span><span class="sxs-lookup"><span data-stu-id="6fc21-305">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="6fc21-306">Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die *.csproj-Datei* vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="6fc21-306">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="6fc21-307">Alle Benutzergeheimnisse für die App wurden aus der *Datei secrets.json* gelöscht:</span><span class="sxs-lookup"><span data-stu-id="6fc21-307">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="6fc21-308">Beim `dotnet user-secrets list` Ausführen wird die folgende Meldung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="6fc21-308">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="6fc21-309">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="6fc21-309">Additional resources</span></span>

* <span data-ttu-id="6fc21-310">Informationen zum Zugriff auf Secret Manager von IIS finden Sie in [diesem Problem.](https://github.com/dotnet/AspNetCore.Docs/issues/16328)</span><span class="sxs-lookup"><span data-stu-id="6fc21-310">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end