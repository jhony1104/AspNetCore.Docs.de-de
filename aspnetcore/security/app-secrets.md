---
title: Sichere Speicherung von App-Geheimnissen in der Entwicklung in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie vertrauliche Informationen in Form von App-Geheimnissen während der Entwicklung einer ASP.net Core-APP speichern und abrufen.
ms.author: scaddie
ms.custom: mvc
ms.date: 03/13/2019
uid: security/app-secrets
ms.openlocfilehash: 0203a5737caf1af809b739d9e266a6971cd1523b
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080719"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="848c0-103">Sichere Speicherung von App-Geheimnissen in der Entwicklung in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="848c0-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

<span data-ttu-id="848c0-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)und [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="848c0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="848c0-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="848c0-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="848c0-106">In diesem Dokument werden Techniken zum Speichern und Abrufen von sensiblen Daten während der Entwicklung einer ASP.net Core-App erläutert.</span><span class="sxs-lookup"><span data-stu-id="848c0-106">This document explains techniques for storing and retrieving sensitive data during the development of an ASP.NET Core app.</span></span> <span data-ttu-id="848c0-107">Speichern Sie niemals Kenn Wörter oder andere sensible Daten im Quellcode.</span><span class="sxs-lookup"><span data-stu-id="848c0-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="848c0-108">Produktionsgeheimnisse sollten nicht für Entwicklungs-oder Testzwecke verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="848c0-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="848c0-109">Sie können Azure-Test- und -Produktionsgeheimnisse mit dem [Konfigurationsanbieter Azure Key Vault](xref:security/key-vault-configuration) speichern und schützen.</span><span class="sxs-lookup"><span data-stu-id="848c0-109">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="848c0-110">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="848c0-110">Environment variables</span></span>

<span data-ttu-id="848c0-111">Umgebungsvariablen werden verwendet, um das Speichern von App-Geheimnissen im Code oder in lokalen Konfigurationsdateien zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="848c0-111">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="848c0-112">Umgebungsvariablen überschreiben Konfigurationswerte für alle zuvor angegebenen Konfigurations Quellen.</span><span class="sxs-lookup"><span data-stu-id="848c0-112">Environment variables override configuration values for all previously specified configuration sources.</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="848c0-113">Konfigurieren Sie das Lesen von Umgebungsvariablen Werten, <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> indem Sie `Startup` im-Konstruktor aufrufen:</span><span class="sxs-lookup"><span data-stu-id="848c0-113">Configure the reading of environment variable values by calling <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> in the `Startup` constructor:</span></span>

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=8)]

::: moniker-end

<span data-ttu-id="848c0-114">Stellen Sie sich eine ASP.net Core Web-App vor, in der die Sicherheit **einzelner Benutzerkonten** aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="848c0-114">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="848c0-115">Eine standardmäßige Daten bankverbindungs Zeichenfolge ist in der Datei " *appSettings. JSON* " `DefaultConnection`des Projekts mit dem Schlüssel enthalten.</span><span class="sxs-lookup"><span data-stu-id="848c0-115">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="848c0-116">Die Standard Verbindungs Zeichenfolge ist für localdb, das im Benutzermodus ausgeführt wird und kein Kennwort erfordert.</span><span class="sxs-lookup"><span data-stu-id="848c0-116">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="848c0-117">Während der APP-bereit `DefaultConnection` Stellung kann der Schlüsselwert mit dem Wert einer Umgebungsvariablen überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="848c0-117">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="848c0-118">In der Umgebungsvariablen kann die gesamte Verbindungs Zeichenfolge mit vertraulichen Anmelde Informationen gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="848c0-118">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="848c0-119">Umgebungsvariablen werden im Allgemeinen in unverschlüsseltem, unverschlüsseltem Text gespeichert.</span><span class="sxs-lookup"><span data-stu-id="848c0-119">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="848c0-120">Wenn der Computer oder der Prozess kompromittiert ist, kann von nicht vertrauenswürdigen Parteien auf Umgebungsvariablen zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="848c0-120">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="848c0-121">Möglicherweise sind zusätzliche Maßnahmen erforderlich, um eine Offenlegung von Benutzer Geheimnissen zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="848c0-121">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="848c0-122">Geheimer Manager</span><span class="sxs-lookup"><span data-stu-id="848c0-122">Secret Manager</span></span>

