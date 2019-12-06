---
title: Sichere Speicherung von App-Geheimnissen in der Entwicklung in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie vertrauliche Informationen in Form von App-Geheimnissen während der Entwicklung einer ASP.net Core-APP speichern und abrufen.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
uid: security/app-secrets
ms.openlocfilehash: 0bbd6af01ce3a29d3931faa2853a50dc895490cc
ms.sourcegitcommit: fd2483f0a384b1c479c5b4af025ee46917db1919
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74868029"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="0f8e0-103">Sichere Speicherung von App-Geheimnissen in der Entwicklung in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="0f8e0-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

<span data-ttu-id="0f8e0-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)und [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="0f8e0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="0f8e0-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0f8e0-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0f8e0-106">In diesem Dokument werden Techniken zum Speichern und Abrufen von sensiblen Daten während der Entwicklung einer ASP.net Core-App erläutert.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-106">This document explains techniques for storing and retrieving sensitive data during the development of an ASP.NET Core app.</span></span> <span data-ttu-id="0f8e0-107">Speichern Sie niemals Kenn Wörter oder andere sensible Daten im Quellcode.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="0f8e0-108">Produktionsgeheimnisse sollten nicht für Entwicklungs-oder Testzwecke verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="0f8e0-109">Geheimnisse sollten nicht mit der APP bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="0f8e0-110">Stattdessen sollten geheime Schlüssel in der Produktionsumgebung über eine kontrollierte Methode wie Umgebungsvariablen, Azure Key Vault usw. zur Verfügung gestellt werden. Sie können Azure-Test-und Produktionsgeheimnisse mit dem [Azure Key Vault Konfigurations Anbieters](xref:security/key-vault-configuration)speichern und schützen.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-110">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="0f8e0-111">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="0f8e0-111">Environment variables</span></span>

<span data-ttu-id="0f8e0-112">Umgebungsvariablen werden verwendet, um das Speichern von App-Geheimnissen im Code oder in lokalen Konfigurationsdateien zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-112">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="0f8e0-113">Umgebungsvariablen überschreiben Konfigurationswerte für alle zuvor angegebenen Konfigurations Quellen.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-113">Environment variables override configuration values for all previously specified configuration sources.</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="0f8e0-114">Konfigurieren Sie das Lesen von Umgebungsvariablen Werten, indem Sie <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables%2A> im `Startup`-Konstruktor aufrufen:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-114">Configure the reading of environment variable values by calling <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables%2A> in the `Startup` constructor:</span></span>

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=8)]

::: moniker-end

<span data-ttu-id="0f8e0-115">Stellen Sie sich eine ASP.net Core Web-App vor, in der die Sicherheit **einzelner Benutzerkonten** aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-115">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="0f8e0-116">Eine standardmäßige Daten bankverbindungs Zeichenfolge ist in der Datei " *appSettings. JSON* " des Projekts mit dem Schlüssel `DefaultConnection`enthalten.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-116">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="0f8e0-117">Die Standard Verbindungs Zeichenfolge ist für localdb, das im Benutzermodus ausgeführt wird und kein Kennwort erfordert.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-117">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="0f8e0-118">Während der APP-Bereitstellung kann der `DefaultConnection` Schlüsselwert mit dem Wert einer Umgebungsvariablen überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-118">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="0f8e0-119">In der Umgebungsvariablen kann die gesamte Verbindungs Zeichenfolge mit vertraulichen Anmelde Informationen gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-119">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="0f8e0-120">Umgebungsvariablen werden im Allgemeinen in unverschlüsseltem, unverschlüsseltem Text gespeichert.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-120">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="0f8e0-121">Wenn der Computer oder der Prozess kompromittiert ist, kann von nicht vertrauenswürdigen Parteien auf Umgebungsvariablen zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-121">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="0f8e0-122">Möglicherweise sind zusätzliche Maßnahmen erforderlich, um eine Offenlegung von Benutzer Geheimnissen zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-122">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="0f8e0-123">Geheimer Manager</span><span class="sxs-lookup"><span data-stu-id="0f8e0-123">Secret Manager</span></span>