<span data-ttu-id="848c0-123">Das Secret Manager-Tool speichert vertrauliche Daten während der Entwicklung eines ASP.net Core Projekts.</span><span class="sxs-lookup"><span data-stu-id="848c0-123">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="848c0-124">In diesem Zusammenhang handelt es sich bei einem sensiblen Datenobjekt um einen geheimen App-Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="848c0-124">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="848c0-125">App-Geheimnisse werden an einem anderen Speicherort als in der Projektstruktur gespeichert.</span><span class="sxs-lookup"><span data-stu-id="848c0-125">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="848c0-126">Die geheimen App-Schlüssel sind einem bestimmten Projekt zugeordnet oder können für mehrere Projekte freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="848c0-126">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="848c0-127">Die geheimen App-Schlüssel werden nicht in die Quell Code Verwaltung eingecheckt</span><span class="sxs-lookup"><span data-stu-id="848c0-127">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="848c0-128">Das Secret Manager-Tool verschlüsselt die gespeicherten geheimen Schlüssel nicht und sollte nicht als vertrauenswürdiger Speicher behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="848c0-128">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="848c0-129">Dies ist nur für Entwicklungszwecke vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="848c0-129">It's for development purposes only.</span></span> <span data-ttu-id="848c0-130">Die Schlüssel und Werte werden in einer JSON-Konfigurationsdatei im Benutzerprofil Verzeichnis gespeichert.</span><span class="sxs-lookup"><span data-stu-id="848c0-130">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="848c0-131">So funktioniert das Secret Manager-Tool</span><span class="sxs-lookup"><span data-stu-id="848c0-131">How the Secret Manager tool works</span></span>

<span data-ttu-id="848c0-132">Das Secret Manager-Tool abstrahiert die Implementierungsdetails, z. b. wo und wie die Werte gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="848c0-132">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="848c0-133">Sie können das Tool verwenden, ohne diese Implementierungsdetails kennen zu lernen.</span><span class="sxs-lookup"><span data-stu-id="848c0-133">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="848c0-134">Die Werte werden in einer JSON-Konfigurationsdatei in einem vom System geschützten Benutzerprofil Ordner auf dem lokalen Computer gespeichert:</span><span class="sxs-lookup"><span data-stu-id="848c0-134">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="848c0-135">Windows</span><span class="sxs-lookup"><span data-stu-id="848c0-135">Windows</span></span>](#tab/windows)

<span data-ttu-id="848c0-136">Dateisystempfad:</span><span class="sxs-lookup"><span data-stu-id="848c0-136">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macostablinuxmacos"></a>[<span data-ttu-id="848c0-137">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="848c0-137">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="848c0-138">Dateisystempfad:</span><span class="sxs-lookup"><span data-stu-id="848c0-138">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="848c0-139">Ersetzen `<user_secrets_id>` Sie in den vorangehenden Dateipfaden durch `UserSecretsId` den Wert, der in der *csproj* -Datei angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="848c0-139">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="848c0-140">Schreiben Sie keinen Code, der vom Speicherort oder Format der mit dem Geheimnis-Manager-Tool gespeicherten Daten abhängt.</span><span class="sxs-lookup"><span data-stu-id="848c0-140">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="848c0-141">Diese Implementierungsdetails können sich ändern.</span><span class="sxs-lookup"><span data-stu-id="848c0-141">These implementation details may change.</span></span> <span data-ttu-id="848c0-142">Die geheimen Werte sind z. b. nicht verschlüsselt, können aber in der Zukunft liegen.</span><span class="sxs-lookup"><span data-stu-id="848c0-142">For example, the secret values aren't encrypted, but could be in the future.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="install-the-secret-manager-tool"></a><span data-ttu-id="848c0-143">Installieren des Secret Manager-Tools</span><span class="sxs-lookup"><span data-stu-id="848c0-143">Install the Secret Manager tool</span></span>

<span data-ttu-id="848c0-144">Das Secret Manager-Tool ist mit dem .net Core-CLI in .net Core SDK 2.1.300 oder höher gebündelt.</span><span class="sxs-lookup"><span data-stu-id="848c0-144">The Secret Manager tool is bundled with the .NET Core CLI in .NET Core SDK 2.1.300 or later.</span></span> <span data-ttu-id="848c0-145">Für .net Core SDK Versionen vor 2.1.300 ist eine Tool Installation erforderlich.</span><span class="sxs-lookup"><span data-stu-id="848c0-145">For .NET Core SDK versions before 2.1.300, tool installation is necessary.</span></span>

> [!TIP]
> <span data-ttu-id="848c0-146">Führen `dotnet --version` Sie über eine Befehlsshell aus, um die installierte .net Core SDK Versionsnummer anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="848c0-146">Run `dotnet --version` from a command shell to see the installed .NET Core SDK version number.</span></span>

<span data-ttu-id="848c0-147">Eine Warnung wird angezeigt, wenn die .net Core SDK, die verwendet wird, das Tool umfasst:</span><span class="sxs-lookup"><span data-stu-id="848c0-147">A warning is displayed if the .NET Core SDK being used includes the tool:</span></span>

```console
The tool 'Microsoft.Extensions.SecretManager.Tools' is now included in the .NET Core SDK. Information on resolving this warning is available at (https://aka.ms/dotnetclitools-in-box).
```

<span data-ttu-id="848c0-148">Installieren Sie das nuget-Paket [Microsoft. Extensions. secretmanager. Tools](https://www.nuget.org/packages/Microsoft.Extensions.SecretManager.Tools/) in Ihrem ASP.net Core Projekt.</span><span class="sxs-lookup"><span data-stu-id="848c0-148">Install the [Microsoft.Extensions.SecretManager.Tools](https://www.nuget.org/packages/Microsoft.Extensions.SecretManager.Tools/) NuGet package in your ASP.NET Core project.</span></span> <span data-ttu-id="848c0-149">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="848c0-149">For example:</span></span>

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_CsprojFile&highlight=15-16)]

<span data-ttu-id="848c0-150">Führen Sie den folgenden Befehl in einer Befehlsshell aus, um die Tool Installation zu überprüfen:</span><span class="sxs-lookup"><span data-stu-id="848c0-150">Execute the following command in a command shell to validate the tool installation:</span></span>

```dotnetcli
dotnet user-secrets -h
```

<span data-ttu-id="848c0-151">Das Tool Secret Manager zeigt Beispiel Verwendung, Optionen und Hilfe für Befehle an:</span><span class="sxs-lookup"><span data-stu-id="848c0-151">The Secret Manager tool displays sample usage, options, and command help:</span></span>

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
> <span data-ttu-id="848c0-152">Sie müssen sich im gleichen Verzeichnis wie die *csproj* -Datei befinden, um die in den Elementen der *csproj* - `DotNetCliToolReference` Datei definierten Tools auszuführen.</span><span class="sxs-lookup"><span data-stu-id="848c0-152">You must be in the same directory as the *.csproj* file to run tools defined in the *.csproj* file's `DotNetCliToolReference` elements.</span></span>

::: moniker-end

## <a name="enable-secret-storage"></a><span data-ttu-id="848c0-153">Aktivieren der geheimen Speicherung</span><span class="sxs-lookup"><span data-stu-id="848c0-153">Enable secret storage</span></span>