<span data-ttu-id="0f8e0-124">Das Secret Manager-Tool speichert vertrauliche Daten während der Entwicklung eines ASP.net Core Projekts.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-124">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="0f8e0-125">In diesem Zusammenhang handelt es sich bei einem sensiblen Datenobjekt um einen geheimen App-Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-125">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="0f8e0-126">App-Geheimnisse werden an einem anderen Speicherort als in der Projektstruktur gespeichert.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-126">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="0f8e0-127">Die geheimen App-Schlüssel sind einem bestimmten Projekt zugeordnet oder können für mehrere Projekte freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-127">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="0f8e0-128">Die geheimen App-Schlüssel werden nicht in die Quell Code Verwaltung eingecheckt</span><span class="sxs-lookup"><span data-stu-id="0f8e0-128">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="0f8e0-129">Das Secret Manager-Tool verschlüsselt die gespeicherten geheimen Schlüssel nicht und sollte nicht als vertrauenswürdiger Speicher behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-129">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="0f8e0-130">Dies ist nur für Entwicklungszwecke vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-130">It's for development purposes only.</span></span> <span data-ttu-id="0f8e0-131">Die Schlüssel und Werte werden in einer JSON-Konfigurationsdatei im Benutzerprofil Verzeichnis gespeichert.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-131">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="0f8e0-132">So funktioniert das Secret Manager-Tool</span><span class="sxs-lookup"><span data-stu-id="0f8e0-132">How the Secret Manager tool works</span></span>

<span data-ttu-id="0f8e0-133">Das Secret Manager-Tool abstrahiert die Implementierungsdetails, z. b. wo und wie die Werte gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-133">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="0f8e0-134">Sie können das Tool verwenden, ohne diese Implementierungsdetails kennen zu lernen.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-134">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="0f8e0-135">Die Werte werden in einer JSON-Konfigurationsdatei in einem vom System geschützten Benutzerprofil Ordner auf dem lokalen Computer gespeichert:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-135">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="0f8e0-136">Windows</span><span class="sxs-lookup"><span data-stu-id="0f8e0-136">Windows</span></span>](#tab/windows)

<span data-ttu-id="0f8e0-137">Dateisystempfad:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-137">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macostablinuxmacos"></a>[<span data-ttu-id="0f8e0-138">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="0f8e0-138">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="0f8e0-139">Dateisystempfad:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-139">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="0f8e0-140">Ersetzen Sie in den vorangehenden Dateipfaden `<user_secrets_id>` durch den `UserSecretsId` Wert, der in der *csproj* -Datei angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-140">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="0f8e0-141">Schreiben Sie keinen Code, der vom Speicherort oder Format der mit dem Geheimnis-Manager-Tool gespeicherten Daten abhängt.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-141">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="0f8e0-142">Diese Implementierungsdetails können sich ändern.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-142">These implementation details may change.</span></span> <span data-ttu-id="0f8e0-143">Die geheimen Werte sind z. b. nicht verschlüsselt, können aber in der Zukunft liegen.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-143">For example, the secret values aren't encrypted, but could be in the future.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="install-the-secret-manager-tool"></a><span data-ttu-id="0f8e0-144">Installieren des Secret Manager-Tools</span><span class="sxs-lookup"><span data-stu-id="0f8e0-144">Install the Secret Manager tool</span></span>

<span data-ttu-id="0f8e0-145">Das Secret Manager-Tool ist mit dem .net Core-CLI in .net Core SDK 2.1.300 oder höher gebündelt.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-145">The Secret Manager tool is bundled with the .NET Core CLI in .NET Core SDK 2.1.300 or later.</span></span> <span data-ttu-id="0f8e0-146">Für .net Core SDK Versionen vor 2.1.300 ist eine Tool Installation erforderlich.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-146">For .NET Core SDK versions before 2.1.300, tool installation is necessary.</span></span>

> [!TIP]
> <span data-ttu-id="0f8e0-147">Führen Sie `dotnet --version` über eine Befehlsshell aus, um die installierte .net Core SDK Versionsnummer anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-147">Run `dotnet --version` from a command shell to see the installed .NET Core SDK version number.</span></span>

<span data-ttu-id="0f8e0-148">Eine Warnung wird angezeigt, wenn die .net Core SDK, die verwendet wird, das Tool umfasst:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-148">A warning is displayed if the .NET Core SDK being used includes the tool:</span></span>

```console
The tool 'Microsoft.Extensions.SecretManager.Tools' is now included in the .NET Core SDK. Information on resolving this warning is available at (https://aka.ms/dotnetclitools-in-box).
```

<span data-ttu-id="0f8e0-149">Installieren Sie das nuget-Paket [Microsoft. Extensions. secretmanager. Tools](https://www.nuget.org/packages/Microsoft.Extensions.SecretManager.Tools/) in Ihrem ASP.net Core Projekt.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-149">Install the [Microsoft.Extensions.SecretManager.Tools](https://www.nuget.org/packages/Microsoft.Extensions.SecretManager.Tools/) NuGet package in your ASP.NET Core project.</span></span> <span data-ttu-id="0f8e0-150">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-150">For example:</span></span>

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_CsprojFile&highlight=15-16)]