<span data-ttu-id="848c0-154">Das Secret Manager-Tool arbeitet mit projektspezifischen Konfigurationseinstellungen, die in Ihrem Benutzerprofil gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="848c0-154">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="848c0-155">Das Secret Manager-Tool enthält `init` einen Befehl in .net Core SDK 3.0.100 oder höher.</span><span class="sxs-lookup"><span data-stu-id="848c0-155">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="848c0-156">Zum Verwenden von Benutzer Geheimnissen führen Sie den folgenden Befehl im Projektverzeichnis aus:</span><span class="sxs-lookup"><span data-stu-id="848c0-156">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="848c0-157">Mit dem vorangehenden Befehl `UserSecretsId` wird ein- `PropertyGroup` Element in einer der *csproj* -Datei hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="848c0-157">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="848c0-158">Standardmäßig ist der innere Text von `UserSecretsId` eine GUID.</span><span class="sxs-lookup"><span data-stu-id="848c0-158">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="848c0-159">Der innere Text ist willkürlich, aber für das Projekt eindeutig.</span><span class="sxs-lookup"><span data-stu-id="848c0-159">The inner text is arbitrary, but is unique to the project.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="848c0-160">Definieren Sie ein `UserSecretsId` -Element in einer `PropertyGroup` -Datei der *csproj* -Datei, um Benutzer Geheimnisse zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="848c0-160">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="848c0-161">Der innere Text von `UserSecretsId` ist willkürlich, aber für das Projekt eindeutig.</span><span class="sxs-lookup"><span data-stu-id="848c0-161">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="848c0-162">Entwickler generieren in der Regel eine GUID `UserSecretsId`für das.</span><span class="sxs-lookup"><span data-stu-id="848c0-162">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](app-secrets/samples/1.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

::: moniker-end

> [!TIP]
> <span data-ttu-id="848c0-163">Klicken Sie in Visual Studio mit der rechten Maustaste auf das Projekt in Projektmappen-Explorer, und wählen Sie im Kontextmenü die Option **Benutzer Geheimnisse verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="848c0-163">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="848c0-164">Diese Geste fügt der `UserSecretsId` *csproj* -Datei ein-Element, das mit einer GUID aufgefüllt ist, hinzu.</span><span class="sxs-lookup"><span data-stu-id="848c0-164">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="848c0-165">Festlegen eines Geheimnisses</span><span class="sxs-lookup"><span data-stu-id="848c0-165">Set a secret</span></span>

<span data-ttu-id="848c0-166">Definieren Sie einen geheimen App-Schlüssel, der aus einem Schlüssel und seinem Wert besteht.</span><span class="sxs-lookup"><span data-stu-id="848c0-166">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="848c0-167">Das Geheimnis wird dem `UserSecretsId` Wert des Projekts zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="848c0-167">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="848c0-168">Führen Sie z. b. den folgenden Befehl aus dem Verzeichnis aus, in dem die *csproj* -Datei vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="848c0-168">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="848c0-169">Im vorherigen Beispiel deutet der Doppelpunkt darauf hin, `Movies` dass ein Objektliteral `ServiceApiKey` mit einer-Eigenschaft ist.</span><span class="sxs-lookup"><span data-stu-id="848c0-169">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="848c0-170">Das Secret Manager-Tool kann auch aus anderen Verzeichnissen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="848c0-170">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="848c0-171">Verwenden Sie `--project` die-Option, um den Dateisystempfad anzugeben, in dem die *csproj* -Datei vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="848c0-171">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="848c0-172">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="848c0-172">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="848c0-173">Vereinfachen der JSON-Struktur in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="848c0-173">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="848c0-174">Die Geste " **Benutzer Geheimnisse verwalten** " von Visual Studio öffnet die Datei " *Secrets. JSON* " im Text-Editor.</span><span class="sxs-lookup"><span data-stu-id="848c0-174">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="848c0-175">Ersetzen Sie den Inhalt von " *Secrets. JSON* " durch die Schlüssel-Wert-Paare, die gespeichert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="848c0-175">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="848c0-176">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="848c0-176">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="848c0-177">Die JSON-Struktur wird nach Änderungen über `dotnet user-secrets remove` oder `dotnet user-secrets set`vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="848c0-177">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="848c0-178">Beispielsweise wird durch `dotnet user-secrets remove "Movies:ConnectionString"` das Ausführen `Movies` des Objektliterals reduziert.</span><span class="sxs-lookup"><span data-stu-id="848c0-178">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="848c0-179">Die geänderte Datei ähnelt der folgenden:</span><span class="sxs-lookup"><span data-stu-id="848c0-179">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="848c0-180">Festlegen mehrerer geheimer Schlüssel</span><span class="sxs-lookup"><span data-stu-id="848c0-180">Set multiple secrets</span></span>

<span data-ttu-id="848c0-181">Ein Batch geheimer Schlüssel kann durch die Weiterleitung von JSON an `set` den Befehl festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="848c0-181">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="848c0-182">Im folgenden Beispiel wird der Inhalt der Datei " *Input. JSON* " an den `set` Befehl weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="848c0-182">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windowstabwindows"></a>[<span data-ttu-id="848c0-183">Windows</span><span class="sxs-lookup"><span data-stu-id="848c0-183">Windows</span></span>](#tab/windows)

<span data-ttu-id="848c0-184">Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="848c0-184">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macostablinuxmacos"></a>[<span data-ttu-id="848c0-185">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="848c0-185">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="848c0-186">Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="848c0-186">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="848c0-187">Auf einen geheimen Schlüssel zugreifen</span><span class="sxs-lookup"><span data-stu-id="848c0-187">Access a secret</span></span>

<span data-ttu-id="848c0-188">Die [ASP.net Core-Konfigurations-API](xref:fundamentals/configuration/index) ermöglicht den Zugriff auf Geheimnisse geheimer Geheimnisse.</span><span class="sxs-lookup"><span data-stu-id="848c0-188">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

::: moniker range=">= aspnetcore-2.0 <= aspnetcore-2.2"

<span data-ttu-id="848c0-189">Wenn das Projekt .NET Framework als Ziel hat, installieren Sie das nuget-Paket [Microsoft. Extensions. Configuration. usersecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) .</span><span class="sxs-lookup"><span data-stu-id="848c0-189">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="848c0-190">In ASP.net Core 2,0 oder höher wird die Konfigurations Quelle "Benutzer Geheimnisse" automatisch im Entwicklungsmodus hinzugefügt, wenn <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> das Projekt aufruft, um eine neue Instanz des Hosts mit vorkonfigurierten Standardwerten zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="848c0-190">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="848c0-191">`CreateDefaultBuilder`<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> wird <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> aufgerufen, wenn <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>Folgendes ist:</span><span class="sxs-lookup"><span data-stu-id="848c0-191">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="848c0-192">Wenn `CreateDefaultBuilder` nicht aufgerufen wird, fügen Sie die Konfigurations Quelle für Benutzer Geheimnisse <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> explizit hinzu `Startup` , indem Sie im-Konstruktor aufrufen.</span><span class="sxs-lookup"><span data-stu-id="848c0-192">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> in the `Startup` constructor.</span></span> <span data-ttu-id="848c0-193">Wird `AddUserSecrets` nur aufgerufen, wenn die app in der Entwicklungsumgebung ausgeführt wird, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="848c0-193">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="848c0-194">Installieren Sie das nuget-Paket [Microsoft. Extensions. Configuration. usersecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) .</span><span class="sxs-lookup"><span data-stu-id="848c0-194">Install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="848c0-195">Fügen Sie die Konfigurations Quelle für <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> Benutzer Geheimnisse mit einem-Befehl `Startup` im-Konstruktor hinzu:</span><span class="sxs-lookup"><span data-stu-id="848c0-195">Add the user secrets configuration source with a call to <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets*> in the `Startup` constructor:</span></span>

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupConstructor&highlight=12)]