<span data-ttu-id="0f8e0-151">Führen Sie den folgenden Befehl in einer Befehlsshell aus, um die Tool Installation zu überprüfen:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-151">Execute the following command in a command shell to validate the tool installation:</span></span>

```dotnetcli
dotnet user-secrets -h
```

<span data-ttu-id="0f8e0-152">Das Tool Secret Manager zeigt Beispiel Verwendung, Optionen und Hilfe für Befehle an:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-152">The Secret Manager tool displays sample usage, options, and command help:</span></span>

```console
Usage: dotnet user-secrets [options] [command]

Options:
  -?|-h|--help                        Show help information
  --version                           Show version information
  -v|--verbose                        Show verbose output
  -p|--project <PROJECT>              Path to project. Defaults to searching the current directory.
  -c|--configuration <CONFIGURATION>  The project configuration to use. Defaults to 'Debug'.
  --id                                The user secret ID to use.

Commands:
  clear   Deletes all the application secrets
  list    Lists all the application secrets
  remove  Removes the specified user secret
  set     Sets the user secret to the specified value

Use "dotnet user-secrets [command] --help" for more information about a command.
```

> [!NOTE]
> <span data-ttu-id="0f8e0-153">Sie müssen sich im gleichen Verzeichnis wie die *csproj* -Datei befinden, um Tools auszuführen, die in den `DotNetCliToolReference` Elementen der *csproj* -Datei definiert sind.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-153">You must be in the same directory as the *.csproj* file to run tools defined in the *.csproj* file's `DotNetCliToolReference` elements.</span></span>

::: moniker-end

## <a name="enable-secret-storage"></a><span data-ttu-id="0f8e0-154">Aktivieren der geheimen Speicherung</span><span class="sxs-lookup"><span data-stu-id="0f8e0-154">Enable secret storage</span></span>

<span data-ttu-id="0f8e0-155">Das Secret Manager-Tool arbeitet mit projektspezifischen Konfigurationseinstellungen, die in Ihrem Benutzerprofil gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-155">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0f8e0-156">Das Secret Manager-Tool enthält einen `init` Befehl in .net Core SDK 3.0.100 oder höher.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-156">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="0f8e0-157">Zum Verwenden von Benutzer Geheimnissen führen Sie den folgenden Befehl im Projektverzeichnis aus:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-157">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="0f8e0-158">Der vorherige Befehl fügt ein `UserSecretsId`-Element innerhalb einer `PropertyGroup` der *csproj* -Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-158">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="0f8e0-159">Standardmäßig ist der innere Text `UserSecretsId` eine GUID.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-159">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="0f8e0-160">Der innere Text ist willkürlich, aber für das Projekt eindeutig.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-160">The inner text is arbitrary, but is unique to the project.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="0f8e0-161">Definieren Sie ein `UserSecretsId`-Element innerhalb einer `PropertyGroup` der *csproj* -Datei, um Benutzer Geheimnisse zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-161">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="0f8e0-162">Der innere Text `UserSecretsId` ist willkürlich, aber für das Projekt eindeutig.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-162">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="0f8e0-163">Entwickler generieren in der Regel eine GUID für die `UserSecretsId`.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-163">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

> [!TIP]
> <span data-ttu-id="0f8e0-164">Klicken Sie in Visual Studio mit der rechten Maustaste auf das Projekt in Projektmappen-Explorer, und wählen Sie im Kontextmenü die Option **Benutzer Geheimnisse verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-164">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="0f8e0-165">Diese Geste fügt der *csproj* -Datei ein `UserSecretsId` Element, das mit einer GUID aufgefüllt ist, hinzu.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-165">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="0f8e0-166">Festlegen eines Geheimnisses</span><span class="sxs-lookup"><span data-stu-id="0f8e0-166">Set a secret</span></span>

<span data-ttu-id="0f8e0-167">Definieren Sie einen geheimen App-Schlüssel, der aus einem Schlüssel und seinem Wert besteht.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-167">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="0f8e0-168">Der geheime Schlüssel wird dem `UserSecretsId` Wert des Projekts zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-168">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="0f8e0-169">Führen Sie z. b. den folgenden Befehl aus dem Verzeichnis aus, in dem die *csproj* -Datei vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-169">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="0f8e0-170">Im vorherigen Beispiel deutet der Doppelpunkt darauf hin, dass `Movies` ein Objektliteral mit einer `ServiceApiKey`-Eigenschaft ist.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-170">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="0f8e0-171">Das Secret Manager-Tool kann auch aus anderen Verzeichnissen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-171">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="0f8e0-172">Verwenden Sie die Option `--project`, um den Dateisystempfad anzugeben, in dem die *csproj* -Datei vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-172">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="0f8e0-173">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-173">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="0f8e0-174">Vereinfachen der JSON-Struktur in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0f8e0-174">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="0f8e0-175">Die Geste " **Benutzer Geheimnisse verwalten** " von Visual Studio öffnet die Datei " *Secrets. JSON* " im Text-Editor.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-175">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="0f8e0-176">Ersetzen Sie den Inhalt von " *Secrets. JSON* " durch die Schlüssel-Wert-Paare, die gespeichert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-176">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="0f8e0-177">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-177">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="0f8e0-178">Die JSON-Struktur wird nach Änderungen über `dotnet user-secrets remove` oder `dotnet user-secrets set`vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-178">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="0f8e0-179">Beispielsweise wird durch das Ausführen von `dotnet user-secrets remove "Movies:ConnectionString"` das `Movies` Objektliterals reduziert.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-179">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="0f8e0-180">Die geänderte Datei ähnelt der folgenden:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-180">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="0f8e0-181">Festlegen mehrerer geheimer Schlüssel</span><span class="sxs-lookup"><span data-stu-id="0f8e0-181">Set multiple secrets</span></span>