::: moniker-end

<span data-ttu-id="848c0-196">Benutzer Geheimnisse können über die `Configuration` API abgerufen werden:</span><span class="sxs-lookup"><span data-stu-id="848c0-196">User secrets can be retrieved via the `Configuration` API:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=26)]

::: moniker-end

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="848c0-197">Zuordnen von Geheimnissen zu einem poco</span><span class="sxs-lookup"><span data-stu-id="848c0-197">Map secrets to a POCO</span></span>

<span data-ttu-id="848c0-198">Das Mapping eines gesamten Objektliterals zu einem poco (eine einfache .NET-Klasse mit-Eigenschaften) ist nützlich für das aggregierten von verknüpften Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="848c0-198">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="848c0-199">Um die vorangehenden geheimen Schlüssel einem poco zuzuordnen, verwenden `Configuration` Sie die [Objekt Diagramm-Bindungs](xref:fundamentals/configuration/index#bind-to-an-object-graph) Funktion der API.</span><span class="sxs-lookup"><span data-stu-id="848c0-199">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="848c0-200">Der folgende Code bindet an ein Benutzer `MovieSettings` definiertes poco und `ServiceApiKey` greift auf den-Eigenschafts Wert zu:</span><span class="sxs-lookup"><span data-stu-id="848c0-200">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

::: moniker range=">= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

::: moniker range="= aspnetcore-1.0"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

::: moniker-end

<span data-ttu-id="848c0-201">Die `Movies:ConnectionString` geheim `Movies:ServiceApiKey` nisse und werden den entsprechenden Eigenschaften in `MovieSettings`zugeordnet:</span><span class="sxs-lookup"><span data-stu-id="848c0-201">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="848c0-202">Zeichen folgen Ersetzung mit geheimen Schlüsseln</span><span class="sxs-lookup"><span data-stu-id="848c0-202">String replacement with secrets</span></span>

<span data-ttu-id="848c0-203">Das Speichern von Kenn Wörtern im Klartext ist unsicher.</span><span class="sxs-lookup"><span data-stu-id="848c0-203">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="848c0-204">Eine in " *appSettings. JSON* " gespeicherte Daten bankverbindungs Zeichenfolge kann z. b. ein Kennwort für den angegebenen Benutzer enthalten:</span><span class="sxs-lookup"><span data-stu-id="848c0-204">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="848c0-205">Ein sichereren Ansatz besteht darin, das Kennwort als geheimen Schlüssel zu speichern.</span><span class="sxs-lookup"><span data-stu-id="848c0-205">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="848c0-206">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="848c0-206">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="848c0-207">Entfernen Sie `Password` das Schlüssel-Wert-Paar aus der Verbindungs Zeichenfolge in *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="848c0-207">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="848c0-208">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="848c0-208">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="848c0-209">Der Wert des Geheimnisses kann für die-Eigenschaft <xref:System.Data.SqlClient.SqlConnectionStringBuilder> eines- <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password*> Objekts festgelegt werden, um die Verbindungs Zeichenfolge abzuschließen:</span><span class="sxs-lookup"><span data-stu-id="848c0-209">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password*> property to complete the connection string:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](app-secrets/samples/1.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=26-29)]