<span data-ttu-id="0f8e0-182">Ein Batch geheimer Schlüssel kann durch die Weiterleitung von JSON an den `set`-Befehl festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-182">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="0f8e0-183">Im folgenden Beispiel wird der Inhalt der Datei " *Input. JSON* " an den `set`-Befehl weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-183">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="0f8e0-184">Windows</span><span class="sxs-lookup"><span data-stu-id="0f8e0-184">Windows</span></span>](#tab/windows)

<span data-ttu-id="0f8e0-185">Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-185">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macostablinuxmacos"></a>[<span data-ttu-id="0f8e0-186">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="0f8e0-186">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="0f8e0-187">Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-187">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="0f8e0-188">Auf einen geheimen Schlüssel zugreifen</span><span class="sxs-lookup"><span data-stu-id="0f8e0-188">Access a secret</span></span>

<span data-ttu-id="0f8e0-189">Die [ASP.net Core-Konfigurations-API](xref:fundamentals/configuration/index) ermöglicht den Zugriff auf Geheimnisse geheimer Geheimnisse.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-189">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

<span data-ttu-id="0f8e0-190">Wenn das Projekt .NET Framework als Ziel hat, installieren Sie das nuget-Paket [Microsoft. Extensions. Configuration. usersecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) .</span><span class="sxs-lookup"><span data-stu-id="0f8e0-190">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="0f8e0-191">In ASP.net Core 2,0 oder höher wird die Konfigurations Quelle "Benutzer Geheimnisse" automatisch im Entwicklungsmodus hinzugefügt, wenn das Projekt <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> aufruft, um eine neue Instanz des Hosts mit vorkonfigurierten Standardwerten zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-191">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="0f8e0-192">`CreateDefaultBuilder` <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> aufruft, wenn der <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>ist:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-192">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="0f8e0-193">Wenn `CreateDefaultBuilder` nicht aufgerufen wird, fügen Sie die Konfigurations Quelle für Benutzer Geheimnisse explizit hinzu, indem Sie <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> im `Startup`-Konstruktor aufrufen.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-193">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="0f8e0-194">Wenn die app in der Entwicklungsumgebung ausgeführt wird, wenden Sie `AddUserSecrets` an, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-194">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="0f8e0-195">Installieren Sie das nuget-Paket [Microsoft. Extensions. Configuration. usersecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) .</span><span class="sxs-lookup"><span data-stu-id="0f8e0-195">Install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="0f8e0-196">Fügen Sie die Konfigurations Quelle für Benutzer Geheimnisse mit einem <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> im `Startup`-Konstruktor aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-196">Add the user secrets configuration source with a call to <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor:</span></span>

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

<span data-ttu-id="0f8e0-197">Benutzer Geheimnisse können über die `Configuration`-API abgerufen werden:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-197">User secrets can be retrieved via the `Configuration` API:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=26)]

::: moniker-end

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="0f8e0-198">Zuordnen von Geheimnissen zu einem poco</span><span class="sxs-lookup"><span data-stu-id="0f8e0-198">Map secrets to a POCO</span></span>

<span data-ttu-id="0f8e0-199">Das Mapping eines gesamten Objektliterals zu einem poco (eine einfache .NET-Klasse mit-Eigenschaften) ist nützlich für das aggregierten von verknüpften Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-199">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="0f8e0-200">Um die vorangehenden geheimen Schlüssel einem poco zuzuordnen, verwenden Sie das [Objekt Graph-Bindungs](xref:fundamentals/configuration/index#bind-to-an-object-graph) Feature der `Configuration`-API.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-200">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="0f8e0-201">Der folgende Code bindet an eine benutzerdefinierte `MovieSettings` poco und greift auf den `ServiceApiKey`-Eigenschafts Wert zu:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-201">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

::: moniker range=">= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

::: moniker range="= aspnetcore-1.0"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

<span data-ttu-id="0f8e0-202">Die `Movies:ConnectionString`-und `Movies:ServiceApiKey` geheimen Schlüssel werden den entsprechenden Eigenschaften in `MovieSettings`zugeordnet:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-202">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="0f8e0-203">Zeichen folgen Ersetzung mit geheimen Schlüsseln</span><span class="sxs-lookup"><span data-stu-id="0f8e0-203">String replacement with secrets</span></span>

<span data-ttu-id="0f8e0-204">Das Speichern von Kenn Wörtern im Klartext ist unsicher.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-204">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="0f8e0-205">Eine in " *appSettings. JSON* " gespeicherte Daten bankverbindungs Zeichenfolge kann z. b. ein Kennwort für den angegebenen Benutzer enthalten:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-205">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="0f8e0-206">Ein sichereren Ansatz besteht darin, das Kennwort als geheimen Schlüssel zu speichern.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-206">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="0f8e0-207">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-207">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="0f8e0-208">Entfernen Sie das `Password` Schlüssel/Wert-Paar aus der Verbindungs Zeichenfolge in *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-208">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="0f8e0-209">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-209">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="0f8e0-210">Der Wert des Geheimnisses kann für die <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A>-Eigenschaft eines <xref:System.Data.SqlClient.SqlConnectionStringBuilder> Objekts festgelegt werden, um die Verbindungs Zeichenfolge abzuschließen:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-210">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=26-29)]

::: moniker-end

## <a name="list-the-secrets"></a><span data-ttu-id="0f8e0-211">Auflisten der geheimen Schlüssel</span><span class="sxs-lookup"><span data-stu-id="0f8e0-211">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="0f8e0-212">Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die *csproj* -Datei vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-212">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="0f8e0-213">Die folgende Ausgabe wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-213">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="0f8e0-214">Im vorherigen Beispiel gibt ein Doppelpunkt in den Schlüsselnamen die Objekthierarchie in " *Secrets. JSON*" an.</span><span class="sxs-lookup"><span data-stu-id="0f8e0-214">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="0f8e0-215">Entfernen eines einzelnen geheimen Schlüssels</span><span class="sxs-lookup"><span data-stu-id="0f8e0-215">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="0f8e0-216">Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die *csproj* -Datei vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-216">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="0f8e0-217">Die Datei " *Secrets. JSON* " der App wurde geändert, um das Schlüssel-Wert-Paar zu entfernen, das mit dem `MoviesConnectionString` Schlüssel verknüpft ist:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-217">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="0f8e0-218">Beim Ausführen `dotnet user-secrets list` wird die folgende Meldung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-218">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="0f8e0-219">Alle geheimen Schlüssel entfernen</span><span class="sxs-lookup"><span data-stu-id="0f8e0-219">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="0f8e0-220">Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die *csproj* -Datei vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-220">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="0f8e0-221">Alle geheimen Benutzerschlüssel für die APP wurden aus der Datei " *Secrets. JSON* " gelöscht:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-221">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="0f8e0-222">Beim Ausführen `dotnet user-secrets list` wird die folgende Meldung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="0f8e0-222">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="0f8e0-223">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="0f8e0-223">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>