::: moniker-end

## <a name="list-the-secrets"></a><span data-ttu-id="848c0-210">Auflisten der geheimen Schlüssel</span><span class="sxs-lookup"><span data-stu-id="848c0-210">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="848c0-211">Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die *csproj* -Datei vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="848c0-211">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="848c0-212">Die folgende Ausgabe wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="848c0-212">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="848c0-213">Im vorherigen Beispiel gibt ein Doppelpunkt in den Schlüsselnamen die Objekthierarchie in " *Secrets. JSON*" an.</span><span class="sxs-lookup"><span data-stu-id="848c0-213">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="848c0-214">Entfernen eines einzelnen geheimen Schlüssels</span><span class="sxs-lookup"><span data-stu-id="848c0-214">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="848c0-215">Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die *csproj* -Datei vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="848c0-215">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="848c0-216">Die Datei " *Secrets. JSON* " der App wurde geändert, um das Schlüssel-Wert-Paar `MoviesConnectionString` zu entfernen, das dem Schlüssel zugeordnet ist:</span><span class="sxs-lookup"><span data-stu-id="848c0-216">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="848c0-217">Wird `dotnet user-secrets list` ausgeführt, wird die folgende Meldung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="848c0-217">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="848c0-218">Alle geheimen Schlüssel entfernen</span><span class="sxs-lookup"><span data-stu-id="848c0-218">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="848c0-219">Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die *csproj* -Datei vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="848c0-219">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="848c0-220">Alle geheimen Benutzerschlüssel für die APP wurden aus der Datei " *Secrets. JSON* " gelöscht:</span><span class="sxs-lookup"><span data-stu-id="848c0-220">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="848c0-221">Wird `dotnet user-secrets list` ausgeführt, wird die folgende Meldung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="848c0-221">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="848c0-222">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="848c0-222">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>
