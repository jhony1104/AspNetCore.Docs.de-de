---
<span data-ttu-id="04001-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-102">'Blazor'</span></span>
- <span data-ttu-id="04001-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-103">'Identity'</span></span>
- <span data-ttu-id="04001-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-105">'Razor'</span></span>
- <span data-ttu-id="04001-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-106">'SignalR' uid:</span></span> 

---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="04001-107">Konfiguration in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="04001-107">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="04001-108">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="04001-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="04001-109">Die Konfiguration in ASP.NET Core erfolgt mithilfe eines oder mehrerer [Konfigurationsanbieter](#cp).</span><span class="sxs-lookup"><span data-stu-id="04001-109">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="04001-110">Konfigurationsanbieter lesen Konfigurationsdaten aus Schlüssel-Wert-Paaren unter Verwendung verschiedener Konfigurationsquellen:</span><span class="sxs-lookup"><span data-stu-id="04001-110">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="04001-111">Einstellungsdateien, z. B. *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="04001-111">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="04001-112">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="04001-112">Environment variables</span></span>
* <span data-ttu-id="04001-113">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="04001-113">Azure Key Vault</span></span>
* <span data-ttu-id="04001-114">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="04001-114">Azure App Configuration</span></span>
* <span data-ttu-id="04001-115">Befehlszeilenargumente</span><span class="sxs-lookup"><span data-stu-id="04001-115">Command-line arguments</span></span>
* <span data-ttu-id="04001-116">Benutzerdefinierte Anbieter (installiert oder erstellt)</span><span class="sxs-lookup"><span data-stu-id="04001-116">Custom providers, installed or created</span></span>
* <span data-ttu-id="04001-117">Verzeichnisdateien</span><span class="sxs-lookup"><span data-stu-id="04001-117">Directory files</span></span>
* <span data-ttu-id="04001-118">Speicherinterne .NET Objekte</span><span class="sxs-lookup"><span data-stu-id="04001-118">In-memory .NET objects</span></span>

<span data-ttu-id="04001-119">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="04001-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="04001-120">Standardkonfiguration</span><span class="sxs-lookup"><span data-stu-id="04001-120">Default configuration</span></span>

<span data-ttu-id="04001-121">ASP.NET Core-Web-Apps, die mit [dotnet new](/dotnet/core/tools/dotnet-new) oder Visual Studio erstellt wurden, generieren den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="04001-121">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="04001-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> legt die Standardkonfiguration für die App in der folgenden Reihenfolge fest:</span><span class="sxs-lookup"><span data-stu-id="04001-122"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="04001-123">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource):  Fügt eine vorhandene `IConfiguration` als Quelle hinzu.</span><span class="sxs-lookup"><span data-stu-id="04001-123">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="04001-124">Im Fall einer Standardkonfiguration wird die [Host](#hvac)-Konfiguration hinzugefügt und als erste Quelle für die _App-_ Konfiguration festgelegt.</span><span class="sxs-lookup"><span data-stu-id="04001-124">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="04001-125">[appsettings.json](#appsettingsjson) mithilfe des [JSON-Konfigurationsanbieters](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="04001-125">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="04001-126">*appsettings.* `Environment` *.json* mithilfe des [JSON-Konfigurationsanbieters](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="04001-126">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="04001-127">Beispielsweise *appsettings*.***Production***.*json* und *appsettings*.***Development***.*json*.</span><span class="sxs-lookup"><span data-stu-id="04001-127">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="04001-128">[App-Geheimnisse](xref:security/app-secrets), wenn die App in der `Development`-Umgebung ausgeführt wird</span><span class="sxs-lookup"><span data-stu-id="04001-128">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="04001-129">Umgebungsvariablen, die den [Umgebungsvariablen-Konfigurationsanbieter](#evcp) verwenden</span><span class="sxs-lookup"><span data-stu-id="04001-129">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="04001-130">Befehlszeilenargumente, die den [Befehlszeilen-Konfigurationsanbieter](#command-line) verwenden</span><span class="sxs-lookup"><span data-stu-id="04001-130">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="04001-131">Später hinzugefügte Konfigurationsanbieter überschreiben vorherige Schlüsseleinstellungen.</span><span class="sxs-lookup"><span data-stu-id="04001-131">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="04001-132">Wenn beispielsweise `MyKey` sowohl unter *appsettings.json* als auch unter Umgebung festgelegt wird, wird der Umgebungswert verwendet.</span><span class="sxs-lookup"><span data-stu-id="04001-132">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="04001-133">Bei Verwendung der Standardkonfigurationsanbieter überschreibt der [Befehlszeilen-Konfigurationsanbieter](#command-line-configuration-provider) alle anderen Anbieter.</span><span class="sxs-lookup"><span data-stu-id="04001-133">Using the default configuration providers, the  [Command-line configuration provider](#command-line-configuration-provider) overrides all other providers.</span></span>

<span data-ttu-id="04001-134">Weitere Informationen zu `CreateDefaultBuilder` finden Sie unter [Standardeinstellungen für den Generator](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="04001-134">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="04001-135">Im folgenden Code werden die aktivierten Konfigurationsanbieter in der Reihenfolge angezeigt, in der sie hinzugefügt wurden:</span><span class="sxs-lookup"><span data-stu-id="04001-135">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="04001-136">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="04001-136">appsettings.json</span></span>

<span data-ttu-id="04001-137">Sehen Sie sich die nachfolgende Datei *appsettings.json* an:</span><span class="sxs-lookup"><span data-stu-id="04001-137">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="04001-138">Im folgenden Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) sind mehrere der vorherigen Konfigurationseinstellungen zu sehen:</span><span class="sxs-lookup"><span data-stu-id="04001-138">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="04001-139">Der Standard-<xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> lädt die Konfiguration in der folgenden Reihenfolge:</span><span class="sxs-lookup"><span data-stu-id="04001-139">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="04001-140">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="04001-140">*appsettings.json*</span></span>
1. <span data-ttu-id="04001-141">*appsettings.* `Environment` *.json*: Beispielsweise die Dateien *appsettings*.***Production***.*json* und *appsettings*.***Development***.*json*.</span><span class="sxs-lookup"><span data-stu-id="04001-141">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="04001-142">Die Umgebungsversion der Datei wird basierend auf [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) geladen.</span><span class="sxs-lookup"><span data-stu-id="04001-142">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="04001-143">Weitere Informationen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="04001-143">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="04001-144">*appsettings*.`Environment`.*json*-Werte überschreiben Schlüssel in *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="04001-144">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="04001-145">Standardmäßig sind dies z. B.:</span><span class="sxs-lookup"><span data-stu-id="04001-145">For example, by default:</span></span>

* <span data-ttu-id="04001-146">In der Entwicklung überschreibt die *appsettings*.***Development***.*json*-Konfiguration in *appsettings.json* gefundene Werte.</span><span class="sxs-lookup"><span data-stu-id="04001-146">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="04001-147">In der Produktion überschreibt die *appsettings*.***Production***.*json*-Konfiguration in *appsettings.json* gefundene Werte.</span><span class="sxs-lookup"><span data-stu-id="04001-147">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="04001-148">Dies ist beispielsweise bei der Bereitstellung der App in Azure der Fall.</span><span class="sxs-lookup"><span data-stu-id="04001-148">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="04001-149">Binden hierarchischer Konfigurationsdaten mit dem Optionsmuster</span><span class="sxs-lookup"><span data-stu-id="04001-149">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="04001-150">Wenn die [Standard](#default)-Konfiguration verwendet wird, werden die Dateien *appsettings.json* und *appsettings.* `Environment` *.json* mit [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75) aktiviert.</span><span class="sxs-lookup"><span data-stu-id="04001-150">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="04001-151">Änderungen an den Dateien *appsettings.json* und *appsettings.* `Environment` *.json* ***nach*** dem Start der App werden vom [JSON-Konfigurationsanbieter](#jcp) gelesen.</span><span class="sxs-lookup"><span data-stu-id="04001-151">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="04001-152">Weitere Informationen zum Hinzufügen zusätzlicher JSON-Konfigurationsdateien finden Sie unter [JSON-Konfigurationsanbieter](#jcp) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="04001-152">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="04001-153">Sicherheits- und Secret-Manager</span><span class="sxs-lookup"><span data-stu-id="04001-153">Security and secret manager</span></span>

<span data-ttu-id="04001-154">Richtlinien für Konfigurationsdaten:</span><span class="sxs-lookup"><span data-stu-id="04001-154">Configuration data guidelines:</span></span>

* <span data-ttu-id="04001-155">Speichern Sie nie Kennwörter oder andere vertrauliche Daten im Konfigurationsanbietercode oder in Nur-Text-Konfigurationsdateien.</span><span class="sxs-lookup"><span data-stu-id="04001-155">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="04001-156">Der [Secret Manager](xref:security/app-secrets) kann zum Speichern von Geheimnissen in der Entwicklungsumgebung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="04001-156">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="04001-157">Verwenden Sie keine Produktionsgeheimnisse in Entwicklungs- oder Testumgebungen.</span><span class="sxs-lookup"><span data-stu-id="04001-157">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="04001-158">Geben Sie Geheimnisse außerhalb des Projekts an, damit sie nicht versehentlich in ein Quellcoderepository übernommen werden können.</span><span class="sxs-lookup"><span data-stu-id="04001-158">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="04001-159">[Standardmäßig](#default) liest [Secret Manager](xref:security/app-secrets) Konfigurationseinstellungen nach dem Lesen von *appsettings.json* und *appsettings.* `Environment` *.json*.</span><span class="sxs-lookup"><span data-stu-id="04001-159">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="04001-160">Weitere Informationen zum Speichern von Kennwörtern oder anderen vertraulichen Daten:</span><span class="sxs-lookup"><span data-stu-id="04001-160">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="04001-161"><xref:security/app-secrets>:  Enthält Hinweise zur Verwendung von Umgebungsvariablen für das Speichern vertraulicher Daten.</span><span class="sxs-lookup"><span data-stu-id="04001-161"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="04001-162">Der Secret Manager verwendet den [Dateikonfigurationsanbieter](#fcp), um Benutzergeheimnisse in einer JSON-Datei im lokalen System zu speichern.</span><span class="sxs-lookup"><span data-stu-id="04001-162">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="04001-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) speichert App-Geheimnisse für ASP.NET Core-Apps auf sichere Weise.</span><span class="sxs-lookup"><span data-stu-id="04001-163">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="04001-164">Weitere Informationen finden Sie unter <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="04001-164">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="04001-165">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="04001-165">Environment variables</span></span>

<span data-ttu-id="04001-166">Bei Verwendung der [Standard](#default)-Konfiguration lädt der <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> die Konfiguration aus Schlüssel-Wert-Paaren der Umgebungsvariablen, nachdem *appsettings.json*, *appsettings.* `Environment` *.json* und [Secret Manager](xref:security/app-secrets) gelesen wurden.</span><span class="sxs-lookup"><span data-stu-id="04001-166">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="04001-167">Daher überschreiben aus der Umgebung gelesene Schlüsselwerte Werte, die aus *appsettings.json*, *appsettings.* `Environment` *.json* und Secret Manager gelesen wurden.</span><span class="sxs-lookup"><span data-stu-id="04001-167">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="04001-168">Die folgenden `set`-Befehle:</span><span class="sxs-lookup"><span data-stu-id="04001-168">The following `set` commands:</span></span>

* <span data-ttu-id="04001-169">Legen die Umgebungsschlüssel und -werte des [vorangehenden Beispiels](#appsettingsjson) unter Windows fest.</span><span class="sxs-lookup"><span data-stu-id="04001-169">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="04001-170">Testen die Einstellungen bei Verwendung des [Beispieldownloads](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="04001-170">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="04001-171">Der `dotnet run`-Befehl muss im Projektverzeichnis ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="04001-171">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="04001-172">Die obigen Umgebungseinstellungen:</span><span class="sxs-lookup"><span data-stu-id="04001-172">The preceding environment settings:</span></span>

* <span data-ttu-id="04001-173">Werden nur in Prozessen festgelegt, die über das Befehlsfenster gestartet werden, in dem sie festgelegt wurden.</span><span class="sxs-lookup"><span data-stu-id="04001-173">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="04001-174">Werden nicht von Browsern gelesen, die mit Visual Studio gestartet wurden.</span><span class="sxs-lookup"><span data-stu-id="04001-174">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="04001-175">Die folgenden [setx](/windows-server/administration/windows-commands/setx)-Befehle können zum Festlegen der Umgebungsschlüssel und -werte unter Windows verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="04001-175">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="04001-176">Anders als `set` werden `setx`-Einstellungen beibehalten.</span><span class="sxs-lookup"><span data-stu-id="04001-176">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="04001-177">`/M` legt die Variable in der Systemumgebung fest.</span><span class="sxs-lookup"><span data-stu-id="04001-177">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="04001-178">Wenn der `/M`-Schalter nicht verwendet wird, wird eine Benutzerumgebungsvariable festgelegt.</span><span class="sxs-lookup"><span data-stu-id="04001-178">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="04001-179">Testen Sie wie folgt, ob die obigen Befehle *appsettings.json* und *appsettings.* `Environment` *.json* überschreiben:</span><span class="sxs-lookup"><span data-stu-id="04001-179">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="04001-180">Mit Visual Studio: Beenden Sie Visual Studio, und starten Sie dann Visual Studio neu.</span><span class="sxs-lookup"><span data-stu-id="04001-180">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="04001-181">Mit der Befehlszeilenschnittstelle: Starten Sie ein neues Befehlsfenster, und geben Sie `dotnet run` ein.</span><span class="sxs-lookup"><span data-stu-id="04001-181">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="04001-182">Rufen Sie <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> mit einer Zeichenfolge auf, um ein Präfix für Umgebungsvariablen anzugeben:</span><span class="sxs-lookup"><span data-stu-id="04001-182">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="04001-183">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="04001-183">In the preceding code:</span></span>

* <span data-ttu-id="04001-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` wird nach den [Standardkonfigurationsanbietern](#default) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="04001-184">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="04001-185">Ein Beispiel für das Festlegen der Reihenfolge der Konfigurationsanbieter finden Sie unter [JSON-Konfigurationsanbieter](#jcp).</span><span class="sxs-lookup"><span data-stu-id="04001-185">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="04001-186">Umgebungsvariablen, die mit dem `MyCustomPrefix_`-Präfix festgelegt wurden, überschreiben die [Standardkonfigurationsanbieter](#default).</span><span class="sxs-lookup"><span data-stu-id="04001-186">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="04001-187">Dies schließt Umgebungsvariablen ohne das Präfix ein.</span><span class="sxs-lookup"><span data-stu-id="04001-187">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="04001-188">Das Präfix wird beim Lesen der Schlüssel-Wert-Paare der Konfiguration entfernt.</span><span class="sxs-lookup"><span data-stu-id="04001-188">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="04001-189">Mit den folgenden Befehlen wird das benutzerdefinierte Präfix getestet:</span><span class="sxs-lookup"><span data-stu-id="04001-189">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="04001-190">Die [Standardkonfiguration](#default) lädt Umgebungsvariablen und Befehlszeilenargumente, die das Präfix `DOTNET_` und `ASPNETCORE_` aufweisen.</span><span class="sxs-lookup"><span data-stu-id="04001-190">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="04001-191">Die Präfixe `DOTNET_` und `ASPNETCORE_` werden von ASP.NET Core für die [Host- und App-Konfiguration](xref:fundamentals/host/generic-host#host-configuration), jedoch nicht für die Benutzerkonfiguration verwendet.</span><span class="sxs-lookup"><span data-stu-id="04001-191">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="04001-192">Weitere Informationen zur Host- und App-Konfiguration finden Sie unter [.NET Generic Host](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="04001-192">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="04001-193">Wählen Sie in [Azure App Service](https://azure.microsoft.com/services/app-service/) auf der Seite **Einstellungen > Konfiguration** die Option **Neue Anwendungseinstellung** aus.</span><span class="sxs-lookup"><span data-stu-id="04001-193">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="04001-194">Anwendungseinstellungen von Azure App Service werden:</span><span class="sxs-lookup"><span data-stu-id="04001-194">Azure App Service application settings are:</span></span>

* <span data-ttu-id="04001-195">Im Ruhezustand verschlüsselt und über einen verschlüsselten Kanal übermittelt.</span><span class="sxs-lookup"><span data-stu-id="04001-195">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="04001-196">Als Umgebungsvariablen verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="04001-196">Exposed as environment variables.</span></span>

<span data-ttu-id="04001-197">Weitere Informationen finden Sie unter [Azure-Apps: Überschreiben der App-Konfiguration im Azure-Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="04001-197">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="04001-198">Informationen zu Azure-Datenbankverbindungszeichenfolgen finden Sie unter [Präfixe für Verbindungszeichenfolgen](#constr).</span><span class="sxs-lookup"><span data-stu-id="04001-198">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="04001-199">Befehlszeile</span><span class="sxs-lookup"><span data-stu-id="04001-199">Command-line</span></span>

<span data-ttu-id="04001-200">Bei Verwendung der [Standard](#default)-Konfiguration lädt der <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> die Konfiguration aus den Schlüssel-Wert-Paaren des Befehlszeilenarguments nach den folgenden Konfigurationsquellen:</span><span class="sxs-lookup"><span data-stu-id="04001-200">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="04001-201">Dateien *appsettings.json* und *appsettings*.`Environment`.*json*.</span><span class="sxs-lookup"><span data-stu-id="04001-201">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="04001-202">[App-Geheimnisse (Secret Manager)](xref:security/app-secrets) in der Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="04001-202">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="04001-203">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="04001-203">Environment variables.</span></span>

<span data-ttu-id="04001-204">[Standardmäßig](#default) überschreiben in der Befehlszeile festgelegte Konfigurationswerte die Konfigurationswerte, die mit allen anderen Konfigurationsanbietern festgelegt wurden.</span><span class="sxs-lookup"><span data-stu-id="04001-204">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="04001-205">Befehlszeilenargumente</span><span class="sxs-lookup"><span data-stu-id="04001-205">Command-line arguments</span></span>

<span data-ttu-id="04001-206">Der folgende Befehl legt Schlüssel und Werte unter Verwendung von `=` fest:</span><span class="sxs-lookup"><span data-stu-id="04001-206">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="04001-207">Der folgende Befehl legt Schlüssel und Werte unter Verwendung von `/` fest:</span><span class="sxs-lookup"><span data-stu-id="04001-207">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="04001-208">Der folgende Befehl legt Schlüssel und Werte unter Verwendung von `--` fest:</span><span class="sxs-lookup"><span data-stu-id="04001-208">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="04001-209">Der Schlüsselwert:</span><span class="sxs-lookup"><span data-stu-id="04001-209">The key value:</span></span>

* <span data-ttu-id="04001-210">Muss auf `=` folgen, oder der Schlüssel muss das Präfix `--` oder `/` aufweisen, wenn der Wert auf ein Leerzeichen folgt.</span><span class="sxs-lookup"><span data-stu-id="04001-210">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="04001-211">Ist nicht erforderlich, wenn `=` verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="04001-211">Isn't required if `=` is used.</span></span> <span data-ttu-id="04001-212">Beispielsweise `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="04001-212">For example, `MySetting=`.</span></span>

<span data-ttu-id="04001-213">Kombinieren Sie in einem Befehl nicht Schlüssel-Wert-Paare des Befehlszeilenarguments, die `=` verwenden, mit Schlüssel-Wert-Paaren, die ein Leerzeichen verwenden.</span><span class="sxs-lookup"><span data-stu-id="04001-213">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="04001-214">Switchmappings</span><span class="sxs-lookup"><span data-stu-id="04001-214">Switch mappings</span></span>

<span data-ttu-id="04001-215">Switchmappings erlauben das Angeben einer Logik zum Ersetzen von **Schlüsselnamen**.</span><span class="sxs-lookup"><span data-stu-id="04001-215">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="04001-216">Stellen Sie ein Wörterbuch mit Switchersetzungen für die <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>-Methode bereit.</span><span class="sxs-lookup"><span data-stu-id="04001-216">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="04001-217">Wenn das Switchmappingwörterbuch verwendet wird, wird das Wörterbuch für Schlüssel, die dem von einem Befehlszeilenargument angegebenen Schlüssel entsprechen, überprüft.</span><span class="sxs-lookup"><span data-stu-id="04001-217">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="04001-218">Wenn der Befehlszeilenschlüssel im Wörterbuch gefunden wird, wird der Wörterbuchwert zum Festlegen des Schlüssel-Wert-Paares in der App-Konfiguration zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="04001-218">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="04001-219">Ein Switchmapping ist für jeden Befehlszeilenschlüssel erforderlich, dem ein einzelner Gedankenstrich (`-`) vorangestellt ist.</span><span class="sxs-lookup"><span data-stu-id="04001-219">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="04001-220">Regeln für Schlüssel von Switchmappingwörterbüchern:</span><span class="sxs-lookup"><span data-stu-id="04001-220">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="04001-221">Switches müssen mit `-` oder `--` beginnen.</span><span class="sxs-lookup"><span data-stu-id="04001-221">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="04001-222">Das Switchmappingwörterbuch darf keine doppelten Schlüssel enthalten.</span><span class="sxs-lookup"><span data-stu-id="04001-222">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="04001-223">Wenn Sie ein Switchmappingwörterbuch verwenden möchten, übergeben Sie es an den `AddCommandLine`-Abruf:</span><span class="sxs-lookup"><span data-stu-id="04001-223">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="04001-224">Der folgende Code zeigt die Schlüsselwerte für die ersetzten Schlüssel:</span><span class="sxs-lookup"><span data-stu-id="04001-224">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="04001-225">Führen Sie den folgenden Befehl aus, um die Schlüsselersetzung zu testen:</span><span class="sxs-lookup"><span data-stu-id="04001-225">Run the following command to test the key replacement:</span></span>

```dotnetcli
dotnet run -k1=value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="04001-226">Hinweis: Derzeit kann `=` nicht verwendet werden, um Schlüsselersatzwerte mit nur einem Bindestrich `-` festzulegen.</span><span class="sxs-lookup"><span data-stu-id="04001-226">Note: Currently, `=` cannot be used to set key-replacement values with a single dash `-`.</span></span> <span data-ttu-id="04001-227">Weitere Informationen finden Sie im entsprechenden [GitHub-Issue](https://github.com/dotnet/extensions/issues/3059).</span><span class="sxs-lookup"><span data-stu-id="04001-227">See [this GitHub issue](https://github.com/dotnet/extensions/issues/3059).</span></span>

<span data-ttu-id="04001-228">Mit dem folgenden Befehl kann die Schlüsselersetzung getestet werden:</span><span class="sxs-lookup"><span data-stu-id="04001-228">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="04001-229">Bei Apps, die Switchmappings verwenden, sollten im `CreateDefaultBuilder`-Aufruf keine Argumente übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="04001-229">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="04001-230">Der `AddCommandLine`-Aufruf der `CreateDefaultBuilder`-Methode umfasst keine zugeordneten Switches, und das Switchmappingwörterbuch kann nicht an `CreateDefaultBuilder` übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="04001-230">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="04001-231">Die Lösung besteht nicht darin, die Argumente an `CreateDefaultBuilder` zu übergeben, sondern der `AddCommandLine`-Methode der `ConfigurationBuilder`-Methode zu erlauben, sowohl die Argumente als auch das Switchmappingwörterbuch zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="04001-231">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="04001-232">Hierarchische Konfigurationsdaten</span><span class="sxs-lookup"><span data-stu-id="04001-232">Hierarchical configuration data</span></span>

<span data-ttu-id="04001-233">Die Konfigurations-API liest hierarchische Konfigurationsdaten, indem sie die hierarchischen Daten mit einem Trennzeichen in den Konfigurationsschlüsseln vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="04001-233">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="04001-234">Der [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) enthält die folgende Datei *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="04001-234">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="04001-235">Der folgende Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zeigt einige der Konfigurationseinstellungen:</span><span class="sxs-lookup"><span data-stu-id="04001-235">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="04001-236">Die bevorzugte Methode zum Lesen hierarchischer Konfigurationsdaten ist die Verwendung des Optionsmusters.</span><span class="sxs-lookup"><span data-stu-id="04001-236">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="04001-237">Weitere Informationen finden Sie unter [Binden hierarchischer Konfigurationsdaten](#optpat) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="04001-237">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="04001-238">Mit den Methoden <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> und <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> können Abschnitte und untergeordnete Abschnittelemente in den Konfigurationsdaten isoliert werden.</span><span class="sxs-lookup"><span data-stu-id="04001-238"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="04001-239">Diese Methoden werden später im Abschnitt [GetSection, GetChildren und Exists](#getsection) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="04001-239">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="04001-240">Konfigurationsschlüssel und -werte</span><span class="sxs-lookup"><span data-stu-id="04001-240">Configuration keys and values</span></span>

<span data-ttu-id="04001-241">Konfigurationsschlüssel:</span><span class="sxs-lookup"><span data-stu-id="04001-241">Configuration keys:</span></span>

* <span data-ttu-id="04001-242">Groß-/Kleinschreibung wird beachtet.</span><span class="sxs-lookup"><span data-stu-id="04001-242">Are case-insensitive.</span></span> <span data-ttu-id="04001-243">Beispielsweise verweisen `ConnectionString` und `connectionstring` auf denselben Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="04001-243">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="04001-244">Wenn ein Schlüssel und ein Wert in mehreren Konfigurationsanbietern festgelegt ist, wird der Wert des zuletzt hinzugefügten Anbieters verwendet.</span><span class="sxs-lookup"><span data-stu-id="04001-244">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="04001-245">Weitere Informationen finden Sie unter [Standardkonfiguration](#default).</span><span class="sxs-lookup"><span data-stu-id="04001-245">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="04001-246">Hierarchische Schlüssel</span><span class="sxs-lookup"><span data-stu-id="04001-246">Hierarchical keys</span></span>
  * <span data-ttu-id="04001-247">Innerhalb der Konfigurations-API funktioniert ein Doppelpunkt (`:`) als Trennzeichen auf allen Plattformen.</span><span class="sxs-lookup"><span data-stu-id="04001-247">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="04001-248">In Umgebungsvariablen funktioniert ein Doppelpunkt als Trennzeichen ggf. nicht auf allen Plattformen.</span><span class="sxs-lookup"><span data-stu-id="04001-248">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="04001-249">Ein doppelter Unterstrich (`__`) wird von allen Plattformen unterstützt und automatisch in einen Doppelpunkt (`:`) umgewandelt.</span><span class="sxs-lookup"><span data-stu-id="04001-249">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="04001-250">In Azure Key Vault verwenden hierarchische Schlüssel `--` als Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="04001-250">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="04001-251">Der [Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration) ersetzt `--` automatisch durch `:`, wenn die Geheimnisse in die Konfiguration der App geladen werden.</span><span class="sxs-lookup"><span data-stu-id="04001-251">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="04001-252"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> unterstützt das Binden von Arrays an Objekte mit Arrayindizes in Konfigurationsschlüsseln.</span><span class="sxs-lookup"><span data-stu-id="04001-252">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="04001-253">Die Arraybindung wird im Abschnitt [Binden eines Arrays an eine Klasse](#boa) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="04001-253">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="04001-254">Konfigurationswerte:</span><span class="sxs-lookup"><span data-stu-id="04001-254">Configuration values:</span></span>

* <span data-ttu-id="04001-255">Sind Zeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="04001-255">Are strings.</span></span>
* <span data-ttu-id="04001-256">NULL-Werte können nicht in einer Konfiguration gespeichert oder an Objekte gebunden werden.</span><span class="sxs-lookup"><span data-stu-id="04001-256">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="04001-257">Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="04001-257">Configuration providers</span></span>

<span data-ttu-id="04001-258">Die folgende Tabelle zeigt die für ASP.NET Core-Apps verfügbaren Konfigurationsanbieter.</span><span class="sxs-lookup"><span data-stu-id="04001-258">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="04001-259">Anbieter</span><span class="sxs-lookup"><span data-stu-id="04001-259">Provider</span></span> | <span data-ttu-id="04001-260">Bereitstellung der Konfiguration über</span><span class="sxs-lookup"><span data-stu-id="04001-260">Provides configuration from</span></span> |
| ---
<span data-ttu-id="04001-261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-262">'Blazor'</span></span>
- <span data-ttu-id="04001-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-263">'Identity'</span></span>
- <span data-ttu-id="04001-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-265">'Razor'</span></span>
- <span data-ttu-id="04001-266">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-268">'Blazor'</span></span>
- <span data-ttu-id="04001-269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-269">'Identity'</span></span>
- <span data-ttu-id="04001-270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-270">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-271">'Razor'</span></span>
- <span data-ttu-id="04001-272">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-272">'SignalR' uid:</span></span> 

<span data-ttu-id="04001-273">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-273">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-274">'Blazor'</span></span>
- <span data-ttu-id="04001-275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-275">'Identity'</span></span>
- <span data-ttu-id="04001-276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-276">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-277">'Razor'</span></span>
- <span data-ttu-id="04001-278">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-280">'Blazor'</span></span>
- <span data-ttu-id="04001-281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-281">'Identity'</span></span>
- <span data-ttu-id="04001-282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-282">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-283">'Razor'</span></span>
- <span data-ttu-id="04001-284">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-286">'Blazor'</span></span>
- <span data-ttu-id="04001-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-287">'Identity'</span></span>
- <span data-ttu-id="04001-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-289">'Razor'</span></span>
- <span data-ttu-id="04001-290">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-292">'Blazor'</span></span>
- <span data-ttu-id="04001-293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-293">'Identity'</span></span>
- <span data-ttu-id="04001-294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-294">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-295">'Razor'</span></span>
- <span data-ttu-id="04001-296">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-296">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-297">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-297">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-298">'Blazor'</span></span>
- <span data-ttu-id="04001-299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-299">'Identity'</span></span>
- <span data-ttu-id="04001-300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-300">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-301">'Razor'</span></span>
- <span data-ttu-id="04001-302">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-304">'Blazor'</span></span>
- <span data-ttu-id="04001-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-305">'Identity'</span></span>
- <span data-ttu-id="04001-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-307">'Razor'</span></span>
- <span data-ttu-id="04001-308">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-310">'Blazor'</span></span>
- <span data-ttu-id="04001-311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-311">'Identity'</span></span>
- <span data-ttu-id="04001-312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-312">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-313">'Razor'</span></span>
- <span data-ttu-id="04001-314">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-316">'Blazor'</span></span>
- <span data-ttu-id="04001-317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-317">'Identity'</span></span>
- <span data-ttu-id="04001-318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-318">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-319">'Razor'</span></span>
- <span data-ttu-id="04001-320">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-322">'Blazor'</span></span>
- <span data-ttu-id="04001-323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-323">'Identity'</span></span>
- <span data-ttu-id="04001-324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-324">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-325">'Razor'</span></span>
- <span data-ttu-id="04001-326">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-328">'Blazor'</span></span>
- <span data-ttu-id="04001-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-329">'Identity'</span></span>
- <span data-ttu-id="04001-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-331">'Razor'</span></span>
- <span data-ttu-id="04001-332">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-334">'Blazor'</span></span>
- <span data-ttu-id="04001-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-335">'Identity'</span></span>
- <span data-ttu-id="04001-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-337">'Razor'</span></span>
- <span data-ttu-id="04001-338">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-340">'Blazor'</span></span>
- <span data-ttu-id="04001-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-341">'Identity'</span></span>
- <span data-ttu-id="04001-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-343">'Razor'</span></span>
- <span data-ttu-id="04001-344">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-346">'Blazor'</span></span>
- <span data-ttu-id="04001-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-347">'Identity'</span></span>
- <span data-ttu-id="04001-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-349">'Razor'</span></span>
- <span data-ttu-id="04001-350">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-352">'Blazor'</span></span>
- <span data-ttu-id="04001-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-353">'Identity'</span></span>
- <span data-ttu-id="04001-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-355">'Razor'</span></span>
- <span data-ttu-id="04001-356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-358">'Blazor'</span></span>
- <span data-ttu-id="04001-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-359">'Identity'</span></span>
- <span data-ttu-id="04001-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-361">'Razor'</span></span>
- <span data-ttu-id="04001-362">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-362">'SignalR' uid:</span></span> 

<span data-ttu-id="04001-363">------------------ | | [Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration) | Azure Key Vault | | [Azure-App-Konfigurationsanbieter](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Azure App Configuration | | [Befehlszeilen-Konfigurationsanbieter](#clcp) | Befehlszeilenparameter | | [Benutzerdefinierter Konfigurationsanbieter](#custom-configuration-provider) | Benutzerdefinierte Quelle | | [Umgebungsvariablen-Konfigurationsanbieter](#evcp) | Umgebungsvariablen | | [Dateikonfigurationsanbieter](#file-configuration-provider) | INI-, JSON- und XML-Dateien| | [Schlüssel-pro-Datei-Konfigurationsanbieter](#key-per-file-configuration-provider) | Verzeichnisdateien | | [Speicherkonfigurationsanbieter](#memory-configuration-provider) | In-Memory-Sammlungen | | [Geheimnis-Manager](xref:security/app-secrets)  | Datei im Benutzerprofilverzeichnis |</span><span class="sxs-lookup"><span data-stu-id="04001-363">------------------ | | [Azure Key Vault configuration provider](xref:security/key-vault-configuration) | Azure Key Vault | | [Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) | Azure App Configuration | | [Command-line configuration provider](#clcp) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables configuration provider](#evcp) | Environment variables | | [File configuration provider](#file-configuration-provider) | INI, JSON, and XML files | | [Key-per-file configuration provider](#key-per-file-configuration-provider) | Directory files | | [Memory configuration provider](#memory-configuration-provider) | In-memory collections | | [Secret Manager](xref:security/app-secrets)  | File in the user profile directory |</span></span>

<span data-ttu-id="04001-364">Konfigurationsquellen werden in der Reihenfolge gelesen, in der ihre Konfigurationsanbieter angegeben sind.</span><span class="sxs-lookup"><span data-stu-id="04001-364">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="04001-365">Ordnen Sie die Konfigurationsanbieter im Code so an, dass sie den Prioritäten für die zugrunde liegenden Konfigurationsquellen entsprechen, die für die App erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="04001-365">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="04001-366">Eine typische Konfigurationsanbietersequenz ist:</span><span class="sxs-lookup"><span data-stu-id="04001-366">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="04001-367">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="04001-367">*appsettings.json*</span></span>
1. <span data-ttu-id="04001-368">*appsettings*.`Environment`.*json*</span><span class="sxs-lookup"><span data-stu-id="04001-368">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="04001-369">Secret Manager</span><span class="sxs-lookup"><span data-stu-id="04001-369">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="04001-370">Umgebungsvariablen, die den [Umgebungsvariablen-Konfigurationsanbieter](#evcp) verwenden</span><span class="sxs-lookup"><span data-stu-id="04001-370">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="04001-371">Befehlszeilenargumente, die den [Befehlszeilen-Konfigurationsanbieter](#command-line-configuration-provider) verwenden</span><span class="sxs-lookup"><span data-stu-id="04001-371">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="04001-372">In der Regel werden Befehlszeilen-Konfigurationsanbieter in einer Reihe von Anbietern an letzter Stelle hinzugefügt, damit Befehlszeilenargumente die von anderen Anbietern festgelegte Konfiguration überschreiben können.</span><span class="sxs-lookup"><span data-stu-id="04001-372">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="04001-373">Die obige Sequenz von Anbietern wird in der [Standardkonfiguration](#default) verwendet.</span><span class="sxs-lookup"><span data-stu-id="04001-373">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="04001-374">Präfixe für Verbindungszeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="04001-374">Connection string prefixes</span></span>

<span data-ttu-id="04001-375">Die Konfigurations-API verfügt über spezielle Verarbeitungsregeln für vier Umgebungsvariablen für Verbindungszeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="04001-375">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="04001-376">Diese Verbindungszeichenfolgen sind beim Konfigurieren von Azure-Verbindungszeichenfolgen für die App-Umgebung beteiligt.</span><span class="sxs-lookup"><span data-stu-id="04001-376">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="04001-377">Umgebungsvariablen mit den in der Tabelle aufgeführten Präfixen werden mit der [Standardkonfiguration](#default) in die App geladen bzw. wenn kein Präfix für `AddEnvironmentVariables` bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="04001-377">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="04001-378">Präfix für Verbindungszeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="04001-378">Connection string prefix</span></span> | <span data-ttu-id="04001-379">Anbieter</span><span class="sxs-lookup"><span data-stu-id="04001-379">Provider</span></span> |
| ---
<span data-ttu-id="04001-380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-381">'Blazor'</span></span>
- <span data-ttu-id="04001-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-382">'Identity'</span></span>
- <span data-ttu-id="04001-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-384">'Razor'</span></span>
- <span data-ttu-id="04001-385">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-387">'Blazor'</span></span>
- <span data-ttu-id="04001-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-388">'Identity'</span></span>
- <span data-ttu-id="04001-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-390">'Razor'</span></span>
- <span data-ttu-id="04001-391">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-393">'Blazor'</span></span>
- <span data-ttu-id="04001-394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-394">'Identity'</span></span>
- <span data-ttu-id="04001-395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-395">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-396">'Razor'</span></span>
- <span data-ttu-id="04001-397">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-397">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-398">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-398">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-399">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-399">'Blazor'</span></span>
- <span data-ttu-id="04001-400">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-400">'Identity'</span></span>
- <span data-ttu-id="04001-401">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-401">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-402">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-402">'Razor'</span></span>
- <span data-ttu-id="04001-403">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-403">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-404">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-404">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-405">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-405">'Blazor'</span></span>
- <span data-ttu-id="04001-406">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-406">'Identity'</span></span>
- <span data-ttu-id="04001-407">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-407">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-408">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-408">'Razor'</span></span>
- <span data-ttu-id="04001-409">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-409">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-410">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-410">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-411">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-411">'Blazor'</span></span>
- <span data-ttu-id="04001-412">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-412">'Identity'</span></span>
- <span data-ttu-id="04001-413">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-413">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-414">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-414">'Razor'</span></span>
- <span data-ttu-id="04001-415">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-415">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-416">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-416">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-417">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-417">'Blazor'</span></span>
- <span data-ttu-id="04001-418">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-418">'Identity'</span></span>
- <span data-ttu-id="04001-419">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-419">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-420">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-420">'Razor'</span></span>
- <span data-ttu-id="04001-421">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-421">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-422">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-422">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-423">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-423">'Blazor'</span></span>
- <span data-ttu-id="04001-424">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-424">'Identity'</span></span>
- <span data-ttu-id="04001-425">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-425">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-426">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-426">'Razor'</span></span>
- <span data-ttu-id="04001-427">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-427">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-429">'Blazor'</span></span>
- <span data-ttu-id="04001-430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-430">'Identity'</span></span>
- <span data-ttu-id="04001-431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-431">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-432">'Razor'</span></span>
- <span data-ttu-id="04001-433">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-433">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-435">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-435">'Blazor'</span></span>
- <span data-ttu-id="04001-436">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-436">'Identity'</span></span>
- <span data-ttu-id="04001-437">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-437">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-438">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-438">'Razor'</span></span>
- <span data-ttu-id="04001-439">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-439">'SignalR' uid:</span></span> 

<span data-ttu-id="04001-440">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-440">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-441">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-441">'Blazor'</span></span>
- <span data-ttu-id="04001-442">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-442">'Identity'</span></span>
- <span data-ttu-id="04001-443">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-443">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-444">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-444">'Razor'</span></span>
- <span data-ttu-id="04001-445">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-445">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-447">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-447">'Blazor'</span></span>
- <span data-ttu-id="04001-448">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-448">'Identity'</span></span>
- <span data-ttu-id="04001-449">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-449">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-450">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-450">'Razor'</span></span>
- <span data-ttu-id="04001-451">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-451">'SignalR' uid:</span></span> 

<span data-ttu-id="04001-452">---- | | `CUSTOMCONNSTR_` | Benutzerdefinierter Anbieter | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL-Datenbank](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span><span class="sxs-lookup"><span data-stu-id="04001-452">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="04001-453">Wenn eine Umgebungsvariable entdeckt und mit einem der vier Präfixe aus der Tabelle in die Konfiguration geladen wird, tritt Folgendes ein:</span><span class="sxs-lookup"><span data-stu-id="04001-453">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="04001-454">Der Konfigurationsschlüssel wird durch Entfernen des Umgebungsvariablenpräfixes und Hinzufügen eines Konfigurationsschlüsselabschnitts (`ConnectionStrings`) erstellt.</span><span class="sxs-lookup"><span data-stu-id="04001-454">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="04001-455">Ein neues Konfigurations-Schlüssel-Wert-Paar wird erstellt, das den Datenbankverbindungsanbieter repräsentiert (mit Ausnahme des Präfixes `CUSTOMCONNSTR_`, für das kein Anbieter angegeben ist).</span><span class="sxs-lookup"><span data-stu-id="04001-455">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="04001-456">Umgebungsvariablenschlüssel</span><span class="sxs-lookup"><span data-stu-id="04001-456">Environment variable key</span></span> | <span data-ttu-id="04001-457">Konvertierter Konfigurationsschlüssel</span><span class="sxs-lookup"><span data-stu-id="04001-457">Converted configuration key</span></span> | <span data-ttu-id="04001-458">Anbieterkonfigurationseintrag</span><span class="sxs-lookup"><span data-stu-id="04001-458">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="04001-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-460">'Blazor'</span></span>
- <span data-ttu-id="04001-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-461">'Identity'</span></span>
- <span data-ttu-id="04001-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-463">'Razor'</span></span>
- <span data-ttu-id="04001-464">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-466">'Blazor'</span></span>
- <span data-ttu-id="04001-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-467">'Identity'</span></span>
- <span data-ttu-id="04001-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-469">'Razor'</span></span>
- <span data-ttu-id="04001-470">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-472">'Blazor'</span></span>
- <span data-ttu-id="04001-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-473">'Identity'</span></span>
- <span data-ttu-id="04001-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-475">'Razor'</span></span>
- <span data-ttu-id="04001-476">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-478">'Blazor'</span></span>
- <span data-ttu-id="04001-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-479">'Identity'</span></span>
- <span data-ttu-id="04001-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-481">'Razor'</span></span>
- <span data-ttu-id="04001-482">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-484">'Blazor'</span></span>
- <span data-ttu-id="04001-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-485">'Identity'</span></span>
- <span data-ttu-id="04001-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-487">'Razor'</span></span>
- <span data-ttu-id="04001-488">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-490">'Blazor'</span></span>
- <span data-ttu-id="04001-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-491">'Identity'</span></span>
- <span data-ttu-id="04001-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-493">'Razor'</span></span>
- <span data-ttu-id="04001-494">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-496">'Blazor'</span></span>
- <span data-ttu-id="04001-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-497">'Identity'</span></span>
- <span data-ttu-id="04001-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-499">'Razor'</span></span>
- <span data-ttu-id="04001-500">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-502">'Blazor'</span></span>
- <span data-ttu-id="04001-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-503">'Identity'</span></span>
- <span data-ttu-id="04001-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-505">'Razor'</span></span>
- <span data-ttu-id="04001-506">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-508">'Blazor'</span></span>
- <span data-ttu-id="04001-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-509">'Identity'</span></span>
- <span data-ttu-id="04001-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-511">'Razor'</span></span>
- <span data-ttu-id="04001-512">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-512">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-513">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-514">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-514">'Blazor'</span></span>
- <span data-ttu-id="04001-515">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-515">'Identity'</span></span>
- <span data-ttu-id="04001-516">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-516">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-517">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-517">'Razor'</span></span>
- <span data-ttu-id="04001-518">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-518">'SignalR' uid:</span></span> 

<span data-ttu-id="04001-519">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-519">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-520">'Blazor'</span></span>
- <span data-ttu-id="04001-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-521">'Identity'</span></span>
- <span data-ttu-id="04001-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-523">'Razor'</span></span>
- <span data-ttu-id="04001-524">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-526">'Blazor'</span></span>
- <span data-ttu-id="04001-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-527">'Identity'</span></span>
- <span data-ttu-id="04001-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-529">'Razor'</span></span>
- <span data-ttu-id="04001-530">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-532">'Blazor'</span></span>
- <span data-ttu-id="04001-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-533">'Identity'</span></span>
- <span data-ttu-id="04001-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-535">'Razor'</span></span>
- <span data-ttu-id="04001-536">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-538">'Blazor'</span></span>
- <span data-ttu-id="04001-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-539">'Identity'</span></span>
- <span data-ttu-id="04001-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-541">'Razor'</span></span>
- <span data-ttu-id="04001-542">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-544">'Blazor'</span></span>
- <span data-ttu-id="04001-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-545">'Identity'</span></span>
- <span data-ttu-id="04001-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-547">'Razor'</span></span>
- <span data-ttu-id="04001-548">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-550">'Blazor'</span></span>
- <span data-ttu-id="04001-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-551">'Identity'</span></span>
- <span data-ttu-id="04001-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-553">'Razor'</span></span>
- <span data-ttu-id="04001-554">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-556">'Blazor'</span></span>
- <span data-ttu-id="04001-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-557">'Identity'</span></span>
- <span data-ttu-id="04001-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-559">'Razor'</span></span>
- <span data-ttu-id="04001-560">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-562">'Blazor'</span></span>
- <span data-ttu-id="04001-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-563">'Identity'</span></span>
- <span data-ttu-id="04001-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-565">'Razor'</span></span>
- <span data-ttu-id="04001-566">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-568">'Blazor'</span></span>
- <span data-ttu-id="04001-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-569">'Identity'</span></span>
- <span data-ttu-id="04001-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-571">'Razor'</span></span>
- <span data-ttu-id="04001-572">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-574">'Blazor'</span></span>
- <span data-ttu-id="04001-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-575">'Identity'</span></span>
- <span data-ttu-id="04001-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-577">'Razor'</span></span>
- <span data-ttu-id="04001-578">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-580">'Blazor'</span></span>
- <span data-ttu-id="04001-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-581">'Identity'</span></span>
- <span data-ttu-id="04001-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-583">'Razor'</span></span>
- <span data-ttu-id="04001-584">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-584">'SignalR' uid:</span></span> 

<span data-ttu-id="04001-585">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-585">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-586">'Blazor'</span></span>
- <span data-ttu-id="04001-587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-587">'Identity'</span></span>
- <span data-ttu-id="04001-588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-588">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-589">'Razor'</span></span>
- <span data-ttu-id="04001-590">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-592">'Blazor'</span></span>
- <span data-ttu-id="04001-593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-593">'Identity'</span></span>
- <span data-ttu-id="04001-594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-594">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-595">'Razor'</span></span>
- <span data-ttu-id="04001-596">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-598">'Blazor'</span></span>
- <span data-ttu-id="04001-599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-599">'Identity'</span></span>
- <span data-ttu-id="04001-600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-600">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-601">'Razor'</span></span>
- <span data-ttu-id="04001-602">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-604">'Blazor'</span></span>
- <span data-ttu-id="04001-605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-605">'Identity'</span></span>
- <span data-ttu-id="04001-606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-606">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-607">'Razor'</span></span>
- <span data-ttu-id="04001-608">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-610">'Blazor'</span></span>
- <span data-ttu-id="04001-611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-611">'Identity'</span></span>
- <span data-ttu-id="04001-612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-612">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-613">'Razor'</span></span>
- <span data-ttu-id="04001-614">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-616">'Blazor'</span></span>
- <span data-ttu-id="04001-617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-617">'Identity'</span></span>
- <span data-ttu-id="04001-618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-618">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-619">'Razor'</span></span>
- <span data-ttu-id="04001-620">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-622">'Blazor'</span></span>
- <span data-ttu-id="04001-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-623">'Identity'</span></span>
- <span data-ttu-id="04001-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-625">'Razor'</span></span>
- <span data-ttu-id="04001-626">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-628">'Blazor'</span></span>
- <span data-ttu-id="04001-629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-629">'Identity'</span></span>
- <span data-ttu-id="04001-630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-630">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-631">'Razor'</span></span>
- <span data-ttu-id="04001-632">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-634">'Blazor'</span></span>
- <span data-ttu-id="04001-635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-635">'Identity'</span></span>
- <span data-ttu-id="04001-636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-636">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-637">'Razor'</span></span>
- <span data-ttu-id="04001-638">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-640">'Blazor'</span></span>
- <span data-ttu-id="04001-641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-641">'Identity'</span></span>
- <span data-ttu-id="04001-642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-642">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-643">'Razor'</span></span>
- <span data-ttu-id="04001-644">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-646">'Blazor'</span></span>
- <span data-ttu-id="04001-647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-647">'Identity'</span></span>
- <span data-ttu-id="04001-648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-648">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-649">'Razor'</span></span>
- <span data-ttu-id="04001-650">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-652">'Blazor'</span></span>
- <span data-ttu-id="04001-653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-653">'Identity'</span></span>
- <span data-ttu-id="04001-654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-654">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-655">'Razor'</span></span>
- <span data-ttu-id="04001-656">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-658">'Blazor'</span></span>
- <span data-ttu-id="04001-659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-659">'Identity'</span></span>
- <span data-ttu-id="04001-660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-660">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-661">'Razor'</span></span>
- <span data-ttu-id="04001-662">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-662">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-664">'Blazor'</span></span>
- <span data-ttu-id="04001-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-665">'Identity'</span></span>
- <span data-ttu-id="04001-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-667">'Razor'</span></span>
- <span data-ttu-id="04001-668">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-670">'Blazor'</span></span>
- <span data-ttu-id="04001-671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-671">'Identity'</span></span>
- <span data-ttu-id="04001-672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-672">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-673">'Razor'</span></span>
- <span data-ttu-id="04001-674">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-674">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-676">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-676">'Blazor'</span></span>
- <span data-ttu-id="04001-677">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-677">'Identity'</span></span>
- <span data-ttu-id="04001-678">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-678">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-679">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-679">'Razor'</span></span>
- <span data-ttu-id="04001-680">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-680">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-682">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-682">'Blazor'</span></span>
- <span data-ttu-id="04001-683">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-683">'Identity'</span></span>
- <span data-ttu-id="04001-684">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-684">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-685">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-685">'Razor'</span></span>
- <span data-ttu-id="04001-686">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-686">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-688">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-688">'Blazor'</span></span>
- <span data-ttu-id="04001-689">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-689">'Identity'</span></span>
- <span data-ttu-id="04001-690">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-690">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-691">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-691">'Razor'</span></span>
- <span data-ttu-id="04001-692">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-692">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-694">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-694">'Blazor'</span></span>
- <span data-ttu-id="04001-695">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-695">'Identity'</span></span>
- <span data-ttu-id="04001-696">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-696">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-697">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-697">'Razor'</span></span>
- <span data-ttu-id="04001-698">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-698">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-700">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-700">'Blazor'</span></span>
- <span data-ttu-id="04001-701">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-701">'Identity'</span></span>
- <span data-ttu-id="04001-702">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-702">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-703">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-703">'Razor'</span></span>
- <span data-ttu-id="04001-704">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-704">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-706">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-706">'Blazor'</span></span>
- <span data-ttu-id="04001-707">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-707">'Identity'</span></span>
- <span data-ttu-id="04001-708">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-708">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-709">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-709">'Razor'</span></span>
- <span data-ttu-id="04001-710">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-710">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-712">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-712">'Blazor'</span></span>
- <span data-ttu-id="04001-713">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-713">'Identity'</span></span>
- <span data-ttu-id="04001-714">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-714">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-715">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-715">'Razor'</span></span>
- <span data-ttu-id="04001-716">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-716">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-718">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-718">'Blazor'</span></span>
- <span data-ttu-id="04001-719">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-719">'Identity'</span></span>
- <span data-ttu-id="04001-720">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-720">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-721">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-721">'Razor'</span></span>
- <span data-ttu-id="04001-722">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-722">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-724">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-724">'Blazor'</span></span>
- <span data-ttu-id="04001-725">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-725">'Identity'</span></span>
- <span data-ttu-id="04001-726">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-726">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-727">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-727">'Razor'</span></span>
- <span data-ttu-id="04001-728">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-728">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-730">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-730">'Blazor'</span></span>
- <span data-ttu-id="04001-731">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-731">'Identity'</span></span>
- <span data-ttu-id="04001-732">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-732">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-733">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-733">'Razor'</span></span>
- <span data-ttu-id="04001-734">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-734">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-736">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-736">'Blazor'</span></span>
- <span data-ttu-id="04001-737">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-737">'Identity'</span></span>
- <span data-ttu-id="04001-738">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-738">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-739">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-739">'Razor'</span></span>
- <span data-ttu-id="04001-740">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-740">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-742">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-742">'Blazor'</span></span>
- <span data-ttu-id="04001-743">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-743">'Identity'</span></span>
- <span data-ttu-id="04001-744">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-744">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-745">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-745">'Razor'</span></span>
- <span data-ttu-id="04001-746">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-746">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-748">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-748">'Blazor'</span></span>
- <span data-ttu-id="04001-749">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-749">'Identity'</span></span>
- <span data-ttu-id="04001-750">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-750">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-751">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-751">'Razor'</span></span>
- <span data-ttu-id="04001-752">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-752">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-754">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-754">'Blazor'</span></span>
- <span data-ttu-id="04001-755">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-755">'Identity'</span></span>
- <span data-ttu-id="04001-756">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-756">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-757">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-757">'Razor'</span></span>
- <span data-ttu-id="04001-758">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-758">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-760">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-760">'Blazor'</span></span>
- <span data-ttu-id="04001-761">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-761">'Identity'</span></span>
- <span data-ttu-id="04001-762">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-762">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-763">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-763">'Razor'</span></span>
- <span data-ttu-id="04001-764">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-764">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-766">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-766">'Blazor'</span></span>
- <span data-ttu-id="04001-767">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-767">'Identity'</span></span>
- <span data-ttu-id="04001-768">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-768">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-769">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-769">'Razor'</span></span>
- <span data-ttu-id="04001-770">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-770">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-772">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-772">'Blazor'</span></span>
- <span data-ttu-id="04001-773">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-773">'Identity'</span></span>
- <span data-ttu-id="04001-774">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-774">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-775">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-775">'Razor'</span></span>
- <span data-ttu-id="04001-776">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-776">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-778">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-778">'Blazor'</span></span>
- <span data-ttu-id="04001-779">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-779">'Identity'</span></span>
- <span data-ttu-id="04001-780">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-780">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-781">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-781">'Razor'</span></span>
- <span data-ttu-id="04001-782">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-782">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-784">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-784">'Blazor'</span></span>
- <span data-ttu-id="04001-785">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-785">'Identity'</span></span>
- <span data-ttu-id="04001-786">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-786">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-787">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-787">'Razor'</span></span>
- <span data-ttu-id="04001-788">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-788">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-790">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-790">'Blazor'</span></span>
- <span data-ttu-id="04001-791">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-791">'Identity'</span></span>
- <span data-ttu-id="04001-792">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-792">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-793">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-793">'Razor'</span></span>
- <span data-ttu-id="04001-794">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-794">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-795">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-796">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-796">'Blazor'</span></span>
- <span data-ttu-id="04001-797">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-797">'Identity'</span></span>
- <span data-ttu-id="04001-798">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-798">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-799">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-799">'Razor'</span></span>
- <span data-ttu-id="04001-800">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-800">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-801">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-801">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-802">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-802">'Blazor'</span></span>
- <span data-ttu-id="04001-803">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-803">'Identity'</span></span>
- <span data-ttu-id="04001-804">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-804">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-805">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-805">'Razor'</span></span>
- <span data-ttu-id="04001-806">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-806">'SignalR' uid:</span></span> 

<span data-ttu-id="04001-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Konfigurationseintrag wurde nicht erstellt.</span><span class="sxs-lookup"><span data-stu-id="04001-807">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="04001-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="04001-808">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="04001-809">Wert: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="04001-809">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="04001-810">Wert: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="04001-810">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="04001-811">Wert: `System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="04001-811">Value: `System.Data.SqlClient`  |</span></span>

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="04001-812">JSON-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="04001-812">JSON configuration provider</span></span>

<span data-ttu-id="04001-813">Der <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> lädt die Konfiguration aus den Schlüssel-Wert-Paaren der JSON-Datei.</span><span class="sxs-lookup"><span data-stu-id="04001-813">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="04001-814">Überladungen können Folgendes angeben:</span><span class="sxs-lookup"><span data-stu-id="04001-814">Overloads can specify:</span></span>

* <span data-ttu-id="04001-815">Ob die Datei optional ist</span><span class="sxs-lookup"><span data-stu-id="04001-815">Whether the file is optional.</span></span>
* <span data-ttu-id="04001-816">Ob die Konfiguration bei Dateiänderungen neu geladen wird</span><span class="sxs-lookup"><span data-stu-id="04001-816">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="04001-817">Betrachten Sie folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="04001-817">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="04001-818">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="04001-818">The preceding code:</span></span>

* <span data-ttu-id="04001-819">Konfiguriert den JSON-Konfigurationsanbieter, sodass dieser die Datei *MyConfig.json* mit den folgenden Optionen lädt:</span><span class="sxs-lookup"><span data-stu-id="04001-819">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="04001-820">`optional: true`: Die Datei ist optional.</span><span class="sxs-lookup"><span data-stu-id="04001-820">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="04001-821">`reloadOnChange: true` : Die Datei wird erneut geladen, wenn Änderungen gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="04001-821">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="04001-822">Liest die [Standardkonfigurationsanbieter](#default) vor der Datei *MyConfig.json*.</span><span class="sxs-lookup"><span data-stu-id="04001-822">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="04001-823">Einstellungen in der Datei *MyConfig.json* überschreiben die Einstellung in den Standardkonfigurationsanbietern, einschließlich des [Umgebungsvariablen-Konfigurationsanbieters](#evcp) und des [Befehlszeilen-Konfigurationsanbieters](#clcp).</span><span class="sxs-lookup"><span data-stu-id="04001-823">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="04001-824">In der Regel möchten Sie ***nicht***, dass eine benutzerdefinierte JSON-Datei Werte überschreibt, die im [Umgebungsvariablen-Konfigurationsanbieter](#evcp) und im [Befehlszeilen-Konfigurationsanbieter](#clcp) festgelegt sind.</span><span class="sxs-lookup"><span data-stu-id="04001-824">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="04001-825">Mit dem folgenden Code werden alle Konfigurationsanbieter gelöscht und mehrere Konfigurationsanbieter hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="04001-825">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="04001-826">Im obigen Code gilt für Einstellungen in den Dateien *MyConfig.json* und *MyConfig*.`Environment`.*json*:</span><span class="sxs-lookup"><span data-stu-id="04001-826">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="04001-827">Sie überschreiben Einstellungen in den Dateien *appsettings.json* und *appsettings*.`Environment`.*json*.</span><span class="sxs-lookup"><span data-stu-id="04001-827">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="04001-828">Sie werden durch Einstellungen im [Umgebungsvariablen-Konfigurationsanbieter](#evcp) und im [Befehlszeilen-Konfigurationsanbieter](#clcp) überschrieben.</span><span class="sxs-lookup"><span data-stu-id="04001-828">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="04001-829">Der [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) enthält die folgende Datei *MyConfig.json*:</span><span class="sxs-lookup"><span data-stu-id="04001-829">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="04001-830">Im folgenden Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) sind mehrere der vorherigen Konfigurationseinstellungen zu sehen:</span><span class="sxs-lookup"><span data-stu-id="04001-830">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="04001-831">Dateikonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="04001-831">File configuration provider</span></span>

<span data-ttu-id="04001-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> ist die Basisklasse für das Laden einer Konfiguration aus dem Dateisystem.</span><span class="sxs-lookup"><span data-stu-id="04001-832"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="04001-833">Die folgenden Konfigurationsanbieter leiten sich vom `FileConfigurationProvider` ab:</span><span class="sxs-lookup"><span data-stu-id="04001-833">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="04001-834">INI-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="04001-834">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="04001-835">JSON-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="04001-835">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="04001-836">XML-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="04001-836">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="04001-837">INI-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="04001-837">INI configuration provider</span></span>

<span data-ttu-id="04001-838"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der INI-Datei.</span><span class="sxs-lookup"><span data-stu-id="04001-838">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="04001-839">Mit dem folgenden Code werden alle Konfigurationsanbieter gelöscht und mehrere Konfigurationsanbieter hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="04001-839">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="04001-840">Im obigen Code werden Einstellungen in den Dateien *MyIniConfig.ini* und *MyIniConfig*.`Environment`.*ini* überschrieben durch Einstellungen im:</span><span class="sxs-lookup"><span data-stu-id="04001-840">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="04001-841">Umgebungsvariablen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="04001-841">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="04001-842">[Befehlszeilen-Konfigurationsanbieter](#clcp)</span><span class="sxs-lookup"><span data-stu-id="04001-842">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="04001-843">Der [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) enthält die folgende Datei *MyIniConfig.ini*:</span><span class="sxs-lookup"><span data-stu-id="04001-843">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="04001-844">Im folgenden Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) sind mehrere der vorherigen Konfigurationseinstellungen zu sehen:</span><span class="sxs-lookup"><span data-stu-id="04001-844">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="04001-845">XML-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="04001-845">XML configuration provider</span></span>

<span data-ttu-id="04001-846"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der XML-Datei.</span><span class="sxs-lookup"><span data-stu-id="04001-846">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="04001-847">Mit dem folgenden Code werden alle Konfigurationsanbieter gelöscht und mehrere Konfigurationsanbieter hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="04001-847">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="04001-848">Im obigen Code werden Einstellungen in den Dateien *MyXMLFile.xml* und *MyXMLFile*.`Environment`.*xml* überschrieben durch Einstellungen im:</span><span class="sxs-lookup"><span data-stu-id="04001-848">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="04001-849">Umgebungsvariablen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="04001-849">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="04001-850">[Befehlszeilen-Konfigurationsanbieter](#clcp)</span><span class="sxs-lookup"><span data-stu-id="04001-850">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="04001-851">Der [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) enthält die folgende Datei *MyXMLFile.xml*:</span><span class="sxs-lookup"><span data-stu-id="04001-851">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="04001-852">Im folgenden Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) sind mehrere der vorherigen Konfigurationseinstellungen zu sehen:</span><span class="sxs-lookup"><span data-stu-id="04001-852">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="04001-853">Wiederholte Elemente mit den gleichen Elementnamen funktionieren, wenn das `name`-Attribut zur Unterscheidung der Elemente verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="04001-853">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="04001-854">Der folgende Code liest die vorherige Konfigurationsdatei und zeigt die Schlüssel und Werte an:</span><span class="sxs-lookup"><span data-stu-id="04001-854">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="04001-855">Mit Attributen können Werte bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="04001-855">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="04001-856">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="04001-856">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="04001-857">key:attribute</span><span class="sxs-lookup"><span data-stu-id="04001-857">key:attribute</span></span>
* <span data-ttu-id="04001-858">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="04001-858">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="04001-859">Schlüssel-pro-Datei-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="04001-859">Key-per-file configuration provider</span></span>

<span data-ttu-id="04001-860"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> verwendet Verzeichnisdateien als Konfigurations-Schlüssel-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="04001-860">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="04001-861">Der Schlüssel ist der Dateiname.</span><span class="sxs-lookup"><span data-stu-id="04001-861">The key is the file name.</span></span> <span data-ttu-id="04001-862">Der Wert enthält den Inhalt der Datei.</span><span class="sxs-lookup"><span data-stu-id="04001-862">The value contains the file's contents.</span></span> <span data-ttu-id="04001-863">Der Schlüssel-pro-Datei-Konfigurationsanbieter wird in Docker-Hostingszenarios verwendet.</span><span class="sxs-lookup"><span data-stu-id="04001-863">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="04001-864">Um die Schlüssel-pro-Datei-Konfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="04001-864">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="04001-865">Der `directoryPath` zu den Dateien muss ein absoluter Pfad sein.</span><span class="sxs-lookup"><span data-stu-id="04001-865">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="04001-866">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="04001-866">Overloads permit specifying:</span></span>

* <span data-ttu-id="04001-867">Einen `Action<KeyPerFileConfigurationSource>`-Delegat, der die Quelle konfiguriert</span><span class="sxs-lookup"><span data-stu-id="04001-867">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="04001-868">Ob das Verzeichnis optional ist; und den Pfad zum Verzeichnis</span><span class="sxs-lookup"><span data-stu-id="04001-868">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="04001-869">Der doppelte Unterstrich (`__`) wird als Trennzeichen für Konfigurationsschlüssel in Dateinamen verwendet.</span><span class="sxs-lookup"><span data-stu-id="04001-869">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="04001-870">Der Dateiname `Logging__LogLevel__System` erzeugt z.B. den Konfigurationsschlüssel `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="04001-870">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="04001-871">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="04001-871">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="04001-872">Speicherkonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="04001-872">Memory configuration provider</span></span>

<span data-ttu-id="04001-873"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> verwendet eine In-Memory-Sammlung für Konfigurations-Schlüssel-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="04001-873">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="04001-874">Der folgende Code fügt eine Arbeitsspeichersammlung zum Konfigurationssystem hinzu:</span><span class="sxs-lookup"><span data-stu-id="04001-874">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="04001-875">Der folgende Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zeigt die vorherigen Konfigurationseinstellungen an:</span><span class="sxs-lookup"><span data-stu-id="04001-875">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="04001-876">Im obigen Code wird `config.AddInMemoryCollection(Dict)` nach den [Standardkonfigurationsanbietern](#default) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="04001-876">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="04001-877">Ein Beispiel für das Festlegen der Reihenfolge der Konfigurationsanbieter finden Sie unter [JSON-Konfigurationsanbieter](#jcp).</span><span class="sxs-lookup"><span data-stu-id="04001-877">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="04001-878">Ein weiteres Beispiel für die Verwendung von `MemoryConfigurationProvider` finden Sie unter [Binden eines Arrays](#boa).</span><span class="sxs-lookup"><span data-stu-id="04001-878">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="04001-879">GetValue</span><span class="sxs-lookup"><span data-stu-id="04001-879">GetValue</span></span>

<span data-ttu-id="04001-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahiert einen Einzelwert aus der Konfiguration mit einem angegebenen Schlüssel und konvertiert ihn in den angegebenen Typ:</span><span class="sxs-lookup"><span data-stu-id="04001-880">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="04001-881">Wenn im obigen Code `NumberKey` nicht in der Konfiguration gefunden wird, wird der Standardwert `99` verwendet.</span><span class="sxs-lookup"><span data-stu-id="04001-881">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="04001-882">GetSection, GetChildren und Exists</span><span class="sxs-lookup"><span data-stu-id="04001-882">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="04001-883">Beachten Sie für die nachstehenden Beispiele die folgende Datei *MySubsection.json*:</span><span class="sxs-lookup"><span data-stu-id="04001-883">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="04001-884">Der folgende Code fügt *MySubsection.json* zu den Konfigurationsanbietern hinzu:</span><span class="sxs-lookup"><span data-stu-id="04001-884">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="04001-885">GetSection</span><span class="sxs-lookup"><span data-stu-id="04001-885">GetSection</span></span>

<span data-ttu-id="04001-886">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) gibt einen Konfigurationsunterabschnitt mit dem angegebenen Unterabschnittsschlüssel zurück.</span><span class="sxs-lookup"><span data-stu-id="04001-886">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="04001-887">Der folgende Code gibt Werte für `section1` zurück:</span><span class="sxs-lookup"><span data-stu-id="04001-887">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="04001-888">Der folgende Code gibt Werte für `section2:subsection0` zurück:</span><span class="sxs-lookup"><span data-stu-id="04001-888">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="04001-889">`GetSection` gibt nie `null` zurück.</span><span class="sxs-lookup"><span data-stu-id="04001-889">`GetSection` never returns `null`.</span></span> <span data-ttu-id="04001-890">Wenn kein entsprechender Abschnitt gefunden wird, wird ein leeres `IConfigurationSection`-Element zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="04001-890">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="04001-891">Wenn `GetSection` einen entsprechenden Abschnitt zurückgibt, wird <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> nicht aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="04001-891">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="04001-892">Eine Eigenschaft <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> und <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> werden zurückgegeben, wenn der Abschnitt vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="04001-892">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="04001-893">GetChildren und Exists</span><span class="sxs-lookup"><span data-stu-id="04001-893">GetChildren and Exists</span></span>

<span data-ttu-id="04001-894">Mit dem folgenden Code wird [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) abgerufen, und es werden Werte für `section2:subsection0` zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="04001-894">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="04001-895">Der obige Code ruft [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) auf, um zu überprüfen, ob der Abschnitt vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="04001-895">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="04001-896">Binden eines Arrays</span><span class="sxs-lookup"><span data-stu-id="04001-896">Bind an array</span></span>

<span data-ttu-id="04001-897">[ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) unterstützt das Binden von Arrays an Objekte mithilfe von Arrayindizes in Konfigurationsschlüsseln.</span><span class="sxs-lookup"><span data-stu-id="04001-897">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="04001-898">Jedes Arrayformat, das ein numerisches Schlüsselsegment verfügbar macht, kann ein Array an ein [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object)-Klassenarray binden.</span><span class="sxs-lookup"><span data-stu-id="04001-898">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="04001-899">Beachten Sie *MyArray.json* aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="04001-899">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="04001-900">Der folgende Code fügt *MyArray.json* zu den Konfigurationsanbietern hinzu:</span><span class="sxs-lookup"><span data-stu-id="04001-900">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="04001-901">Der folgende Code liest die Konfiguration und zeigt die Werte an:</span><span class="sxs-lookup"><span data-stu-id="04001-901">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="04001-902">Der obige Code erzeugt die folgende Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="04001-902">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="04001-903">In der obigen Ausgabe hat Index 3 den Wert `value40`, der `"4": "value40",` in *MyArray.json* entspricht.</span><span class="sxs-lookup"><span data-stu-id="04001-903">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="04001-904">Die gebundenen Arrayindizes sind kontinuierlich und nicht an den Konfigurationsschlüsselindex gebunden.</span><span class="sxs-lookup"><span data-stu-id="04001-904">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="04001-905">Der Konfigurationsbinder ist nicht in der Lage, NULL-Werte zu binden oder NULL-Einträge in gebundenen Objekten zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="04001-905">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="04001-906">Mit dem folgenden Code wird die `array:entries`-Konfiguration mit der <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*>-Erweiterungsmethode geladen:</span><span class="sxs-lookup"><span data-stu-id="04001-906">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="04001-907">Der folgende Code liest die Konfiguration im `arrayDict` `Dictionary` und zeigt die Werte an:</span><span class="sxs-lookup"><span data-stu-id="04001-907">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="04001-908">Der obige Code erzeugt die folgende Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="04001-908">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="04001-909">Index &num;3 im gebundenen Objekt enthält die Konfigurationsdaten für den `array:4`-Konfigurationsschlüssel und die Wert für `value4`.</span><span class="sxs-lookup"><span data-stu-id="04001-909">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="04001-910">Beim Binden von Konfigurationsdaten, die ein Array enthalten, werden die Arrayindizes in den Konfigurationsschlüsseln zum Durchlaufen der Konfigurationsdaten beim Erstellen des Objekts verwendet.</span><span class="sxs-lookup"><span data-stu-id="04001-910">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="04001-911">Ein NULL-Wert kann in den Konfigurationsdaten nicht beibehalten werden, und ein NULL-Eintrag wird nicht in einem gebundenen Objekt erstellt, wenn ein Array in Konfigurationsschlüsseln mindestens einen Index überspringt.</span><span class="sxs-lookup"><span data-stu-id="04001-911">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="04001-912">Das fehlende Konfigurationselement für Index &num;3 kann vor dem Binden an die `ArrayExample`-Instanz von jedem Konfigurationsanbieter bereitgestellt werden, der das Schlüssel-Wert-Paar von Index &num;3 liest.</span><span class="sxs-lookup"><span data-stu-id="04001-912">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="04001-913">Beachten Sie die folgende Datei *Value3.json* aus dem Beispieldownload:</span><span class="sxs-lookup"><span data-stu-id="04001-913">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="04001-914">Der folgende Code enthält die Konfiguration für *Value3.json* und das `arrayDict` `Dictionary`:</span><span class="sxs-lookup"><span data-stu-id="04001-914">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="04001-915">Der folgende Code liest die obige Konfiguration und zeigt die Werte an:</span><span class="sxs-lookup"><span data-stu-id="04001-915">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="04001-916">Der obige Code erzeugt die folgende Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="04001-916">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="04001-917">Benutzerdefinierte Konfigurationsanbieter sind nicht erforderlich, um Arraybindung zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="04001-917">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="04001-918">Benutzerdefinierter Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="04001-918">Custom configuration provider</span></span>

<span data-ttu-id="04001-919">Die Beispiel-App veranschaulicht, wie ein Standardkonfigurationsanbieter erstellt wird, der Konfigurations-Schlüssel-Wert-Paare aus einer Datenbank mit [Entity Framework (EF)](/ef/core/) liest.</span><span class="sxs-lookup"><span data-stu-id="04001-919">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="04001-920">Der Anbieter weist die folgenden Merkmale auf:</span><span class="sxs-lookup"><span data-stu-id="04001-920">The provider has the following characteristics:</span></span>

* <span data-ttu-id="04001-921">Die EF-In-Memory-Datenbank wird zu Demonstrationszwecken verwendet.</span><span class="sxs-lookup"><span data-stu-id="04001-921">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="04001-922">Um eine Datenbank zu verwenden, die eine Verbindungszeichenfolge benötigt, implementieren Sie einen sekundären `ConfigurationBuilder`, um die Verbindungszeichenfolge aus einem anderen Konfigurationsanbieter anzugeben.</span><span class="sxs-lookup"><span data-stu-id="04001-922">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="04001-923">Der Anbieter liest eine Datenbanktabelle beim Start in die Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="04001-923">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="04001-924">Der Anbieter fragt die Datenbank nicht pro Schlüssel ab.</span><span class="sxs-lookup"><span data-stu-id="04001-924">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="04001-925">Das erneute Laden bei Änderung ist nicht implementiert. Das heißt, das Aktualisieren der Datenbank nach App-Start hat keine Auswirkungen auf die App-Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="04001-925">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="04001-926">Definieren Sie eine `EFConfigurationValue`-Entität zum Speichern von Konfigurationswerten in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="04001-926">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="04001-927">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="04001-927">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="04001-928">Fügen Sie `EFConfigurationContext` hinzu, um die konfigurierten Werte zu speichern und auf diese zugreifen.</span><span class="sxs-lookup"><span data-stu-id="04001-928">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="04001-929">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="04001-929">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="04001-930">Erstellen Sie eine Klasse, die das <xref:Microsoft.Extensions.Configuration.IConfigurationSource> implementiert.</span><span class="sxs-lookup"><span data-stu-id="04001-930">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="04001-931">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="04001-931">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="04001-932">Erstellen Sie den benutzerdefinierten Konfigurationsanbieter durch Vererbung von <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="04001-932">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="04001-933">Der Konfigurationsanbieter initialisiert die Datenbank, wenn diese leer ist.</span><span class="sxs-lookup"><span data-stu-id="04001-933">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="04001-934">Da [Konfigurationsschlüssel die Groß-/Kleinschreibung nicht beachten](#keys), wird das zum Initialisieren der Datenbank verwendete Wörterbuch mit der Vergleichsfunktion, die die Groß-/Kleinschreibung nicht beachtet, erstellt ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="04001-934">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="04001-935">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="04001-935">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="04001-936">Mit einer `AddEFConfiguration`-Erweiterungsmethode kann die Konfigurationsquelle `ConfigurationBuilder` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="04001-936">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="04001-937">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="04001-937">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="04001-938">Der folgende Code veranschaulicht die Verwendung des benutzerdefinierten Anbieters `EFConfigurationProvider` in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="04001-938">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="04001-939">Zugriffskonfiguration beim Start</span><span class="sxs-lookup"><span data-stu-id="04001-939">Access configuration in Startup</span></span>

<span data-ttu-id="04001-940">Der folgende Code zeigt Konfigurationsdaten in `Startup`-Methoden an:</span><span class="sxs-lookup"><span data-stu-id="04001-940">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="04001-941">Ein Beispiel für den Zugriff auf die Konfiguration mit den Starthilfsmethoden finden Sie unter [Anwendungsstart: Hilfsmethoden](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="04001-941">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-razor-pages"></a><span data-ttu-id="04001-942">Zugriffskonfiguration in Razor Pages</span><span class="sxs-lookup"><span data-stu-id="04001-942">Access configuration in Razor Pages</span></span>

<span data-ttu-id="04001-943">Der folgende Code zeigt Konfigurationsdaten auf einer Razor-Seite an:</span><span class="sxs-lookup"><span data-stu-id="04001-943">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="04001-944">Im folgenden Code wird `MyOptions` mit <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> zum Dienstcontainer hinzugefügt und an die Konfiguration gebunden:</span><span class="sxs-lookup"><span data-stu-id="04001-944">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="04001-945">Das folgende Markup verwendet die [`@inject`](xref:mvc/views/razor#inject) Razor Direktive, um die Optionswerte aufzulösen und anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="04001-945">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="04001-946">Zugriffskonfiguration in einer MVC-Ansichtsdatei</span><span class="sxs-lookup"><span data-stu-id="04001-946">Access configuration in a MVC view file</span></span>

<span data-ttu-id="04001-947">Der folgende Code zeigt Konfigurationsdaten in einer MVC-Ansicht an:</span><span class="sxs-lookup"><span data-stu-id="04001-947">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="04001-948">Konfigurieren von Optionen mit einem Delegaten</span><span class="sxs-lookup"><span data-stu-id="04001-948">Configure options with a delegate</span></span>

<span data-ttu-id="04001-949">In einem Delegaten konfigurierte Optionen überschreiben die in den Konfigurationsanbietern festgelegten Werte.</span><span class="sxs-lookup"><span data-stu-id="04001-949">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="04001-950">Das Konfigurieren von Optionen mit einem Delegaten wird als Beispiel 2 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="04001-950">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="04001-951">Im folgenden Code wird ein dritter <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Dienst zum Dienstcontainer hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="04001-951">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="04001-952">Er verwendet einen Delegaten, um Werte für `MyOptions` zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="04001-952">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="04001-953">Im folgenden Code werden die Optionswerte angezeigt:</span><span class="sxs-lookup"><span data-stu-id="04001-953">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="04001-954">Im vorherigen Beispiel wurden die Werte von `Option1` und `Option2` in *appsettings.json* angegeben und anschließend vom konfigurierten Delegaten überschrieben.</span><span class="sxs-lookup"><span data-stu-id="04001-954">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="04001-955">Hostkonfiguration und App-Konfiguration im Vergleich</span><span class="sxs-lookup"><span data-stu-id="04001-955">Host versus app configuration</span></span>

<span data-ttu-id="04001-956">Bevor die App konfiguriert und gestartet wird, wird ein *Host* konfiguriert und gestartet.</span><span class="sxs-lookup"><span data-stu-id="04001-956">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="04001-957">Der Host ist verantwortlich für das Starten der App und das Verwalten der Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="04001-957">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="04001-958">Die App und der Host werden mit den in diesem Thema beschriebenen Konfigurationsanbietern konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="04001-958">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="04001-959">Schlüssel-Wert-Paare der Hostkonfiguration sind ebenfalls in der globalen App-Konfiguration enthalten.</span><span class="sxs-lookup"><span data-stu-id="04001-959">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="04001-960">Weitere Informationen dazu, wie Konfigurationsanbieter beim Erstellen des Hosts verwendet werden, und wie sich Konfigurationsquellen auf die Hostkonfiguration auswirken, finden Sie unter <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="04001-960">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="04001-961">Standardhostkonfiguration</span><span class="sxs-lookup"><span data-stu-id="04001-961">Default host configuration</span></span>

<span data-ttu-id="04001-962">Ausführliche Informationen zur Standardkonfiguration bei Verwendung des [Webhosts](xref:fundamentals/host/web-host) finden Sie in der [ASP.NET Core 2.2-Version dieses Themas](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="04001-962">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="04001-963">Die Konfiguration des Hosts wird durch Folgendes festgelegt:</span><span class="sxs-lookup"><span data-stu-id="04001-963">Host configuration is provided from:</span></span>
  * <span data-ttu-id="04001-964">Umgebungsvariablen mit dem Präfix `DOTNET_` (z. B. `DOTNET_ENVIRONMENT`), die den [Umgebungsvariablen-Konfigurationsanbieter](#environment-variables-configuration-provider) verwenden.</span><span class="sxs-lookup"><span data-stu-id="04001-964">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="04001-965">Das Präfix (`DOTNET_`) wird beim Laden der Schlüssel-Wert-Paare der Konfiguration entfernt.</span><span class="sxs-lookup"><span data-stu-id="04001-965">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="04001-966">Befehlszeilenargumente, die den [Befehlszeilen-Konfigurationsanbieter](#command-line-configuration-provider) verwenden</span><span class="sxs-lookup"><span data-stu-id="04001-966">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="04001-967">Die Webhost-Standardkonfiguration wird eingerichtet (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="04001-967">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="04001-968">Als Webserver wird Kestrel verwendet und mithilfe der Konfigurationsanbieter der App konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="04001-968">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="04001-969">Fügen Sie Middleware zum Filtern von Hosts hinzu.</span><span class="sxs-lookup"><span data-stu-id="04001-969">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="04001-970">Fügen Sie Middleware für weitergeleitete Header hinzu, wenn die Umgebungsvariable `ASPNETCORE_FORWARDEDHEADERS_ENABLED` auf `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="04001-970">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="04001-971">Aktivieren Sie die IIS-Integration.</span><span class="sxs-lookup"><span data-stu-id="04001-971">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="04001-972">Sonstige Konfiguration</span><span class="sxs-lookup"><span data-stu-id="04001-972">Other configuration</span></span>

<span data-ttu-id="04001-973">Dieses Thema bezieht sich nur auf *App-Konfigurationen*.</span><span class="sxs-lookup"><span data-stu-id="04001-973">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="04001-974">Andere Aspekte des Ausführens und Hostings von ASP.NET Core-Apps werden mithilfe von Konfigurationsdateien konfiguriert, die in diesem Thema nicht behandelt werden:</span><span class="sxs-lookup"><span data-stu-id="04001-974">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="04001-975">*launch.json*/*launchSettings.json* sind Toolkonfigurationsdateien für die Entwicklungsumgebung, die hier beschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="04001-975">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="04001-976">In <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="04001-976">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="04001-977">In der Dokumentationsreihe, wo die Dateien zum Konfigurieren von ASP.NET Core-Apps für Entwicklungsszenarien verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="04001-977">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="04001-978">*web.config* ist eine Serverkonfigurationsdatei, die in den folgenden Themen beschrieben wird:</span><span class="sxs-lookup"><span data-stu-id="04001-978">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="04001-979">Weitere Informationen zum Migrieren der App-Konfiguration aus früheren Versionen von ASP.NET finden Sie unter <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="04001-979">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="04001-980">Hinzufügen von Konfigurationen aus einer externen Assembly</span><span class="sxs-lookup"><span data-stu-id="04001-980">Add configuration from an external assembly</span></span>

<span data-ttu-id="04001-981">Eine <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>-Implementierung ermöglicht das Hinzufügen von Erweiterungen zu einer App beim Start von einer externen Assembly außerhalb der `Startup`-Klasse der App aus.</span><span class="sxs-lookup"><span data-stu-id="04001-981">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="04001-982">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="04001-982">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="04001-983">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="04001-983">Additional resources</span></span>

* [<span data-ttu-id="04001-984">Quellcode der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="04001-984">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="04001-985">Die App-Konfiguration in ASP.NET Core basiert auf Schlüssel-Wert-Paaren, die von *Konfigurationsanbietern* erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="04001-985">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="04001-986">Konfigurationsanbieter lesen Konfigurationsdaten in Schlüssel-Wert-Paare aus verschiedenen Konfigurationsquellen:</span><span class="sxs-lookup"><span data-stu-id="04001-986">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="04001-987">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="04001-987">Azure Key Vault</span></span>
* <span data-ttu-id="04001-988">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="04001-988">Azure App Configuration</span></span>
* <span data-ttu-id="04001-989">Befehlszeilenargumente</span><span class="sxs-lookup"><span data-stu-id="04001-989">Command-line arguments</span></span>
* <span data-ttu-id="04001-990">Benutzerdefinierte Anbieter (installiert oder erstellt)</span><span class="sxs-lookup"><span data-stu-id="04001-990">Custom providers (installed or created)</span></span>
* <span data-ttu-id="04001-991">Verzeichnisdateien</span><span class="sxs-lookup"><span data-stu-id="04001-991">Directory files</span></span>
* <span data-ttu-id="04001-992">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="04001-992">Environment variables</span></span>
* <span data-ttu-id="04001-993">Speicherinterne .NET Objekte</span><span class="sxs-lookup"><span data-stu-id="04001-993">In-memory .NET objects</span></span>
* <span data-ttu-id="04001-994">Einstellungsdateien</span><span class="sxs-lookup"><span data-stu-id="04001-994">Settings files</span></span>

<span data-ttu-id="04001-995">Konfigurationspakete für gängige Konfigurationsanbieterszenarien ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) sind im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="04001-995">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="04001-996">Codebeispiele, die den <xref:Microsoft.Extensions.Configuration>-Namespace befolgen und in der Beispiel-App verwenden:</span><span class="sxs-lookup"><span data-stu-id="04001-996">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="04001-997">Das *Optionsmuster* ist eine Erweiterung der in diesem Thema beschriebenen Konfigurationskonzepte.</span><span class="sxs-lookup"><span data-stu-id="04001-997">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="04001-998">Optionen verwenden Klassen, um Gruppen von zusammengehörigen Einstellungen darzustellen.</span><span class="sxs-lookup"><span data-stu-id="04001-998">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="04001-999">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="04001-999">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="04001-1000">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="04001-1000">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="04001-1001">Hostkonfiguration und App-Konfiguration im Vergleich</span><span class="sxs-lookup"><span data-stu-id="04001-1001">Host versus app configuration</span></span>

<span data-ttu-id="04001-1002">Bevor die App konfiguriert und gestartet wird, wird ein *Host* konfiguriert und gestartet.</span><span class="sxs-lookup"><span data-stu-id="04001-1002">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="04001-1003">Der Host ist verantwortlich für das Starten der App und das Verwalten der Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="04001-1003">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="04001-1004">Die App und der Host werden mit den in diesem Thema beschriebenen Konfigurationsanbietern konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="04001-1004">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="04001-1005">Schlüssel-Wert-Paare der Hostkonfiguration sind ebenfalls in der globalen App-Konfiguration enthalten.</span><span class="sxs-lookup"><span data-stu-id="04001-1005">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="04001-1006">Weitere Informationen dazu, wie Konfigurationsanbieter beim Erstellen des Hosts verwendet werden, und wie sich Konfigurationsquellen auf die Hostkonfiguration auswirken, finden Sie unter <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="04001-1006">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="04001-1007">Sonstige Konfiguration</span><span class="sxs-lookup"><span data-stu-id="04001-1007">Other configuration</span></span>

<span data-ttu-id="04001-1008">Dieses Thema bezieht sich nur auf *App-Konfigurationen*.</span><span class="sxs-lookup"><span data-stu-id="04001-1008">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="04001-1009">Andere Aspekte des Ausführens und Hostings von ASP.NET Core-Apps werden mithilfe von Konfigurationsdateien konfiguriert, die in diesem Thema nicht behandelt werden:</span><span class="sxs-lookup"><span data-stu-id="04001-1009">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="04001-1010">*launch.json*/*launchSettings.json* sind Toolkonfigurationsdateien für die Entwicklungsumgebung, die hier beschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="04001-1010">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="04001-1011">In <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="04001-1011">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="04001-1012">In der Dokumentationsreihe, wo die Dateien zum Konfigurieren von ASP.NET Core-Apps für Entwicklungsszenarien verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="04001-1012">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="04001-1013">*web.config* ist eine Serverkonfigurationsdatei, die in den folgenden Themen beschrieben wird:</span><span class="sxs-lookup"><span data-stu-id="04001-1013">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="04001-1014">Weitere Informationen zum Migrieren der App-Konfiguration aus früheren Versionen von ASP.NET finden Sie unter <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="04001-1014">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="04001-1015">Standardkonfiguration</span><span class="sxs-lookup"><span data-stu-id="04001-1015">Default configuration</span></span>

<span data-ttu-id="04001-1016">Web-Apps, die auf den [dotnet new](/dotnet/core/tools/dotnet-new)-Vorlagen von ASP.NET Core basieren, rufen beim Erstellen eines Hosts <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> auf.</span><span class="sxs-lookup"><span data-stu-id="04001-1016">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="04001-1017">`CreateDefaultBuilder` legt die Standardkonfiguration für die App in der folgenden Reihenfolge fest:</span><span class="sxs-lookup"><span data-stu-id="04001-1017">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="04001-1018">Folgendes gilt für Apps, die den [Webhost](xref:fundamentals/host/web-host) verwenden.</span><span class="sxs-lookup"><span data-stu-id="04001-1018">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="04001-1019">Ausführliche Informationen zur Standardkonfiguration bei Verwendung des [generischen Hosts](xref:fundamentals/host/generic-host) finden Sie in der [neuesten Version dieses Themas](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="04001-1019">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="04001-1020">Die Konfiguration des Hosts wird durch Folgendes festgelegt:</span><span class="sxs-lookup"><span data-stu-id="04001-1020">Host configuration is provided from:</span></span>
  * <span data-ttu-id="04001-1021">Umgebungsvariablen mit dem Präfix `ASPNETCORE_` (zum Beispiel `ASPNETCORE_ENVIRONMENT`) mithilfe des [Umgebungsvariablen-Konfigurationsanbieters](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="04001-1021">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="04001-1022">Das Präfix (`ASPNETCORE_`) wird beim Laden der Schlüssel-Wert-Paare der Konfiguration entfernt.</span><span class="sxs-lookup"><span data-stu-id="04001-1022">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="04001-1023">Befehlszeilenargumente mithilfe des [Befehlszeilen-Konfigurationsanbieters](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="04001-1023">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="04001-1024">Die App-Konfiguration wird durch Folgendes festgelegt:</span><span class="sxs-lookup"><span data-stu-id="04001-1024">App configuration is provided from:</span></span>
  * <span data-ttu-id="04001-1025">*appsettings.json* mithilfe des [Dateikonfigurationsanbieters](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="04001-1025">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="04001-1026">*appsettings.{Umgebung}.json* mithilfe des [Dateikonfigurationsanbieters](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="04001-1026">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="04001-1027">[Geheimnis-Manager](xref:security/app-secrets), wenn die App in der `Development`-Umgebung mit der Einstiegsassembly ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="04001-1027">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="04001-1028">Umgebungsvariablen mithilfe des [Umgebungsvariablen-Konfigurationsanbieters](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="04001-1028">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="04001-1029">Befehlszeilenargumente mithilfe des [Befehlszeilen-Konfigurationsanbieters](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="04001-1029">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="04001-1030">Sicherheit</span><span class="sxs-lookup"><span data-stu-id="04001-1030">Security</span></span>

<span data-ttu-id="04001-1031">Wenden Sie die folgenden Verfahren an, um vertrauliche Konfigurationsdaten zu schützen:</span><span class="sxs-lookup"><span data-stu-id="04001-1031">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="04001-1032">Speichern Sie nie Kennwörter oder andere vertrauliche Daten im Konfigurationsanbietercode oder in Nur-Text-Konfigurationsdateien.</span><span class="sxs-lookup"><span data-stu-id="04001-1032">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="04001-1033">Verwenden Sie keine Produktionsgeheimnisse in Entwicklungs- oder Testumgebungen.</span><span class="sxs-lookup"><span data-stu-id="04001-1033">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="04001-1034">Geben Sie Geheimnisse außerhalb des Projekts an, damit sie nicht versehentlich in ein Quellcoderepository übernommen werden können.</span><span class="sxs-lookup"><span data-stu-id="04001-1034">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="04001-1035">Weitere Informationen finden Sie unter den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="04001-1035">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="04001-1036"><xref:security/app-secrets>: Enthält Hinweise zur Verwendung von Umgebungsvariablen für das Speichern vertraulicher Daten.</span><span class="sxs-lookup"><span data-stu-id="04001-1036"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="04001-1037">Der Geheimnis-Manager verwendet den Dateikonfigurationsanbieter, um Benutzergeheimnisse in einer JSON-Datei im lokalen System zu speichern.</span><span class="sxs-lookup"><span data-stu-id="04001-1037">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="04001-1038">Der Dateikonfigurationsanbieter wird später in diesem Thema beschrieben.</span><span class="sxs-lookup"><span data-stu-id="04001-1038">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="04001-1039">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) speichert App-Geheimnisse für ASP.NET Core-Apps auf sichere Weise.</span><span class="sxs-lookup"><span data-stu-id="04001-1039">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="04001-1040">Weitere Informationen finden Sie unter <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="04001-1040">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="04001-1041">Hierarchische Konfigurationsdaten</span><span class="sxs-lookup"><span data-stu-id="04001-1041">Hierarchical configuration data</span></span>

<span data-ttu-id="04001-1042">Die Konfigurations-API kann hierarchische Konfigurationsdaten erhalten, indem sie die hierarchischen Daten mit einem Trennzeichen in den Konfigurationsschlüsseln vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="04001-1042">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="04001-1043">Die folgende JSON-Datei enthält vier Schlüssel in einer strukturierten Hierarchie von zwei Abschnitten:</span><span class="sxs-lookup"><span data-stu-id="04001-1043">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

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

<span data-ttu-id="04001-1044">Wenn die Datei in die Konfiguration gelesen wird, werden eindeutige Schlüssel erstellt, um die ursprüngliche hierarchische Datenstruktur der Konfigurationsquelle zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="04001-1044">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="04001-1045">Die Abschnitte und Schlüssel werden mithilfe eines Doppelpunkts (`:`) vereinfacht, um die Originalstruktur zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="04001-1045">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="04001-1046">section0:key0</span><span class="sxs-lookup"><span data-stu-id="04001-1046">section0:key0</span></span>
* <span data-ttu-id="04001-1047">section0:key1</span><span class="sxs-lookup"><span data-stu-id="04001-1047">section0:key1</span></span>
* <span data-ttu-id="04001-1048">section1:key0</span><span class="sxs-lookup"><span data-stu-id="04001-1048">section1:key0</span></span>
* <span data-ttu-id="04001-1049">section1:key1</span><span class="sxs-lookup"><span data-stu-id="04001-1049">section1:key1</span></span>

<span data-ttu-id="04001-1050">Mit den Methoden <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> und <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> können Abschnitte und untergeordnete Abschnittelemente in den Konfigurationsdaten isoliert werden.</span><span class="sxs-lookup"><span data-stu-id="04001-1050"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="04001-1051">Diese Methoden werden später im Abschnitt [GetSection, GetChildren und Exists](#getsection-getchildren-and-exists) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="04001-1051">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="04001-1052">Konventionen</span><span class="sxs-lookup"><span data-stu-id="04001-1052">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="04001-1053">Quellen und Anbieter</span><span class="sxs-lookup"><span data-stu-id="04001-1053">Sources and providers</span></span>

<span data-ttu-id="04001-1054">Beim Starten der Anwendung werden Konfigurationsquellen in der Reihenfolge gelesen, in der ihre Konfigurationsanbieter angegeben sind.</span><span class="sxs-lookup"><span data-stu-id="04001-1054">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="04001-1055">Konfigurationsanbieter, die Änderungserkennung implementieren, können Konfigurationen erneut laden, wenn zugrunde liegende Einstellungen geändert werden.</span><span class="sxs-lookup"><span data-stu-id="04001-1055">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="04001-1056">Der Dateikonfigurationsanbieter (weiter unten in diesem Thema beschrieben) und der [Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration) implementieren beispielsweise die Änderungserkennung.</span><span class="sxs-lookup"><span data-stu-id="04001-1056">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="04001-1057"><xref:Microsoft.Extensions.Configuration.IConfiguration> steht im App-Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="04001-1057"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="04001-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration> kann in <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> von Razor Pages oder <xref:Microsoft.AspNetCore.Mvc.Controller> von MVC eingefügt werden, um die Konfiguration für die Klasse abzurufen.</span><span class="sxs-lookup"><span data-stu-id="04001-1058"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="04001-1059">In den folgenden Beispielen wird das Feld `_config` verwendet, um auf Konfigurationswerte zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="04001-1059">In the following examples, the `_config` field is used to access configuration values:</span></span>

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

<span data-ttu-id="04001-1060">Konfigurationsanbieter können die Abhängigkeitsinjektion nicht verwenden, weil sie nicht verfügbar ist, wenn sie vom Host eingerichtet werden.</span><span class="sxs-lookup"><span data-stu-id="04001-1060">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="04001-1061">Tasten</span><span class="sxs-lookup"><span data-stu-id="04001-1061">Keys</span></span>

<span data-ttu-id="04001-1062">Konfigurationsschlüssel entsprechen den folgenden Konventionen:</span><span class="sxs-lookup"><span data-stu-id="04001-1062">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="04001-1063">Bei Schlüsseln wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="04001-1063">Keys are case-insensitive.</span></span> <span data-ttu-id="04001-1064">Beispielsweise verweisen `ConnectionString` und `connectionstring` auf denselben Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="04001-1064">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="04001-1065">Wenn ein Wert für denselben Schlüssel von denselben oder unterschiedlichen Konfigurationsanbietern festgelegt wird, wird der zuletzt für den Schlüssel bestimmte Wert verwendet.</span><span class="sxs-lookup"><span data-stu-id="04001-1065">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span>
* <span data-ttu-id="04001-1066">Hierarchische Schlüssel</span><span class="sxs-lookup"><span data-stu-id="04001-1066">Hierarchical keys</span></span>
  * <span data-ttu-id="04001-1067">Innerhalb der Konfigurations-API funktioniert ein Doppelpunkt (`:`) als Trennzeichen auf allen Plattformen.</span><span class="sxs-lookup"><span data-stu-id="04001-1067">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="04001-1068">In Umgebungsvariablen funktioniert ein Doppelpunkt als Trennzeichen ggf. nicht auf allen Plattformen.</span><span class="sxs-lookup"><span data-stu-id="04001-1068">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="04001-1069">Ein doppelter Unterstrich (`__`) wird auf allen Plattformen unterstützt und automatisch in einen Doppelpunkt konvertiert.</span><span class="sxs-lookup"><span data-stu-id="04001-1069">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="04001-1070">In Azure Key Vault verwenden hierarchische Schlüssel zwei Bindestriche (`--`) als Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="04001-1070">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="04001-1071">Schreiben Sie Code, um die Bindestriche durch einen Doppelpunkt zu ersetzen, wenn die Geheimnisse in die App-Konfiguration geladen werden.</span><span class="sxs-lookup"><span data-stu-id="04001-1071">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="04001-1072"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> unterstützt das Binden von Arrays an Objekte mit Arrayindizes in Konfigurationsschlüsseln.</span><span class="sxs-lookup"><span data-stu-id="04001-1072">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="04001-1073">Die Arraybindung wird im Abschnitt [Binden eines Arrays an eine Klasse](#bind-an-array-to-a-class) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="04001-1073">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="04001-1074">Werte</span><span class="sxs-lookup"><span data-stu-id="04001-1074">Values</span></span>

<span data-ttu-id="04001-1075">Konfigurationswerte entsprechen den folgenden Konventionen:</span><span class="sxs-lookup"><span data-stu-id="04001-1075">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="04001-1076">Die Werte sind Zeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="04001-1076">Values are strings.</span></span>
* <span data-ttu-id="04001-1077">NULL-Werte können nicht in einer Konfiguration gespeichert oder an Objekte gebunden werden.</span><span class="sxs-lookup"><span data-stu-id="04001-1077">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="04001-1078">Anbieter</span><span class="sxs-lookup"><span data-stu-id="04001-1078">Providers</span></span>

<span data-ttu-id="04001-1079">Die folgende Tabelle zeigt die für ASP.NET Core-Apps verfügbaren Konfigurationsanbieter.</span><span class="sxs-lookup"><span data-stu-id="04001-1079">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="04001-1080">Anbieter</span><span class="sxs-lookup"><span data-stu-id="04001-1080">Provider</span></span> | <span data-ttu-id="04001-1081">Bereitstellung der Konfiguration über&hellip;</span><span class="sxs-lookup"><span data-stu-id="04001-1081">Provides configuration from&hellip;</span></span> |
| ---
<span data-ttu-id="04001-1082">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1082">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1083">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1083">'Blazor'</span></span>
- <span data-ttu-id="04001-1084">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1084">'Identity'</span></span>
- <span data-ttu-id="04001-1085">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1085">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1086">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1086">'Razor'</span></span>
- <span data-ttu-id="04001-1087">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1087">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1088">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1088">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1089">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1089">'Blazor'</span></span>
- <span data-ttu-id="04001-1090">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1090">'Identity'</span></span>
- <span data-ttu-id="04001-1091">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1091">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1092">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1092">'Razor'</span></span>
- <span data-ttu-id="04001-1093">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1093">'SignalR' uid:</span></span> 

<span data-ttu-id="04001-1094">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1094">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1095">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1095">'Blazor'</span></span>
- <span data-ttu-id="04001-1096">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1096">'Identity'</span></span>
- <span data-ttu-id="04001-1097">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1097">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1098">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1098">'Razor'</span></span>
- <span data-ttu-id="04001-1099">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1099">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1100">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1100">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1101">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1101">'Blazor'</span></span>
- <span data-ttu-id="04001-1102">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1102">'Identity'</span></span>
- <span data-ttu-id="04001-1103">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1103">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1104">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1104">'Razor'</span></span>
- <span data-ttu-id="04001-1105">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1105">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1106">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1106">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1107">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1107">'Blazor'</span></span>
- <span data-ttu-id="04001-1108">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1108">'Identity'</span></span>
- <span data-ttu-id="04001-1109">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1109">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1110">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1110">'Razor'</span></span>
- <span data-ttu-id="04001-1111">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1111">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1112">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1112">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1113">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1113">'Blazor'</span></span>
- <span data-ttu-id="04001-1114">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1114">'Identity'</span></span>
- <span data-ttu-id="04001-1115">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1115">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1116">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1116">'Razor'</span></span>
- <span data-ttu-id="04001-1117">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1117">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1119">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1119">'Blazor'</span></span>
- <span data-ttu-id="04001-1120">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1120">'Identity'</span></span>
- <span data-ttu-id="04001-1121">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1121">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1122">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1122">'Razor'</span></span>
- <span data-ttu-id="04001-1123">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1123">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1124">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1124">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1125">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1125">'Blazor'</span></span>
- <span data-ttu-id="04001-1126">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1126">'Identity'</span></span>
- <span data-ttu-id="04001-1127">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1127">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1128">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1128">'Razor'</span></span>
- <span data-ttu-id="04001-1129">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1129">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1131">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1131">'Blazor'</span></span>
- <span data-ttu-id="04001-1132">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1132">'Identity'</span></span>
- <span data-ttu-id="04001-1133">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1133">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1134">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1134">'Razor'</span></span>
- <span data-ttu-id="04001-1135">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1135">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1137">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1137">'Blazor'</span></span>
- <span data-ttu-id="04001-1138">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1138">'Identity'</span></span>
- <span data-ttu-id="04001-1139">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1139">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1140">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1140">'Razor'</span></span>
- <span data-ttu-id="04001-1141">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1141">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1142">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1142">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1143">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1143">'Blazor'</span></span>
- <span data-ttu-id="04001-1144">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1144">'Identity'</span></span>
- <span data-ttu-id="04001-1145">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1145">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1146">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1146">'Razor'</span></span>
- <span data-ttu-id="04001-1147">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1147">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1148">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1148">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1149">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1149">'Blazor'</span></span>
- <span data-ttu-id="04001-1150">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1150">'Identity'</span></span>
- <span data-ttu-id="04001-1151">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1151">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1152">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1152">'Razor'</span></span>
- <span data-ttu-id="04001-1153">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1153">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1154">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1154">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1155">'Blazor'</span></span>
- <span data-ttu-id="04001-1156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1156">'Identity'</span></span>
- <span data-ttu-id="04001-1157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1157">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1158">'Razor'</span></span>
- <span data-ttu-id="04001-1159">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1159">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1160">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1160">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1161">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1161">'Blazor'</span></span>
- <span data-ttu-id="04001-1162">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1162">'Identity'</span></span>
- <span data-ttu-id="04001-1163">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1163">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1164">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1164">'Razor'</span></span>
- <span data-ttu-id="04001-1165">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1165">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1166">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1166">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1167">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1167">'Blazor'</span></span>
- <span data-ttu-id="04001-1168">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1168">'Identity'</span></span>
- <span data-ttu-id="04001-1169">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1169">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1170">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1170">'Razor'</span></span>
- <span data-ttu-id="04001-1171">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1171">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1172">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1172">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1173">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1173">'Blazor'</span></span>
- <span data-ttu-id="04001-1174">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1174">'Identity'</span></span>
- <span data-ttu-id="04001-1175">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1175">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1176">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1176">'Razor'</span></span>
- <span data-ttu-id="04001-1177">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1177">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1178">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1178">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1179">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1179">'Blazor'</span></span>
- <span data-ttu-id="04001-1180">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1180">'Identity'</span></span>
- <span data-ttu-id="04001-1181">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1181">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1182">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1182">'Razor'</span></span>
- <span data-ttu-id="04001-1183">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1183">'SignalR' uid:</span></span> 

<span data-ttu-id="04001-1184">------------------ | | [Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration) (*Sicherheitsthemen*) | Azure Key Vault | | [Azure-App-Konfigurationsanbieter](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure-Dokumentation) | Azure App Configuration | | [Befehlszeilen-Konfigurationsanbieter](#command-line-configuration-provider) | Befehlszeilenparameter | | [Benutzerdefinierter Konfigurationsanbieter](#custom-configuration-provider) | Benutzerdefinierte Quelle | | [Umgebungsvariablen-Konfigurationsanbieter](#environment-variables-configuration-provider) | Umgebungsvariablen | | [Dateikonfigurationsanbieter](#file-configuration-provider) | Dateien (INI, JSON, XML) | | [Schlüssel-pro-Datei-Konfigurationsanbieter](#key-per-file-configuration-provider) | Verzeichnisdateien | | [Speicherkonfigurationsanbieter](#memory-configuration-provider) | In-Memory-Sammlungen | | [Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) (*Sicherheitsthemen*) | Datei im Benutzerprofilverzeichnis |</span><span class="sxs-lookup"><span data-stu-id="04001-1184">------------------ | | [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics) | Azure Key Vault | | [Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation) | Azure App Configuration | | [Command-line Configuration Provider](#command-line-configuration-provider) | Command-line parameters | | [Custom configuration provider](#custom-configuration-provider) | Custom source | | [Environment Variables Configuration Provider](#environment-variables-configuration-provider) | Environment variables | | [File Configuration Provider](#file-configuration-provider) | Files (INI, JSON, XML) | | [Key-per-file Configuration Provider](#key-per-file-configuration-provider) | Directory files | | [Memory Configuration Provider](#memory-configuration-provider) | In-memory collections | | [User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics) | File in the user profile directory |</span></span>

<span data-ttu-id="04001-1185">Konfigurationsquellen werden beim Start in der Reihenfolge gelesen, in der ihre Konfigurationsanbieter angegeben sind.</span><span class="sxs-lookup"><span data-stu-id="04001-1185">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="04001-1186">Die in diesem Thema beschriebenen Konfigurationsanbieter werden in alphabetischer Reihenfolge beschrieben, nicht in der Reihenfolge, in der der Code sie anordnet.</span><span class="sxs-lookup"><span data-stu-id="04001-1186">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="04001-1187">Ordnen Sie die Konfigurationsanbieter im Code so an, dass sie den Prioritäten für die zugrunde liegenden Konfigurationsquellen entsprechen, die für die App erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="04001-1187">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="04001-1188">Eine typische Konfigurationsanbietersequenz ist:</span><span class="sxs-lookup"><span data-stu-id="04001-1188">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="04001-1189">Dateien (*appsettings.json*, *appsettings.{Environment}.json*, wobei `{Environment}` die aktuelle Hostingumgebung der App ist)</span><span class="sxs-lookup"><span data-stu-id="04001-1189">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="04001-1190">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="04001-1190">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="04001-1191">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) (nur Entwicklungsumgebung)</span><span class="sxs-lookup"><span data-stu-id="04001-1191">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="04001-1192">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="04001-1192">Environment variables</span></span>
1. <span data-ttu-id="04001-1193">Befehlszeilenargumente</span><span class="sxs-lookup"><span data-stu-id="04001-1193">Command-line arguments</span></span>

<span data-ttu-id="04001-1194">In der Regel werden Befehlszeilen-Konfigurationsanbieter in einer Reihe von Anbietern an letzter Stelle positioniert, damit Befehlszeilenargumente die von anderen Anbietern festgelegte Konfiguration überschreiben können.</span><span class="sxs-lookup"><span data-stu-id="04001-1194">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="04001-1195">Die vorhergehende Anbieterfolge wird verwendet, wenn Sie einen neuen Host-Generator mit `CreateDefaultBuilder` initialisieren.</span><span class="sxs-lookup"><span data-stu-id="04001-1195">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="04001-1196">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="04001-1196">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="04001-1197">Konfigurieren des Host-Generators mit UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="04001-1197">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="04001-1198">Um den Host-Generator zu konfigurieren, rufen Sie <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> mit der Konfiguration für den Host-Generator auf.</span><span class="sxs-lookup"><span data-stu-id="04001-1198">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

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

## <a name="configureappconfiguration"></a><span data-ttu-id="04001-1199">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="04001-1199">ConfigureAppConfiguration</span></span>

<span data-ttu-id="04001-1200">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfigurationsanbieter der App zusätzlich zu denen anzugeben, die automatisch von `CreateDefaultBuilder` hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="04001-1200">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="04001-1201">Überschreiben der vorherigen Konfiguration mit Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="04001-1201">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="04001-1202">Um die App-Konfiguration bereitzustellen, die mit Befehlszeilenargumenten überschrieben werden kann, rufen Sie `AddCommandLine` zuletzt auf:</span><span class="sxs-lookup"><span data-stu-id="04001-1202">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="04001-1203">Entfernen von durch CreateDefaultBuilder hinzugefügten Anbietern</span><span class="sxs-lookup"><span data-stu-id="04001-1203">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="04001-1204">Rufen Sie zunächst [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) in [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) auf, um die durch `CreateDefaultBuilder` hinzugefügten Anbieter zu entfernen:</span><span class="sxs-lookup"><span data-stu-id="04001-1204">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="04001-1205">Verarbeiten der Konfiguration während des App-Starts</span><span class="sxs-lookup"><span data-stu-id="04001-1205">Consume configuration during app startup</span></span>

<span data-ttu-id="04001-1206">Die für die App in der `ConfigureAppConfiguration`-Methode angegebene Konfiguration, einschließlich `Startup.ConfigureServices`, ist während des Starts der App verfügbar.</span><span class="sxs-lookup"><span data-stu-id="04001-1206">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="04001-1207">Weitere Informationen finden Sie im Abschnitt [Hinzufügen von Konfigurationen aus einer externen Assembly](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="04001-1207">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="04001-1208">Befehlszeilen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="04001-1208">Command-line Configuration Provider</span></span>

<span data-ttu-id="04001-1209"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren des Befehlszeilenarguments.</span><span class="sxs-lookup"><span data-stu-id="04001-1209">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="04001-1210">Um die Befehlszeilenkonfiguration zu aktivieren, wird die <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>-Erweiterungsmethode für eine Instanz von <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="04001-1210">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="04001-1211">`AddCommandLine` wird automatisch aufgerufen, wenn `CreateDefaultBuilder(string [])` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="04001-1211">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="04001-1212">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="04001-1212">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="04001-1213">`CreateDefaultBuilder` lädt außerdem:</span><span class="sxs-lookup"><span data-stu-id="04001-1213">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="04001-1214">Optionale Konfiguration aus den Dateien *appsettings.json* und *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="04001-1214">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="04001-1215">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) in der Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="04001-1215">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="04001-1216">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="04001-1216">Environment variables.</span></span>

<span data-ttu-id="04001-1217">`CreateDefaultBuilder` fügt den Befehlszeilen-Konfigurationsanbieter zuletzt hinzu.</span><span class="sxs-lookup"><span data-stu-id="04001-1217">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="04001-1218">Während der Laufzeit übergebene Befehlszeilenargumente überschreiben die von anderen Anbietern festgelegte Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="04001-1218">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="04001-1219">`CreateDefaultBuilder` wird aktiv, wenn der Host erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="04001-1219">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="04001-1220">Deswegen kann die durch `CreateDefaultBuilder` aktivierte Befehlszeilenkonfiguration die Konfiguration des Hosts beeinflussen.</span><span class="sxs-lookup"><span data-stu-id="04001-1220">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="04001-1221">Für Apps, die auf den ASP.NET Core-Vorlagen basieren, wurde `AddCommandLine` bereits von `CreateDefaultBuilder` aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="04001-1221">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="04001-1222">Um weitere Konfigurationsanbieter hinzuzufügen und die Möglichkeit einer Überschreibung der Konfiguration dieser Anbieter durch Befehlszeilenargumente beizubehalten, rufen Sie die zusätzlichen Anbieter in `ConfigureAppConfiguration` und zuletzt `AddCommandLine` auf.</span><span class="sxs-lookup"><span data-stu-id="04001-1222">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="04001-1223">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="04001-1223">**Example**</span></span>

<span data-ttu-id="04001-1224">Die Beispiel-App nutzt zum Erstellen des Hosts die statische Hilfsmethode `CreateDefaultBuilder`, die einen Aufruf von <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> enthält.</span><span class="sxs-lookup"><span data-stu-id="04001-1224">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="04001-1225">Öffnen Sie im Projektverzeichnis eine Eingabeaufforderung.</span><span class="sxs-lookup"><span data-stu-id="04001-1225">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="04001-1226">Geben Sie ein Befehlszeilenargument für den `dotnet run`-Befehl an, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="04001-1226">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="04001-1227">Wenn die App ausgeführt wird, öffnen Sie einen Browser für die App unter `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="04001-1227">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="04001-1228">Beachten Sie, dass die Ausgabe das Schlüssel-Wert-Paar für das an `dotnet run` übergebene Konfigurations-Befehlszeilenargument enthält.</span><span class="sxs-lookup"><span data-stu-id="04001-1228">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="04001-1229">Argumente</span><span class="sxs-lookup"><span data-stu-id="04001-1229">Arguments</span></span>

<span data-ttu-id="04001-1230">Der Wert muss einem Gleichheitszeichen (`=`) folgen, oder der Schlüssel muss ein Präfix (`--` oder `/`) haben, wenn der Wert einem Leerzeichen folgt.</span><span class="sxs-lookup"><span data-stu-id="04001-1230">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="04001-1231">Der Wert ist nicht erforderlich, wenn ein Gleichheitszeichen verwendet wird (z. B. `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="04001-1231">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="04001-1232">Schlüsselpräfix</span><span class="sxs-lookup"><span data-stu-id="04001-1232">Key prefix</span></span>               | <span data-ttu-id="04001-1233">Beispiel</span><span class="sxs-lookup"><span data-stu-id="04001-1233">Example</span></span>                                                |
| ---
<span data-ttu-id="04001-1234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1234">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1235">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1235">'Blazor'</span></span>
- <span data-ttu-id="04001-1236">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1236">'Identity'</span></span>
- <span data-ttu-id="04001-1237">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1237">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1238">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1238">'Razor'</span></span>
- <span data-ttu-id="04001-1239">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1239">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1240">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1241">'Blazor'</span></span>
- <span data-ttu-id="04001-1242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1242">'Identity'</span></span>
- <span data-ttu-id="04001-1243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1243">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1244">'Razor'</span></span>
- <span data-ttu-id="04001-1245">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1246">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1246">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1247">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1247">'Blazor'</span></span>
- <span data-ttu-id="04001-1248">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1248">'Identity'</span></span>
- <span data-ttu-id="04001-1249">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1249">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1250">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1250">'Razor'</span></span>
- <span data-ttu-id="04001-1251">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1251">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1252">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1252">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1253">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1253">'Blazor'</span></span>
- <span data-ttu-id="04001-1254">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1254">'Identity'</span></span>
- <span data-ttu-id="04001-1255">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1255">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1256">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1256">'Razor'</span></span>
- <span data-ttu-id="04001-1257">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1257">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1258">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1258">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1259">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1259">'Blazor'</span></span>
- <span data-ttu-id="04001-1260">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1260">'Identity'</span></span>
- <span data-ttu-id="04001-1261">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1261">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1262">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1262">'Razor'</span></span>
- <span data-ttu-id="04001-1263">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1263">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1264">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1264">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1265">'Blazor'</span></span>
- <span data-ttu-id="04001-1266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1266">'Identity'</span></span>
- <span data-ttu-id="04001-1267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1267">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1268">'Razor'</span></span>
- <span data-ttu-id="04001-1269">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1270">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1270">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1271">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1271">'Blazor'</span></span>
- <span data-ttu-id="04001-1272">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1272">'Identity'</span></span>
- <span data-ttu-id="04001-1273">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1273">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1274">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1274">'Razor'</span></span>
- <span data-ttu-id="04001-1275">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1275">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1276">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1276">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1277">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1277">'Blazor'</span></span>
- <span data-ttu-id="04001-1278">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1278">'Identity'</span></span>
- <span data-ttu-id="04001-1279">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1279">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1280">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1280">'Razor'</span></span>
- <span data-ttu-id="04001-1281">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1281">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1282">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1282">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1283">'Blazor'</span></span>
- <span data-ttu-id="04001-1284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1284">'Identity'</span></span>
- <span data-ttu-id="04001-1285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1285">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1286">'Razor'</span></span>
- <span data-ttu-id="04001-1287">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1288">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1289">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1289">'Blazor'</span></span>
- <span data-ttu-id="04001-1290">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1290">'Identity'</span></span>
- <span data-ttu-id="04001-1291">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1291">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1292">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1292">'Razor'</span></span>
- <span data-ttu-id="04001-1293">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1293">'SignalR' uid:</span></span> 

<span data-ttu-id="04001-1294">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1294">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1295">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1295">'Blazor'</span></span>
- <span data-ttu-id="04001-1296">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1296">'Identity'</span></span>
- <span data-ttu-id="04001-1297">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1297">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1298">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1298">'Razor'</span></span>
- <span data-ttu-id="04001-1299">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1299">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1300">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1301">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1301">'Blazor'</span></span>
- <span data-ttu-id="04001-1302">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1302">'Identity'</span></span>
- <span data-ttu-id="04001-1303">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1303">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1304">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1304">'Razor'</span></span>
- <span data-ttu-id="04001-1305">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1305">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1306">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1307">'Blazor'</span></span>
- <span data-ttu-id="04001-1308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1308">'Identity'</span></span>
- <span data-ttu-id="04001-1309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1309">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1310">'Razor'</span></span>
- <span data-ttu-id="04001-1311">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1311">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1312">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1313">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1313">'Blazor'</span></span>
- <span data-ttu-id="04001-1314">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1314">'Identity'</span></span>
- <span data-ttu-id="04001-1315">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1315">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1316">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1316">'Razor'</span></span>
- <span data-ttu-id="04001-1317">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1317">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1318">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1319">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1319">'Blazor'</span></span>
- <span data-ttu-id="04001-1320">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1320">'Identity'</span></span>
- <span data-ttu-id="04001-1321">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1321">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1322">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1322">'Razor'</span></span>
- <span data-ttu-id="04001-1323">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1323">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1324">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1325">'Blazor'</span></span>
- <span data-ttu-id="04001-1326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1326">'Identity'</span></span>
- <span data-ttu-id="04001-1327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1327">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1328">'Razor'</span></span>
- <span data-ttu-id="04001-1329">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1329">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1330">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1331">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1331">'Blazor'</span></span>
- <span data-ttu-id="04001-1332">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1332">'Identity'</span></span>
- <span data-ttu-id="04001-1333">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1333">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1334">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1334">'Razor'</span></span>
- <span data-ttu-id="04001-1335">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1335">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1336">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1337">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1337">'Blazor'</span></span>
- <span data-ttu-id="04001-1338">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1338">'Identity'</span></span>
- <span data-ttu-id="04001-1339">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1339">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1340">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1340">'Razor'</span></span>
- <span data-ttu-id="04001-1341">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1341">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1342">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1343">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1343">'Blazor'</span></span>
- <span data-ttu-id="04001-1344">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1344">'Identity'</span></span>
- <span data-ttu-id="04001-1345">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1345">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1346">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1346">'Razor'</span></span>
- <span data-ttu-id="04001-1347">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1347">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1348">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1349">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1349">'Blazor'</span></span>
- <span data-ttu-id="04001-1350">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1350">'Identity'</span></span>
- <span data-ttu-id="04001-1351">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1351">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1352">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1352">'Razor'</span></span>
- <span data-ttu-id="04001-1353">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1353">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1354">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1355">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1355">'Blazor'</span></span>
- <span data-ttu-id="04001-1356">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1356">'Identity'</span></span>
- <span data-ttu-id="04001-1357">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1357">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1358">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1358">'Razor'</span></span>
- <span data-ttu-id="04001-1359">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1359">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1360">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1361">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1361">'Blazor'</span></span>
- <span data-ttu-id="04001-1362">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1362">'Identity'</span></span>
- <span data-ttu-id="04001-1363">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1363">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1364">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1364">'Razor'</span></span>
- <span data-ttu-id="04001-1365">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1365">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1366">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1367">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1367">'Blazor'</span></span>
- <span data-ttu-id="04001-1368">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1368">'Identity'</span></span>
- <span data-ttu-id="04001-1369">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1369">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1370">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1370">'Razor'</span></span>
- <span data-ttu-id="04001-1371">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1371">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1372">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1372">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1373">'Blazor'</span></span>
- <span data-ttu-id="04001-1374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1374">'Identity'</span></span>
- <span data-ttu-id="04001-1375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1375">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1376">'Razor'</span></span>
- <span data-ttu-id="04001-1377">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1378">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1378">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1379">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1379">'Blazor'</span></span>
- <span data-ttu-id="04001-1380">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1380">'Identity'</span></span>
- <span data-ttu-id="04001-1381">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1381">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1382">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1382">'Razor'</span></span>
- <span data-ttu-id="04001-1383">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1383">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1384">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1384">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1385">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1385">'Blazor'</span></span>
- <span data-ttu-id="04001-1386">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1386">'Identity'</span></span>
- <span data-ttu-id="04001-1387">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1387">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1388">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1388">'Razor'</span></span>
- <span data-ttu-id="04001-1389">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1389">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1390">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1390">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1391">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1391">'Blazor'</span></span>
- <span data-ttu-id="04001-1392">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1392">'Identity'</span></span>
- <span data-ttu-id="04001-1393">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1393">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1394">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1394">'Razor'</span></span>
- <span data-ttu-id="04001-1395">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1395">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1396">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1396">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1397">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1397">'Blazor'</span></span>
- <span data-ttu-id="04001-1398">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1398">'Identity'</span></span>
- <span data-ttu-id="04001-1399">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1399">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1400">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1400">'Razor'</span></span>
- <span data-ttu-id="04001-1401">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1401">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1403">'Blazor'</span></span>
- <span data-ttu-id="04001-1404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1404">'Identity'</span></span>
- <span data-ttu-id="04001-1405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1405">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1406">'Razor'</span></span>
- <span data-ttu-id="04001-1407">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1409">'Blazor'</span></span>
- <span data-ttu-id="04001-1410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1410">'Identity'</span></span>
- <span data-ttu-id="04001-1411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1411">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1412">'Razor'</span></span>
- <span data-ttu-id="04001-1413">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1415">'Blazor'</span></span>
- <span data-ttu-id="04001-1416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1416">'Identity'</span></span>
- <span data-ttu-id="04001-1417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1417">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1418">'Razor'</span></span>
- <span data-ttu-id="04001-1419">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1421">'Blazor'</span></span>
- <span data-ttu-id="04001-1422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1422">'Identity'</span></span>
- <span data-ttu-id="04001-1423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1423">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1424">'Razor'</span></span>
- <span data-ttu-id="04001-1425">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1427">'Blazor'</span></span>
- <span data-ttu-id="04001-1428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1428">'Identity'</span></span>
- <span data-ttu-id="04001-1429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1429">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1430">'Razor'</span></span>
- <span data-ttu-id="04001-1431">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1433">'Blazor'</span></span>
- <span data-ttu-id="04001-1434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1434">'Identity'</span></span>
- <span data-ttu-id="04001-1435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1435">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1436">'Razor'</span></span>
- <span data-ttu-id="04001-1437">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1439">'Blazor'</span></span>
- <span data-ttu-id="04001-1440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1440">'Identity'</span></span>
- <span data-ttu-id="04001-1441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1441">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1442">'Razor'</span></span>
- <span data-ttu-id="04001-1443">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1443">'SignalR' uid:</span></span> 

<span data-ttu-id="04001-1444">--------------------------- | | Kein Präfix                | `CommandLineKey1=value1`                               |
| Zwei Bindestriche (`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
|Schrägstrich (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |</span><span class="sxs-lookup"><span data-stu-id="04001-1444">--------------------------- | | No prefix                | `CommandLineKey1=value1`                               |
| Two dashes (`--`)        | `--CommandLineKey2=value2`, `--CommandLineKey2 value2` |
| Forward slash (`/`)      | `/CommandLineKey3=value3`, `/CommandLineKey3 value3`   |</span></span>

<span data-ttu-id="04001-1445">Kombinieren Sie in einem Befehl nicht Schlüssel-Wert-Paare, die ein Gleichheitszeichen verwenden, mit Schlüssel-Wert-Paaren, die ein Leerzeichen verwenden.</span><span class="sxs-lookup"><span data-stu-id="04001-1445">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="04001-1446">Beispielbefehle:</span><span class="sxs-lookup"><span data-stu-id="04001-1446">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="04001-1447">Switchmappings</span><span class="sxs-lookup"><span data-stu-id="04001-1447">Switch mappings</span></span>

<span data-ttu-id="04001-1448">Switchmappings erlauben das Angeben einer Logik zum Ersetzen von Schlüsselnamen.</span><span class="sxs-lookup"><span data-stu-id="04001-1448">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="04001-1449">Wenn Sie die Konfiguration manuell mit <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> erstellen, können Sie ein Wörterbuch der Switchersetzungen für die <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>-Methode bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="04001-1449">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="04001-1450">Wenn das Switchmappingwörterbuch verwendet wird, wird das Wörterbuch für Schlüssel, die dem von einem Befehlszeilenargument angegebenen Schlüssel entsprechen, überprüft.</span><span class="sxs-lookup"><span data-stu-id="04001-1450">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="04001-1451">Wenn der Befehlszeilenschlüssel im Wörterbuch gefunden wird, wird der Wörterbuchwert (der Schlüsselersatz) zum Festlegen des Schlüssel-Wert-Paares in der App-Konfiguration zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="04001-1451">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="04001-1452">Ein Switchmapping ist für jeden Befehlszeilenschlüssel erforderlich, dem ein einzelner Gedankenstrich (`-`) vorangestellt ist.</span><span class="sxs-lookup"><span data-stu-id="04001-1452">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="04001-1453">Regeln für Schlüssel von Switchmappingwörterbüchern:</span><span class="sxs-lookup"><span data-stu-id="04001-1453">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="04001-1454">Switchmappings müssen mit einem Gedankenstrich (`-`) oder zwei Gedankenstrichen (`--`) beginnen.</span><span class="sxs-lookup"><span data-stu-id="04001-1454">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="04001-1455">Das Switchmappingwörterbuch darf keine doppelten Schlüssel enthalten.</span><span class="sxs-lookup"><span data-stu-id="04001-1455">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="04001-1456">Erstellen Sie ein Switchmappingwörterbuch.</span><span class="sxs-lookup"><span data-stu-id="04001-1456">Create a switch mappings dictionary.</span></span> <span data-ttu-id="04001-1457">Im folgenden Beispiel werden zwei Switchmappings erstellt:</span><span class="sxs-lookup"><span data-stu-id="04001-1457">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="04001-1458">Wenn der Host erstellt wird, rufen Sie `AddCommandLine` mit dem Switchmappingwörterbuch auf:</span><span class="sxs-lookup"><span data-stu-id="04001-1458">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="04001-1459">Bei Apps, die Switchmappings verwenden, sollten im `CreateDefaultBuilder`-Aufruf keine Argumente übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="04001-1459">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="04001-1460">Der Aufruf von `CreateDefaultBuilder` der `AddCommandLine`-Methode umfasst keine zugeordneten Switches, und das Switchmappingwörterbuch kann nicht an `CreateDefaultBuilder` übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="04001-1460">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="04001-1461">Die Lösung besteht nicht darin, die Argumente an `CreateDefaultBuilder` zu übergeben, sondern der Methode `AddCommandLine` der `ConfigurationBuilder`-Methode zu erlauben, sowohl die Argumente als auch das Switchmappingwörterbuch zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="04001-1461">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="04001-1462">Das erstellte Switchmappingwörterbuch enthält die in der folgenden Tabelle gezeigten Daten.</span><span class="sxs-lookup"><span data-stu-id="04001-1462">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="04001-1463">Key</span><span class="sxs-lookup"><span data-stu-id="04001-1463">Key</span></span>       | <span data-ttu-id="04001-1464">Wert</span><span class="sxs-lookup"><span data-stu-id="04001-1464">Value</span></span>             |
| ---
<span data-ttu-id="04001-1465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1466">'Blazor'</span></span>
- <span data-ttu-id="04001-1467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1467">'Identity'</span></span>
- <span data-ttu-id="04001-1468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1468">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1469">'Razor'</span></span>
- <span data-ttu-id="04001-1470">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1472">'Blazor'</span></span>
- <span data-ttu-id="04001-1473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1473">'Identity'</span></span>
- <span data-ttu-id="04001-1474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1474">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1475">'Razor'</span></span>
- <span data-ttu-id="04001-1476">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1476">'SignalR' uid:</span></span> 

<span data-ttu-id="04001-1477">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1477">----- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1478">'Blazor'</span></span>
- <span data-ttu-id="04001-1479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1479">'Identity'</span></span>
- <span data-ttu-id="04001-1480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1480">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1481">'Razor'</span></span>
- <span data-ttu-id="04001-1482">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1484">'Blazor'</span></span>
- <span data-ttu-id="04001-1485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1485">'Identity'</span></span>
- <span data-ttu-id="04001-1486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1486">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1487">'Razor'</span></span>
- <span data-ttu-id="04001-1488">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1490">'Blazor'</span></span>
- <span data-ttu-id="04001-1491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1491">'Identity'</span></span>
- <span data-ttu-id="04001-1492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1492">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1493">'Razor'</span></span>
- <span data-ttu-id="04001-1494">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1496">'Blazor'</span></span>
- <span data-ttu-id="04001-1497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1497">'Identity'</span></span>
- <span data-ttu-id="04001-1498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1498">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1499">'Razor'</span></span>
- <span data-ttu-id="04001-1500">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1502">'Blazor'</span></span>
- <span data-ttu-id="04001-1503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1503">'Identity'</span></span>
- <span data-ttu-id="04001-1504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1504">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1505">'Razor'</span></span>
- <span data-ttu-id="04001-1506">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1508">'Blazor'</span></span>
- <span data-ttu-id="04001-1509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1509">'Identity'</span></span>
- <span data-ttu-id="04001-1510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1510">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1511">'Razor'</span></span>
- <span data-ttu-id="04001-1512">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1512">'SignalR' uid:</span></span> 

<span data-ttu-id="04001-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span><span class="sxs-lookup"><span data-stu-id="04001-1513">--------- | | `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |</span></span>

<span data-ttu-id="04001-1514">Wenn Switches zugeordnete Schlüssel beim Start der App verwendet werden, erhält die Konfiguration den Konfigurationswert auf dem vom Wörterbuch bereitgestellten Schlüssel:</span><span class="sxs-lookup"><span data-stu-id="04001-1514">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="04001-1515">Nach dem Ausführen des obigen Befehls enthält die Konfiguration die in der folgenden Tabelle angegebenen Werte.</span><span class="sxs-lookup"><span data-stu-id="04001-1515">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="04001-1516">Key</span><span class="sxs-lookup"><span data-stu-id="04001-1516">Key</span></span>               | <span data-ttu-id="04001-1517">Wert</span><span class="sxs-lookup"><span data-stu-id="04001-1517">Value</span></span>    |
| ---
<span data-ttu-id="04001-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1519">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1519">'Blazor'</span></span>
- <span data-ttu-id="04001-1520">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1520">'Identity'</span></span>
- <span data-ttu-id="04001-1521">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1521">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1522">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1522">'Razor'</span></span>
- <span data-ttu-id="04001-1523">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1523">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1524">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1524">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1525">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1525">'Blazor'</span></span>
- <span data-ttu-id="04001-1526">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1526">'Identity'</span></span>
- <span data-ttu-id="04001-1527">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1527">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1528">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1528">'Razor'</span></span>
- <span data-ttu-id="04001-1529">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1529">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1531">'Blazor'</span></span>
- <span data-ttu-id="04001-1532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1532">'Identity'</span></span>
- <span data-ttu-id="04001-1533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1533">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1534">'Razor'</span></span>
- <span data-ttu-id="04001-1535">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1537">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1537">'Blazor'</span></span>
- <span data-ttu-id="04001-1538">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1538">'Identity'</span></span>
- <span data-ttu-id="04001-1539">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1539">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1540">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1540">'Razor'</span></span>
- <span data-ttu-id="04001-1541">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1541">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1543">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1543">'Blazor'</span></span>
- <span data-ttu-id="04001-1544">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1544">'Identity'</span></span>
- <span data-ttu-id="04001-1545">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1545">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1546">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1546">'Razor'</span></span>
- <span data-ttu-id="04001-1547">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1547">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1549">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1549">'Blazor'</span></span>
- <span data-ttu-id="04001-1550">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1550">'Identity'</span></span>
- <span data-ttu-id="04001-1551">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1551">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1552">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1552">'Razor'</span></span>
- <span data-ttu-id="04001-1553">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1553">'SignalR' uid:</span></span> 

<span data-ttu-id="04001-1554">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1554">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1555">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1555">'Blazor'</span></span>
- <span data-ttu-id="04001-1556">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1556">'Identity'</span></span>
- <span data-ttu-id="04001-1557">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1557">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1558">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1558">'Razor'</span></span>
- <span data-ttu-id="04001-1559">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1559">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1561">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1561">'Blazor'</span></span>
- <span data-ttu-id="04001-1562">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1562">'Identity'</span></span>
- <span data-ttu-id="04001-1563">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1563">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1564">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1564">'Razor'</span></span>
- <span data-ttu-id="04001-1565">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1565">'SignalR' uid:</span></span> 

<span data-ttu-id="04001-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span><span class="sxs-lookup"><span data-stu-id="04001-1566">---- | | `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |</span></span>

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="04001-1567">Umgebungsvariablen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="04001-1567">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="04001-1568"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="04001-1568">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="04001-1569">Um die Umgebungsvariablenkonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="04001-1569">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="04001-1570">[Azure App Service](https://azure.microsoft.com/services/app-service/) ermöglicht Ihnen Umgebungsvariablen im Azure-Portal festzulegen, die die App-Konfiguration mit dem Konfigurationsanbieter für Umgebungsvariablen überschreiben können.</span><span class="sxs-lookup"><span data-stu-id="04001-1570">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="04001-1571">Weitere Informationen finden Sie unter [Azure-Apps: Überschreiben der App-Konfiguration im Azure-Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="04001-1571">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="04001-1572">`AddEnvironmentVariables` wird zum Laden von Umgebungsvariablen verwendet, die das Präfix `ASPNETCORE_` für die [Hostkonfiguration](#host-versus-app-configuration) aufweisen, wenn ein neuer Host-Generator mit dem [Webhost](xref:fundamentals/host/web-host) initialisiert und `CreateDefaultBuilder` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="04001-1572">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="04001-1573">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="04001-1573">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="04001-1574">`CreateDefaultBuilder` lädt außerdem:</span><span class="sxs-lookup"><span data-stu-id="04001-1574">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="04001-1575">App-Konfiguration über Umgebungsvariablen ohne Präfix durch Aufruf von `AddEnvironmentVariables` ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="04001-1575">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="04001-1576">Optionale Konfiguration aus den Dateien *appsettings.json* und *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="04001-1576">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="04001-1577">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) in der Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="04001-1577">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="04001-1578">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="04001-1578">Command-line arguments.</span></span>

<span data-ttu-id="04001-1579">Der Umgebungsvariablen-Konfigurationsanbieter wird aufgerufen, nachdem die Konfiguration aus Benutzergeheimnissen und *appsettings*-Dateien erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="04001-1579">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="04001-1580">Das Aufrufen des Anbieters an dieser Stelle ermöglicht den während der Laufzeit gelesenen Umgebungsvariablen, die von Benutzergeheimnissen und *appsettings*-Dateien festgelegte Konfiguration zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="04001-1580">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="04001-1581">Wenn Sie App-Konfigurationen aus zusätzlichen Umgebungsvariablen bereitstellen müssen, rufen Sie die zusätzlichen Anbieter der App in `ConfigureAppConfiguration` auf, und rufen Sie `AddEnvironmentVariables` mit dem Präfix auf:</span><span class="sxs-lookup"><span data-stu-id="04001-1581">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="04001-1582">Rufen Sie `AddEnvironmentVariables` zuletzt auf, um Umgebungsvariablen mit dem angegebenen Präfix zuzulassen, um Werte von anderen Anbietern zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="04001-1582">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="04001-1583">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="04001-1583">**Example**</span></span>

<span data-ttu-id="04001-1584">Die Beispiel-App nutzt zum Erstellen des Hosts die statische Hilfsmethode `CreateDefaultBuilder`, die einen Aufruf von `AddEnvironmentVariables` enthält.</span><span class="sxs-lookup"><span data-stu-id="04001-1584">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="04001-1585">Führen Sie die Beispiel-App aus.</span><span class="sxs-lookup"><span data-stu-id="04001-1585">Run the sample app.</span></span> <span data-ttu-id="04001-1586">Öffnen Sie einen Browser für die App unter `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="04001-1586">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="04001-1587">Beachten Sie, dass die Ausgabe das Schlüssel-Wert-Paar für die Umgebungsvariable `ENVIRONMENT` enthält.</span><span class="sxs-lookup"><span data-stu-id="04001-1587">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="04001-1588">Der Wert entspricht der Umgebung, in der die App ausgeführt wird. In der Regel ist das `Development` bei lokaler Ausführung.</span><span class="sxs-lookup"><span data-stu-id="04001-1588">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="04001-1589">Um die Liste der von der App gerenderten Umgebungsvariablen kurz zu halten, filtert die App Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="04001-1589">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="04001-1590">Weitere Informationen finden Sie in der Datei *Pages/Index.cshtml.cs* der Beispiel-App.</span><span class="sxs-lookup"><span data-stu-id="04001-1590">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="04001-1591">Wenn Sie alle für die App verfügbaren Umgebungsvariablen verfügbar machen möchten, ändern Sie `FilteredConfiguration` in *Pages/Index.cshtml.cs* wie folgt:</span><span class="sxs-lookup"><span data-stu-id="04001-1591">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="04001-1592">Präfixe</span><span class="sxs-lookup"><span data-stu-id="04001-1592">Prefixes</span></span>

<span data-ttu-id="04001-1593">Umgebungsvariablen, die in die Konfiguration der App geladen werden, werden gefiltert, wenn Sie ein Präfix für die Methode `AddEnvironmentVariables` angeben.</span><span class="sxs-lookup"><span data-stu-id="04001-1593">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="04001-1594">Um beispielsweise Umgebungsvariablen nach dem Präfix `CUSTOM_` zu filtern, geben Sie das Präfix für den Konfigurationsanbieter an:</span><span class="sxs-lookup"><span data-stu-id="04001-1594">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="04001-1595">Das Präfix wird beim Erstellen der Schlüssel-Wert-Paare der Konfiguration entfernt.</span><span class="sxs-lookup"><span data-stu-id="04001-1595">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="04001-1596">Beim Erstellen des Host-Generators wird die Hostkonfiguration durch Umgebungsvariablen bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="04001-1596">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="04001-1597">Weitere Informationen zu dem Präfix, das für diese Umgebungsvariablen verwendet wird, finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="04001-1597">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="04001-1598">**Präfixe für Verbindungszeichenfolgen**</span><span class="sxs-lookup"><span data-stu-id="04001-1598">**Connection string prefixes**</span></span>

<span data-ttu-id="04001-1599">Die Konfigurations-API verfügt über spezielle Verarbeitungsregeln für vier Umgebungsvariablen für Verbindungszeichenfolgen, die beim Konfigurieren von Azure-Verbindungszeichenfolgen für die App-Umgebung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="04001-1599">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="04001-1600">Umgebungsvariablen mit Präfixen, die in der Tabelle aufgeführt werden, werden in die App geladen, wenn für `AddEnvironmentVariables` kein Präfix angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="04001-1600">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="04001-1601">Präfix für Verbindungszeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="04001-1601">Connection string prefix</span></span> | <span data-ttu-id="04001-1602">Anbieter</span><span class="sxs-lookup"><span data-stu-id="04001-1602">Provider</span></span> |
| ---
<span data-ttu-id="04001-1603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1604">'Blazor'</span></span>
- <span data-ttu-id="04001-1605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1605">'Identity'</span></span>
- <span data-ttu-id="04001-1606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1606">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1607">'Razor'</span></span>
- <span data-ttu-id="04001-1608">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1610">'Blazor'</span></span>
- <span data-ttu-id="04001-1611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1611">'Identity'</span></span>
- <span data-ttu-id="04001-1612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1612">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1613">'Razor'</span></span>
- <span data-ttu-id="04001-1614">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1614">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1615">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1616">'Blazor'</span></span>
- <span data-ttu-id="04001-1617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1617">'Identity'</span></span>
- <span data-ttu-id="04001-1618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1618">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1619">'Razor'</span></span>
- <span data-ttu-id="04001-1620">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1622">'Blazor'</span></span>
- <span data-ttu-id="04001-1623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1623">'Identity'</span></span>
- <span data-ttu-id="04001-1624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1624">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1625">'Razor'</span></span>
- <span data-ttu-id="04001-1626">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1628">'Blazor'</span></span>
- <span data-ttu-id="04001-1629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1629">'Identity'</span></span>
- <span data-ttu-id="04001-1630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1630">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1631">'Razor'</span></span>
- <span data-ttu-id="04001-1632">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1634">'Blazor'</span></span>
- <span data-ttu-id="04001-1635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1635">'Identity'</span></span>
- <span data-ttu-id="04001-1636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1636">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1637">'Razor'</span></span>
- <span data-ttu-id="04001-1638">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1640">'Blazor'</span></span>
- <span data-ttu-id="04001-1641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1641">'Identity'</span></span>
- <span data-ttu-id="04001-1642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1642">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1643">'Razor'</span></span>
- <span data-ttu-id="04001-1644">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1646">'Blazor'</span></span>
- <span data-ttu-id="04001-1647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1647">'Identity'</span></span>
- <span data-ttu-id="04001-1648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1648">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1649">'Razor'</span></span>
- <span data-ttu-id="04001-1650">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1652">'Blazor'</span></span>
- <span data-ttu-id="04001-1653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1653">'Identity'</span></span>
- <span data-ttu-id="04001-1654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1654">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1655">'Razor'</span></span>
- <span data-ttu-id="04001-1656">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1658">'Blazor'</span></span>
- <span data-ttu-id="04001-1659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1659">'Identity'</span></span>
- <span data-ttu-id="04001-1660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1660">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1661">'Razor'</span></span>
- <span data-ttu-id="04001-1662">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1662">'SignalR' uid:</span></span> 

<span data-ttu-id="04001-1663">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1663">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1664">'Blazor'</span></span>
- <span data-ttu-id="04001-1665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1665">'Identity'</span></span>
- <span data-ttu-id="04001-1666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1666">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1667">'Razor'</span></span>
- <span data-ttu-id="04001-1668">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1670">'Blazor'</span></span>
- <span data-ttu-id="04001-1671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1671">'Identity'</span></span>
- <span data-ttu-id="04001-1672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1672">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1673">'Razor'</span></span>
- <span data-ttu-id="04001-1674">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1674">'SignalR' uid:</span></span> 

<span data-ttu-id="04001-1675">---- | | `CUSTOMCONNSTR_` | Benutzerdefinierter Anbieter | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL-Datenbank](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span><span class="sxs-lookup"><span data-stu-id="04001-1675">---- | | `CUSTOMCONNSTR_` | Custom provider | | `MYSQLCONNSTR_` | [MySQL](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [SQL Server](https://www.microsoft.com/sql-server/) |</span></span>

<span data-ttu-id="04001-1676">Wenn eine Umgebungsvariable entdeckt und mit einem der vier Präfixe aus der Tabelle in die Konfiguration geladen wird, tritt Folgendes ein:</span><span class="sxs-lookup"><span data-stu-id="04001-1676">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="04001-1677">Der Konfigurationsschlüssel wird durch Entfernen des Umgebungsvariablenpräfixes und Hinzufügen eines Konfigurationsschlüsselabschnitts (`ConnectionStrings`) erstellt.</span><span class="sxs-lookup"><span data-stu-id="04001-1677">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="04001-1678">Ein neues Konfigurations-Schlüssel-Wert-Paar wird erstellt, das den Datenbankverbindungsanbieter repräsentiert (mit Ausnahme des Präfixes `CUSTOMCONNSTR_`, für das kein Anbieter angegeben ist).</span><span class="sxs-lookup"><span data-stu-id="04001-1678">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="04001-1679">Umgebungsvariablenschlüssel</span><span class="sxs-lookup"><span data-stu-id="04001-1679">Environment variable key</span></span> | <span data-ttu-id="04001-1680">Konvertierter Konfigurationsschlüssel</span><span class="sxs-lookup"><span data-stu-id="04001-1680">Converted configuration key</span></span> | <span data-ttu-id="04001-1681">Anbieterkonfigurationseintrag</span><span class="sxs-lookup"><span data-stu-id="04001-1681">Provider configuration entry</span></span>                                                    |
| ---
<span data-ttu-id="04001-1682">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1682">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1683">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1683">'Blazor'</span></span>
- <span data-ttu-id="04001-1684">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1684">'Identity'</span></span>
- <span data-ttu-id="04001-1685">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1685">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1686">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1686">'Razor'</span></span>
- <span data-ttu-id="04001-1687">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1687">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1688">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1688">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1689">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1689">'Blazor'</span></span>
- <span data-ttu-id="04001-1690">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1690">'Identity'</span></span>
- <span data-ttu-id="04001-1691">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1691">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1692">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1692">'Razor'</span></span>
- <span data-ttu-id="04001-1693">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1693">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1694">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1694">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1695">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1695">'Blazor'</span></span>
- <span data-ttu-id="04001-1696">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1696">'Identity'</span></span>
- <span data-ttu-id="04001-1697">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1697">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1698">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1698">'Razor'</span></span>
- <span data-ttu-id="04001-1699">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1699">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1700">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1700">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1701">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1701">'Blazor'</span></span>
- <span data-ttu-id="04001-1702">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1702">'Identity'</span></span>
- <span data-ttu-id="04001-1703">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1703">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1704">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1704">'Razor'</span></span>
- <span data-ttu-id="04001-1705">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1705">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1706">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1706">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1707">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1707">'Blazor'</span></span>
- <span data-ttu-id="04001-1708">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1708">'Identity'</span></span>
- <span data-ttu-id="04001-1709">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1709">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1710">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1710">'Razor'</span></span>
- <span data-ttu-id="04001-1711">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1711">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1712">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1712">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1713">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1713">'Blazor'</span></span>
- <span data-ttu-id="04001-1714">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1714">'Identity'</span></span>
- <span data-ttu-id="04001-1715">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1715">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1716">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1716">'Razor'</span></span>
- <span data-ttu-id="04001-1717">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1717">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1718">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1718">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1719">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1719">'Blazor'</span></span>
- <span data-ttu-id="04001-1720">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1720">'Identity'</span></span>
- <span data-ttu-id="04001-1721">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1721">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1722">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1722">'Razor'</span></span>
- <span data-ttu-id="04001-1723">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1723">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1724">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1724">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1725">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1725">'Blazor'</span></span>
- <span data-ttu-id="04001-1726">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1726">'Identity'</span></span>
- <span data-ttu-id="04001-1727">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1727">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1728">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1728">'Razor'</span></span>
- <span data-ttu-id="04001-1729">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1729">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1730">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1730">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1731">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1731">'Blazor'</span></span>
- <span data-ttu-id="04001-1732">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1732">'Identity'</span></span>
- <span data-ttu-id="04001-1733">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1733">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1734">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1734">'Razor'</span></span>
- <span data-ttu-id="04001-1735">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1735">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1736">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1736">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1737">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1737">'Blazor'</span></span>
- <span data-ttu-id="04001-1738">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1738">'Identity'</span></span>
- <span data-ttu-id="04001-1739">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1739">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1740">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1740">'Razor'</span></span>
- <span data-ttu-id="04001-1741">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1741">'SignalR' uid:</span></span> 

<span data-ttu-id="04001-1742">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1742">------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1743">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1743">'Blazor'</span></span>
- <span data-ttu-id="04001-1744">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1744">'Identity'</span></span>
- <span data-ttu-id="04001-1745">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1745">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1746">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1746">'Razor'</span></span>
- <span data-ttu-id="04001-1747">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1747">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1748">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1748">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1749">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1749">'Blazor'</span></span>
- <span data-ttu-id="04001-1750">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1750">'Identity'</span></span>
- <span data-ttu-id="04001-1751">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1751">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1752">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1752">'Razor'</span></span>
- <span data-ttu-id="04001-1753">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1753">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1754">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1754">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1755">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1755">'Blazor'</span></span>
- <span data-ttu-id="04001-1756">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1756">'Identity'</span></span>
- <span data-ttu-id="04001-1757">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1757">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1758">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1758">'Razor'</span></span>
- <span data-ttu-id="04001-1759">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1759">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1760">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1760">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1761">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1761">'Blazor'</span></span>
- <span data-ttu-id="04001-1762">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1762">'Identity'</span></span>
- <span data-ttu-id="04001-1763">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1763">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1764">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1764">'Razor'</span></span>
- <span data-ttu-id="04001-1765">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1765">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1766">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1766">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1767">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1767">'Blazor'</span></span>
- <span data-ttu-id="04001-1768">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1768">'Identity'</span></span>
- <span data-ttu-id="04001-1769">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1769">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1770">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1770">'Razor'</span></span>
- <span data-ttu-id="04001-1771">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1771">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1772">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1772">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1773">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1773">'Blazor'</span></span>
- <span data-ttu-id="04001-1774">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1774">'Identity'</span></span>
- <span data-ttu-id="04001-1775">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1775">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1776">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1776">'Razor'</span></span>
- <span data-ttu-id="04001-1777">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1777">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1778">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1778">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1779">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1779">'Blazor'</span></span>
- <span data-ttu-id="04001-1780">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1780">'Identity'</span></span>
- <span data-ttu-id="04001-1781">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1781">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1782">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1782">'Razor'</span></span>
- <span data-ttu-id="04001-1783">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1783">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1784">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1784">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1785">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1785">'Blazor'</span></span>
- <span data-ttu-id="04001-1786">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1786">'Identity'</span></span>
- <span data-ttu-id="04001-1787">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1787">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1788">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1788">'Razor'</span></span>
- <span data-ttu-id="04001-1789">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1789">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1790">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1790">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1791">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1791">'Blazor'</span></span>
- <span data-ttu-id="04001-1792">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1792">'Identity'</span></span>
- <span data-ttu-id="04001-1793">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1793">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1794">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1794">'Razor'</span></span>
- <span data-ttu-id="04001-1795">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1795">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1796">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1796">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1797">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1797">'Blazor'</span></span>
- <span data-ttu-id="04001-1798">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1798">'Identity'</span></span>
- <span data-ttu-id="04001-1799">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1799">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1800">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1800">'Razor'</span></span>
- <span data-ttu-id="04001-1801">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1801">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1802">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1802">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1803">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1803">'Blazor'</span></span>
- <span data-ttu-id="04001-1804">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1804">'Identity'</span></span>
- <span data-ttu-id="04001-1805">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1805">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1806">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1806">'Razor'</span></span>
- <span data-ttu-id="04001-1807">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1807">'SignalR' uid:</span></span> 

<span data-ttu-id="04001-1808">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1808">-------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1809">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1809">'Blazor'</span></span>
- <span data-ttu-id="04001-1810">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1810">'Identity'</span></span>
- <span data-ttu-id="04001-1811">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1811">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1812">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1812">'Razor'</span></span>
- <span data-ttu-id="04001-1813">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1813">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1814">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1814">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1815">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1815">'Blazor'</span></span>
- <span data-ttu-id="04001-1816">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1816">'Identity'</span></span>
- <span data-ttu-id="04001-1817">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1817">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1818">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1818">'Razor'</span></span>
- <span data-ttu-id="04001-1819">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1819">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1820">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1820">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1821">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1821">'Blazor'</span></span>
- <span data-ttu-id="04001-1822">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1822">'Identity'</span></span>
- <span data-ttu-id="04001-1823">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1823">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1824">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1824">'Razor'</span></span>
- <span data-ttu-id="04001-1825">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1825">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1826">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1826">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1827">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1827">'Blazor'</span></span>
- <span data-ttu-id="04001-1828">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1828">'Identity'</span></span>
- <span data-ttu-id="04001-1829">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1829">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1830">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1830">'Razor'</span></span>
- <span data-ttu-id="04001-1831">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1831">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1832">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1832">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1833">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1833">'Blazor'</span></span>
- <span data-ttu-id="04001-1834">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1834">'Identity'</span></span>
- <span data-ttu-id="04001-1835">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1835">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1836">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1836">'Razor'</span></span>
- <span data-ttu-id="04001-1837">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1837">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1838">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1838">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1839">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1839">'Blazor'</span></span>
- <span data-ttu-id="04001-1840">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1840">'Identity'</span></span>
- <span data-ttu-id="04001-1841">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1841">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1842">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1842">'Razor'</span></span>
- <span data-ttu-id="04001-1843">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1843">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1844">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1844">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1845">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1845">'Blazor'</span></span>
- <span data-ttu-id="04001-1846">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1846">'Identity'</span></span>
- <span data-ttu-id="04001-1847">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1847">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1848">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1848">'Razor'</span></span>
- <span data-ttu-id="04001-1849">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1849">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1850">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1850">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1851">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1851">'Blazor'</span></span>
- <span data-ttu-id="04001-1852">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1852">'Identity'</span></span>
- <span data-ttu-id="04001-1853">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1853">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1854">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1854">'Razor'</span></span>
- <span data-ttu-id="04001-1855">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1855">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1856">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1856">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1857">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1857">'Blazor'</span></span>
- <span data-ttu-id="04001-1858">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1858">'Identity'</span></span>
- <span data-ttu-id="04001-1859">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1859">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1860">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1860">'Razor'</span></span>
- <span data-ttu-id="04001-1861">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1861">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1862">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1862">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1863">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1863">'Blazor'</span></span>
- <span data-ttu-id="04001-1864">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1864">'Identity'</span></span>
- <span data-ttu-id="04001-1865">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1865">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1866">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1866">'Razor'</span></span>
- <span data-ttu-id="04001-1867">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1867">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1868">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1868">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1869">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1869">'Blazor'</span></span>
- <span data-ttu-id="04001-1870">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1870">'Identity'</span></span>
- <span data-ttu-id="04001-1871">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1871">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1872">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1872">'Razor'</span></span>
- <span data-ttu-id="04001-1873">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1873">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1874">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1874">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1875">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1875">'Blazor'</span></span>
- <span data-ttu-id="04001-1876">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1876">'Identity'</span></span>
- <span data-ttu-id="04001-1877">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1877">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1878">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1878">'Razor'</span></span>
- <span data-ttu-id="04001-1879">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1879">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1880">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1880">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1881">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1881">'Blazor'</span></span>
- <span data-ttu-id="04001-1882">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1882">'Identity'</span></span>
- <span data-ttu-id="04001-1883">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1883">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1884">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1884">'Razor'</span></span>
- <span data-ttu-id="04001-1885">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1885">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1886">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1886">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1887">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1887">'Blazor'</span></span>
- <span data-ttu-id="04001-1888">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1888">'Identity'</span></span>
- <span data-ttu-id="04001-1889">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1889">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1890">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1890">'Razor'</span></span>
- <span data-ttu-id="04001-1891">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1891">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1892">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1892">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1893">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1893">'Blazor'</span></span>
- <span data-ttu-id="04001-1894">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1894">'Identity'</span></span>
- <span data-ttu-id="04001-1895">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1895">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1896">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1896">'Razor'</span></span>
- <span data-ttu-id="04001-1897">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1897">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1898">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1898">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1899">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1899">'Blazor'</span></span>
- <span data-ttu-id="04001-1900">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1900">'Identity'</span></span>
- <span data-ttu-id="04001-1901">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1901">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1902">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1902">'Razor'</span></span>
- <span data-ttu-id="04001-1903">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1903">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1904">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1904">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1905">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1905">'Blazor'</span></span>
- <span data-ttu-id="04001-1906">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1906">'Identity'</span></span>
- <span data-ttu-id="04001-1907">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1907">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1908">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1908">'Razor'</span></span>
- <span data-ttu-id="04001-1909">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1909">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1910">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1910">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1911">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1911">'Blazor'</span></span>
- <span data-ttu-id="04001-1912">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1912">'Identity'</span></span>
- <span data-ttu-id="04001-1913">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1913">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1914">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1914">'Razor'</span></span>
- <span data-ttu-id="04001-1915">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1915">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1916">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1916">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1917">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1917">'Blazor'</span></span>
- <span data-ttu-id="04001-1918">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1918">'Identity'</span></span>
- <span data-ttu-id="04001-1919">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1919">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1920">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1920">'Razor'</span></span>
- <span data-ttu-id="04001-1921">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1921">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1922">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1922">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1923">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1923">'Blazor'</span></span>
- <span data-ttu-id="04001-1924">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1924">'Identity'</span></span>
- <span data-ttu-id="04001-1925">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1925">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1926">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1926">'Razor'</span></span>
- <span data-ttu-id="04001-1927">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1927">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1928">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1928">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1929">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1929">'Blazor'</span></span>
- <span data-ttu-id="04001-1930">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1930">'Identity'</span></span>
- <span data-ttu-id="04001-1931">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1931">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1932">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1932">'Razor'</span></span>
- <span data-ttu-id="04001-1933">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1933">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1934">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1934">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1935">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1935">'Blazor'</span></span>
- <span data-ttu-id="04001-1936">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1936">'Identity'</span></span>
- <span data-ttu-id="04001-1937">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1937">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1938">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1938">'Razor'</span></span>
- <span data-ttu-id="04001-1939">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1939">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1940">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1940">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1941">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1941">'Blazor'</span></span>
- <span data-ttu-id="04001-1942">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1942">'Identity'</span></span>
- <span data-ttu-id="04001-1943">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1943">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1944">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1944">'Razor'</span></span>
- <span data-ttu-id="04001-1945">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1945">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1946">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1946">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1947">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1947">'Blazor'</span></span>
- <span data-ttu-id="04001-1948">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1948">'Identity'</span></span>
- <span data-ttu-id="04001-1949">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1949">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1950">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1950">'Razor'</span></span>
- <span data-ttu-id="04001-1951">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1951">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1952">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1952">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1953">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1953">'Blazor'</span></span>
- <span data-ttu-id="04001-1954">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1954">'Identity'</span></span>
- <span data-ttu-id="04001-1955">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1955">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1956">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1956">'Razor'</span></span>
- <span data-ttu-id="04001-1957">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1957">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1958">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1958">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1959">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1959">'Blazor'</span></span>
- <span data-ttu-id="04001-1960">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1960">'Identity'</span></span>
- <span data-ttu-id="04001-1961">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1961">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1962">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1962">'Razor'</span></span>
- <span data-ttu-id="04001-1963">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1963">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1964">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1964">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1965">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1965">'Blazor'</span></span>
- <span data-ttu-id="04001-1966">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1966">'Identity'</span></span>
- <span data-ttu-id="04001-1967">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1967">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1968">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1968">'Razor'</span></span>
- <span data-ttu-id="04001-1969">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1969">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1970">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1970">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1971">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1971">'Blazor'</span></span>
- <span data-ttu-id="04001-1972">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1972">'Identity'</span></span>
- <span data-ttu-id="04001-1973">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1973">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1974">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1974">'Razor'</span></span>
- <span data-ttu-id="04001-1975">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1975">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1976">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1976">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1977">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1977">'Blazor'</span></span>
- <span data-ttu-id="04001-1978">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1978">'Identity'</span></span>
- <span data-ttu-id="04001-1979">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1979">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1980">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1980">'Razor'</span></span>
- <span data-ttu-id="04001-1981">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1981">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1982">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1982">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1983">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1983">'Blazor'</span></span>
- <span data-ttu-id="04001-1984">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1984">'Identity'</span></span>
- <span data-ttu-id="04001-1985">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1985">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1986">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1986">'Razor'</span></span>
- <span data-ttu-id="04001-1987">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1987">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1988">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1988">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1989">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1989">'Blazor'</span></span>
- <span data-ttu-id="04001-1990">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1990">'Identity'</span></span>
- <span data-ttu-id="04001-1991">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1991">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1992">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1992">'Razor'</span></span>
- <span data-ttu-id="04001-1993">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1993">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-1994">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-1994">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-1995">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-1995">'Blazor'</span></span>
- <span data-ttu-id="04001-1996">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-1996">'Identity'</span></span>
- <span data-ttu-id="04001-1997">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-1997">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-1998">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-1998">'Razor'</span></span>
- <span data-ttu-id="04001-1999">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-1999">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2000">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2000">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2001">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2001">'Blazor'</span></span>
- <span data-ttu-id="04001-2002">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2002">'Identity'</span></span>
- <span data-ttu-id="04001-2003">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2003">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2004">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2004">'Razor'</span></span>
- <span data-ttu-id="04001-2005">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2005">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2006">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2006">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2007">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2007">'Blazor'</span></span>
- <span data-ttu-id="04001-2008">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2008">'Identity'</span></span>
- <span data-ttu-id="04001-2009">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2009">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2010">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2010">'Razor'</span></span>
- <span data-ttu-id="04001-2011">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2011">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2012">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2012">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2013">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2013">'Blazor'</span></span>
- <span data-ttu-id="04001-2014">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2014">'Identity'</span></span>
- <span data-ttu-id="04001-2015">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2015">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2016">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2016">'Razor'</span></span>
- <span data-ttu-id="04001-2017">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2017">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2018">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2018">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2019">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2019">'Blazor'</span></span>
- <span data-ttu-id="04001-2020">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2020">'Identity'</span></span>
- <span data-ttu-id="04001-2021">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2021">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2022">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2022">'Razor'</span></span>
- <span data-ttu-id="04001-2023">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2023">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2024">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2024">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2025">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2025">'Blazor'</span></span>
- <span data-ttu-id="04001-2026">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2026">'Identity'</span></span>
- <span data-ttu-id="04001-2027">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2027">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2028">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2028">'Razor'</span></span>
- <span data-ttu-id="04001-2029">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2029">'SignalR' uid:</span></span> 

<span data-ttu-id="04001-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Konfigurationseintrag wurde nicht erstellt.</span><span class="sxs-lookup"><span data-stu-id="04001-2030">---------------------------------------- | | `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | Configuration entry not created.</span></span>                                                <span data-ttu-id="04001-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="04001-2031">| | `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="04001-2032">Wert: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="04001-2032">Value: `MySql.Data.MySqlClient` | | `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="04001-2033">Wert: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="04001-2033">Value: `System.Data.SqlClient`  | | `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="04001-2034">Wert: `System.Data.SqlClient`  |</span><span class="sxs-lookup"><span data-stu-id="04001-2034">Value: `System.Data.SqlClient`  |</span></span>

<span data-ttu-id="04001-2035">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="04001-2035">**Example**</span></span>

<span data-ttu-id="04001-2036">Eine benutzerdefinierte Umgebungsvariable einer Verbindungszeichenfolge wird auf dem Server erstellt:</span><span class="sxs-lookup"><span data-stu-id="04001-2036">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="04001-2037">Name: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="04001-2037">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="04001-2038">Wert: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="04001-2038">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="04001-2039">Wenn `IConfiguration` eingefügt und einem Feld namens `_config` zugewiesen wird, sieht der Wert so aus:</span><span class="sxs-lookup"><span data-stu-id="04001-2039">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="04001-2040">Dateikonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="04001-2040">File Configuration Provider</span></span>

<span data-ttu-id="04001-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> ist die Basisklasse für das Laden einer Konfiguration aus dem Dateisystem.</span><span class="sxs-lookup"><span data-stu-id="04001-2041"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="04001-2042">Die folgenden Konfigurationsanbieter sind für bestimmte Dateitypen vorgesehen:</span><span class="sxs-lookup"><span data-stu-id="04001-2042">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="04001-2043">INI-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="04001-2043">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="04001-2044">JSON-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="04001-2044">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="04001-2045">XML-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="04001-2045">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="04001-2046">INI-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="04001-2046">INI Configuration Provider</span></span>

<span data-ttu-id="04001-2047"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der INI-Datei.</span><span class="sxs-lookup"><span data-stu-id="04001-2047">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="04001-2048">Um die INI-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="04001-2048">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="04001-2049">Der Doppelpunkt kann in einer INI-Dateikonfiguration als Abschnittstrennzeichen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="04001-2049">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="04001-2050">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="04001-2050">Overloads permit specifying:</span></span>

* <span data-ttu-id="04001-2051">Ob die Datei optional ist</span><span class="sxs-lookup"><span data-stu-id="04001-2051">Whether the file is optional.</span></span>
* <span data-ttu-id="04001-2052">Ob die Konfiguration bei Dateiänderungen neu geladen wird</span><span class="sxs-lookup"><span data-stu-id="04001-2052">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="04001-2053">Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird</span><span class="sxs-lookup"><span data-stu-id="04001-2053">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="04001-2054">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="04001-2054">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="04001-2055">Ein allgemeines Beispiel einer INI-Konfigurationsdatei:</span><span class="sxs-lookup"><span data-stu-id="04001-2055">A generic example of an INI configuration file:</span></span>

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

<span data-ttu-id="04001-2056">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="04001-2056">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="04001-2057">section0:key0</span><span class="sxs-lookup"><span data-stu-id="04001-2057">section0:key0</span></span>
* <span data-ttu-id="04001-2058">section0:key1</span><span class="sxs-lookup"><span data-stu-id="04001-2058">section0:key1</span></span>
* <span data-ttu-id="04001-2059">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="04001-2059">section1:subsection:key</span></span>
* <span data-ttu-id="04001-2060">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="04001-2060">section2:subsection0:key</span></span>
* <span data-ttu-id="04001-2061">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="04001-2061">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="04001-2062">JSON-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="04001-2062">JSON Configuration Provider</span></span>

<span data-ttu-id="04001-2063"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der JSON-Datei.</span><span class="sxs-lookup"><span data-stu-id="04001-2063">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="04001-2064">Um die JSON-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="04001-2064">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="04001-2065">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="04001-2065">Overloads permit specifying:</span></span>

* <span data-ttu-id="04001-2066">Ob die Datei optional ist</span><span class="sxs-lookup"><span data-stu-id="04001-2066">Whether the file is optional.</span></span>
* <span data-ttu-id="04001-2067">Ob die Konfiguration bei Dateiänderungen neu geladen wird</span><span class="sxs-lookup"><span data-stu-id="04001-2067">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="04001-2068">Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird</span><span class="sxs-lookup"><span data-stu-id="04001-2068">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="04001-2069">`AddJsonFile` wird automatisch zweimal aufgerufen, wenn Sie einen neuen Host-Generator mit `CreateDefaultBuilder` initialisieren.</span><span class="sxs-lookup"><span data-stu-id="04001-2069">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="04001-2070">Die Methode wird aufgerufen, um die Konfiguration aus folgenden Dateien zu laden:</span><span class="sxs-lookup"><span data-stu-id="04001-2070">The method is called to load configuration from:</span></span>

* <span data-ttu-id="04001-2071">*appsettings.json*: Diese Datei wird zuerst gelesen.</span><span class="sxs-lookup"><span data-stu-id="04001-2071">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="04001-2072">Die Umgebungsversion der Datei kann die Werte der Datei *appsettings.json* überschreiben.</span><span class="sxs-lookup"><span data-stu-id="04001-2072">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="04001-2073">*appsettings.{Environment}.json*: Die Umgebungsversion der Datei wird basierend auf [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) geladen.</span><span class="sxs-lookup"><span data-stu-id="04001-2073">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="04001-2074">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="04001-2074">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="04001-2075">`CreateDefaultBuilder` lädt außerdem:</span><span class="sxs-lookup"><span data-stu-id="04001-2075">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="04001-2076">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="04001-2076">Environment variables.</span></span>
* <span data-ttu-id="04001-2077">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) in der Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="04001-2077">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="04001-2078">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="04001-2078">Command-line arguments.</span></span>

<span data-ttu-id="04001-2079">Der JSON-Konfigurationsanbieter wird zuerst eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="04001-2079">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="04001-2080">Daher überschreiben Benutzergeheimnisse, Umgebungsvariablen und Befehlszeilenargumente die von *appsettings*-Dateien festgelegte Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="04001-2080">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="04001-2081">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um Konfiguration der App für andere Dateien als *appsettings.json* und *appsettings.{Environment}.json* anzugeben:</span><span class="sxs-lookup"><span data-stu-id="04001-2081">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="04001-2082">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="04001-2082">**Example**</span></span>

<span data-ttu-id="04001-2083">Die Beispiel-App nutzt zum Erstellen des Hosts die statische Hilfsmethode `CreateDefaultBuilder`, die zwei Aufrufe von `AddJsonFile` enthält:</span><span class="sxs-lookup"><span data-stu-id="04001-2083">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="04001-2084">Der erste Aufruf auf `AddJsonFile` lädt die Konfiguration aus *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="04001-2084">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="04001-2085">Der zweite Aufruf auf `AddJsonFile` lädt die Konfiguration aus *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="04001-2085">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="04001-2086">Die folgende Datei wird für *appsettings.Development.json* in der Beispiel-App geladen:</span><span class="sxs-lookup"><span data-stu-id="04001-2086">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="04001-2087">Führen Sie die Beispiel-App aus.</span><span class="sxs-lookup"><span data-stu-id="04001-2087">Run the sample app.</span></span> <span data-ttu-id="04001-2088">Öffnen Sie einen Browser für die App unter `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="04001-2088">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="04001-2089">Die Ausgabe enthält Schlüssel-Wert-Paare für die Konfiguration basierend auf der Umgebung der App.</span><span class="sxs-lookup"><span data-stu-id="04001-2089">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="04001-2090">Die Protokollebene für den Schlüssel `Logging:LogLevel:Default` ist `Debug`, wenn die App in der Entwicklungsumgebung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="04001-2090">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="04001-2091">Führen Sie die Beispiel-App noch mal in der Produktionsumgebung aus:</span><span class="sxs-lookup"><span data-stu-id="04001-2091">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="04001-2092">Öffnen Sie die Datei *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="04001-2092">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="04001-2093">Ändern Sie im `ConfigurationSample`-Profil den Wert der Umgebungsvariablen `ASPNETCORE_ENVIRONMENT` in `Production`.</span><span class="sxs-lookup"><span data-stu-id="04001-2093">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="04001-2094">Speichern Sie die Datei, und führen Sie die App mit `dotnet run` in einer Befehlsshell aus.</span><span class="sxs-lookup"><span data-stu-id="04001-2094">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="04001-2095">Die Einstellungen in der *appsettings.Development.json*-Datei setzen die Einstellungen in der Datei *appsettings.json* nicht länger außer Kraft.</span><span class="sxs-lookup"><span data-stu-id="04001-2095">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="04001-2096">Die Protokollebene für den Schlüssel `Logging:LogLevel:Default` lautet `Warning`.</span><span class="sxs-lookup"><span data-stu-id="04001-2096">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="04001-2097">XML-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="04001-2097">XML Configuration Provider</span></span>

<span data-ttu-id="04001-2098"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der XML-Datei.</span><span class="sxs-lookup"><span data-stu-id="04001-2098">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="04001-2099">Um die XML-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="04001-2099">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="04001-2100">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="04001-2100">Overloads permit specifying:</span></span>

* <span data-ttu-id="04001-2101">Ob die Datei optional ist</span><span class="sxs-lookup"><span data-stu-id="04001-2101">Whether the file is optional.</span></span>
* <span data-ttu-id="04001-2102">Ob die Konfiguration bei Dateiänderungen neu geladen wird</span><span class="sxs-lookup"><span data-stu-id="04001-2102">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="04001-2103">Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird</span><span class="sxs-lookup"><span data-stu-id="04001-2103">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="04001-2104">Der Stammknoten der Konfigurationsdatei wird beim Erstellen der Konfigurations-Schlüssel-Wert-Paare ignoriert.</span><span class="sxs-lookup"><span data-stu-id="04001-2104">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="04001-2105">Geben Sie keine Dokumenttypdefinition (DTD) und keinen Namespace in der Datei an.</span><span class="sxs-lookup"><span data-stu-id="04001-2105">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="04001-2106">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="04001-2106">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="04001-2107">XML-Konfigurationsdateien können unterschiedliche Elementnamen für wiederholte Abschnitte verwenden:</span><span class="sxs-lookup"><span data-stu-id="04001-2107">XML configuration files can use distinct element names for repeating sections:</span></span>

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

<span data-ttu-id="04001-2108">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="04001-2108">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="04001-2109">section0:key0</span><span class="sxs-lookup"><span data-stu-id="04001-2109">section0:key0</span></span>
* <span data-ttu-id="04001-2110">section0:key1</span><span class="sxs-lookup"><span data-stu-id="04001-2110">section0:key1</span></span>
* <span data-ttu-id="04001-2111">section1:key0</span><span class="sxs-lookup"><span data-stu-id="04001-2111">section1:key0</span></span>
* <span data-ttu-id="04001-2112">section1:key1</span><span class="sxs-lookup"><span data-stu-id="04001-2112">section1:key1</span></span>

<span data-ttu-id="04001-2113">Wiederholte Elemente mit den gleichen Elementnamen funktionieren, wenn das `name`-Attribut zur Unterscheidung der Elemente verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="04001-2113">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

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

<span data-ttu-id="04001-2114">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="04001-2114">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="04001-2115">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="04001-2115">section:section0:key:key0</span></span>
* <span data-ttu-id="04001-2116">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="04001-2116">section:section0:key:key1</span></span>
* <span data-ttu-id="04001-2117">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="04001-2117">section:section1:key:key0</span></span>
* <span data-ttu-id="04001-2118">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="04001-2118">section:section1:key:key1</span></span>

<span data-ttu-id="04001-2119">Mit Attributen können Werte bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="04001-2119">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="04001-2120">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="04001-2120">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="04001-2121">key:attribute</span><span class="sxs-lookup"><span data-stu-id="04001-2121">key:attribute</span></span>
* <span data-ttu-id="04001-2122">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="04001-2122">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="04001-2123">Schlüssel-pro-Datei-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="04001-2123">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="04001-2124"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> verwendet Verzeichnisdateien als Konfigurations-Schlüssel-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="04001-2124">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="04001-2125">Der Schlüssel ist der Dateiname.</span><span class="sxs-lookup"><span data-stu-id="04001-2125">The key is the file name.</span></span> <span data-ttu-id="04001-2126">Der Wert enthält den Inhalt der Datei.</span><span class="sxs-lookup"><span data-stu-id="04001-2126">The value contains the file's contents.</span></span> <span data-ttu-id="04001-2127">Der Schlüssel-pro-Datei-Konfigurationsanbieter wird in Docker-Hostingszenarien verwendet.</span><span class="sxs-lookup"><span data-stu-id="04001-2127">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="04001-2128">Um die Schlüssel-pro-Datei-Konfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="04001-2128">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="04001-2129">Der `directoryPath` zu den Dateien muss ein absoluter Pfad sein.</span><span class="sxs-lookup"><span data-stu-id="04001-2129">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="04001-2130">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="04001-2130">Overloads permit specifying:</span></span>

* <span data-ttu-id="04001-2131">Einen `Action<KeyPerFileConfigurationSource>`-Delegat, der die Quelle konfiguriert</span><span class="sxs-lookup"><span data-stu-id="04001-2131">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="04001-2132">Ob das Verzeichnis optional ist; und den Pfad zum Verzeichnis</span><span class="sxs-lookup"><span data-stu-id="04001-2132">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="04001-2133">Der doppelte Unterstrich (`__`) wird als Trennzeichen für Konfigurationsschlüssel in Dateinamen verwendet.</span><span class="sxs-lookup"><span data-stu-id="04001-2133">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="04001-2134">Der Dateiname `Logging__LogLevel__System` erzeugt z.B. den Konfigurationsschlüssel `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="04001-2134">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="04001-2135">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="04001-2135">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="04001-2136">Speicherkonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="04001-2136">Memory Configuration Provider</span></span>

<span data-ttu-id="04001-2137"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> verwendet eine In-Memory-Sammlung für Konfigurations-Schlüssel-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="04001-2137">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="04001-2138">Um die In-Memory-Sammlungskonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="04001-2138">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="04001-2139">Der Konfigurationsanbieter kann mit `IEnumerable<KeyValuePair<String,String>>` initialisiert werden.</span><span class="sxs-lookup"><span data-stu-id="04001-2139">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="04001-2140">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben.</span><span class="sxs-lookup"><span data-stu-id="04001-2140">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="04001-2141">Im folgenden Beispiel wird ein Konfigurationswörterbuch erstellt:</span><span class="sxs-lookup"><span data-stu-id="04001-2141">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="04001-2142">Das Wörterbuch wird mit einem Aufruf von `AddInMemoryCollection` verwendet, um die Konfiguration bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="04001-2142">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="04001-2143">GetValue</span><span class="sxs-lookup"><span data-stu-id="04001-2143">GetValue</span></span>

<span data-ttu-id="04001-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahiert einen Einzelwert aus der Konfiguration mit einem angegebenen Schlüssel und konvertiert ihn in den angegebenen Typ, der kein Sammlungstyp ist.</span><span class="sxs-lookup"><span data-stu-id="04001-2144">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="04001-2145">Eine Überladung akzeptiert einen Standardwert.</span><span class="sxs-lookup"><span data-stu-id="04001-2145">An overload accepts a default value.</span></span>

<span data-ttu-id="04001-2146">Im Beispiel unten geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="04001-2146">The following example:</span></span>

* <span data-ttu-id="04001-2147">Der Zeichenfolgenwert aus der Konfiguration wird mit dem Schlüssel `NumberKey` extrahiert.</span><span class="sxs-lookup"><span data-stu-id="04001-2147">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="04001-2148">Wenn `NumberKey` nicht in den Konfigurationsschlüsseln gefunden wird, wird der Standardwert `99` verwendet.</span><span class="sxs-lookup"><span data-stu-id="04001-2148">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="04001-2149">Der Typ für den Wert wird als `int` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="04001-2149">Types the value as an `int`.</span></span>
* <span data-ttu-id="04001-2150">Der Wert wird in der `NumberConfig`-Eigenschaft für die Verwendung durch die Seite gespeichert.</span><span class="sxs-lookup"><span data-stu-id="04001-2150">Stores the value in the `NumberConfig` property for use by the page.</span></span>

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

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="04001-2151">GetSection, GetChildren und Exists</span><span class="sxs-lookup"><span data-stu-id="04001-2151">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="04001-2152">Betrachten Sie für die folgenden Beispiele die JSON-Datei unten.</span><span class="sxs-lookup"><span data-stu-id="04001-2152">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="04001-2153">Vier Schlüssel befinden sich in zwei Abschnitten, von denen einer ein Paar Unterabschnitte enthält:</span><span class="sxs-lookup"><span data-stu-id="04001-2153">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

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

<span data-ttu-id="04001-2154">Wenn die Datei in die Konfiguration gelesen wird, werden die folgenden eindeutigen hierarchischen Schlüssel erstellt, um die Konfigurationswerte zu enthalten:</span><span class="sxs-lookup"><span data-stu-id="04001-2154">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="04001-2155">section0:key0</span><span class="sxs-lookup"><span data-stu-id="04001-2155">section0:key0</span></span>
* <span data-ttu-id="04001-2156">section0:key1</span><span class="sxs-lookup"><span data-stu-id="04001-2156">section0:key1</span></span>
* <span data-ttu-id="04001-2157">section1:key0</span><span class="sxs-lookup"><span data-stu-id="04001-2157">section1:key0</span></span>
* <span data-ttu-id="04001-2158">section1:key1</span><span class="sxs-lookup"><span data-stu-id="04001-2158">section1:key1</span></span>
* <span data-ttu-id="04001-2159">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="04001-2159">section2:subsection0:key0</span></span>
* <span data-ttu-id="04001-2160">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="04001-2160">section2:subsection0:key1</span></span>
* <span data-ttu-id="04001-2161">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="04001-2161">section2:subsection1:key0</span></span>
* <span data-ttu-id="04001-2162">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="04001-2162">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="04001-2163">GetSection</span><span class="sxs-lookup"><span data-stu-id="04001-2163">GetSection</span></span>

<span data-ttu-id="04001-2164">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrahiert einen Konfigurationsunterabschnitt mit dem angegebenen Unterabschnittsschlüssel.</span><span class="sxs-lookup"><span data-stu-id="04001-2164">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="04001-2165">Um ein <xref:Microsoft.Extensions.Configuration.IConfigurationSection>-Element zurückzugeben, das nur die Schlüssel-Wert-Paare in `section1` enthält, rufen Sie `GetSection` auf, und geben Sie den Abschnittsnamen an:</span><span class="sxs-lookup"><span data-stu-id="04001-2165">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="04001-2166">Das Element `configSection` weist keinen Wert auf, nur einen Schlüssel und einen Pfad.</span><span class="sxs-lookup"><span data-stu-id="04001-2166">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="04001-2167">Um die Werte für Schlüssel in `section2:subsection0` zu erhalten, rufen Sie `GetSection` auf, und geben Sie den Abschnittspfad an:</span><span class="sxs-lookup"><span data-stu-id="04001-2167">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="04001-2168">`GetSection` gibt nie `null` zurück.</span><span class="sxs-lookup"><span data-stu-id="04001-2168">`GetSection` never returns `null`.</span></span> <span data-ttu-id="04001-2169">Wenn kein entsprechender Abschnitt gefunden wird, wird ein leeres `IConfigurationSection`-Element zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="04001-2169">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="04001-2170">Wenn `GetSection` einen entsprechenden Abschnitt zurückgibt, wird <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> nicht aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="04001-2170">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="04001-2171">Eine Eigenschaft <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> und <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> werden zurückgegeben, wenn der Abschnitt vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="04001-2171">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="04001-2172">GetChildren</span><span class="sxs-lookup"><span data-stu-id="04001-2172">GetChildren</span></span>

<span data-ttu-id="04001-2173">Ein Aufruf von [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) auf `section2` erhält `IEnumerable<IConfigurationSection>` mit folgenden Elementen:</span><span class="sxs-lookup"><span data-stu-id="04001-2173">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="04001-2174">Vorhanden</span><span class="sxs-lookup"><span data-stu-id="04001-2174">Exists</span></span>

<span data-ttu-id="04001-2175">Verwenden Sie [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) um zu bestimmen, ob ein Konfigurationsabschnitt vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="04001-2175">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="04001-2176">Im Falle der Beispieldaten ist `sectionExists``false`, weil in den Konfigurationsdaten kein Abschnitt `section2:subsection2` vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="04001-2176">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="04001-2177">Binden an ein Objektdiagramm</span><span class="sxs-lookup"><span data-stu-id="04001-2177">Bind to an object graph</span></span>

<span data-ttu-id="04001-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> kann ein ganzes POCO-Objektdiagramm binden.</span><span class="sxs-lookup"><span data-stu-id="04001-2178"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="04001-2179">Wie beim Binden eines einfachen Objekts werden nur öffentliche Lese-/Schreibeigenschaften gebunden.</span><span class="sxs-lookup"><span data-stu-id="04001-2179">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="04001-2180">Das Beispiel enthält ein `TvShow`-Modell, dessen Objektdiagramm die Klassen `Metadata` und `Actors` enthält (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="04001-2180">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="04001-2181">Die Beispiel-App verfügt über eine *tvshow.xml*-Datei mit den Konfigurationsdaten:</span><span class="sxs-lookup"><span data-stu-id="04001-2181">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="04001-2182">Die Konfiguration wird mit der `Bind`-Methode an das gesamte `TvShow`-Objektdiagramm gebunden.</span><span class="sxs-lookup"><span data-stu-id="04001-2182">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="04001-2183">Die gebundene Instanz ist einer Eigenschaft zum Rendern zugewiesen:</span><span class="sxs-lookup"><span data-stu-id="04001-2183">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="04001-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) bindet den angegebenen Typ und gibt ihn zurück.</span><span class="sxs-lookup"><span data-stu-id="04001-2184">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="04001-2185">`Get<T>` ist praktischer als `Bind`.</span><span class="sxs-lookup"><span data-stu-id="04001-2185">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="04001-2186">Der folgende Code zeigt, wie `Get<T>` in Bezug auf das vorherige Beispiel verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="04001-2186">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="04001-2187">Binden eines Arrays an eine Klasse</span><span class="sxs-lookup"><span data-stu-id="04001-2187">Bind an array to a class</span></span>

<span data-ttu-id="04001-2188">*Die Beispiel-App veranschaulicht die in diesem Abschnitt erläuterten Konzepte.*</span><span class="sxs-lookup"><span data-stu-id="04001-2188">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="04001-2189"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> unterstützt das Binden von Arrays an Objekte mit Arrayindizes in Konfigurationsschlüsseln.</span><span class="sxs-lookup"><span data-stu-id="04001-2189">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="04001-2190">Jedes Arrayformat, das ein numerisches Schlüsselsegment (`:0:`, `:1:`, &hellip; `:{n}:`) verfügbar macht, kann ein Array an ein POCO-Klassenarray binden.</span><span class="sxs-lookup"><span data-stu-id="04001-2190">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="04001-2191">Das Binden ist standardmäßig möglich.</span><span class="sxs-lookup"><span data-stu-id="04001-2191">Binding is provided by convention.</span></span> <span data-ttu-id="04001-2192">Benutzerdefinierte Konfigurationsanbieter sind nicht erforderlich, um Arraybindung zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="04001-2192">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="04001-2193">**In-Memory-Arrayverarbeitung**</span><span class="sxs-lookup"><span data-stu-id="04001-2193">**In-memory array processing**</span></span>

<span data-ttu-id="04001-2194">Betrachten Sie die Konfigurationsschlüssel und -werte in der folgenden Tabelle.</span><span class="sxs-lookup"><span data-stu-id="04001-2194">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="04001-2195">Key</span><span class="sxs-lookup"><span data-stu-id="04001-2195">Key</span></span>             | <span data-ttu-id="04001-2196">Wert</span><span class="sxs-lookup"><span data-stu-id="04001-2196">Value</span></span>  |
| :---
<span data-ttu-id="04001-2197">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2197">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2198">'Blazor'</span></span>
- <span data-ttu-id="04001-2199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2199">'Identity'</span></span>
- <span data-ttu-id="04001-2200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2200">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2201">'Razor'</span></span>
- <span data-ttu-id="04001-2202">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2203">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2203">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2204">'Blazor'</span></span>
- <span data-ttu-id="04001-2205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2205">'Identity'</span></span>
- <span data-ttu-id="04001-2206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2206">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2207">'Razor'</span></span>
- <span data-ttu-id="04001-2208">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2209">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2209">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2210">'Blazor'</span></span>
- <span data-ttu-id="04001-2211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2211">'Identity'</span></span>
- <span data-ttu-id="04001-2212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2212">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2213">'Razor'</span></span>
- <span data-ttu-id="04001-2214">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2216">'Blazor'</span></span>
- <span data-ttu-id="04001-2217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2217">'Identity'</span></span>
- <span data-ttu-id="04001-2218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2218">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2219">'Razor'</span></span>
- <span data-ttu-id="04001-2220">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2220">'SignalR' uid:</span></span> 

<span data-ttu-id="04001-2221">-------: | :----: | | array:entries:0 | value0 | | array:entries:1 | value1 | | array:entries:2 | value2 | | array:entries:4 | value4 | | array:entries:5 | value5 |</span><span class="sxs-lookup"><span data-stu-id="04001-2221">-------: | :----: | | array:entries:0 | value0 | | array:entries:1 | value1 | | array:entries:2 | value2 | | array:entries:4 | value4 | | array:entries:5 | value5 |</span></span>

<span data-ttu-id="04001-2222">Diese Schlüssel und Werte werden mit dem Speicherkonfigurationsanbieter in die Beispiel-App geladen:</span><span class="sxs-lookup"><span data-stu-id="04001-2222">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="04001-2223">Das Array überspringt einen Wert für Index &num;3.</span><span class="sxs-lookup"><span data-stu-id="04001-2223">The array skips a value for index &num;3.</span></span> <span data-ttu-id="04001-2224">Der Konfigurationsbinder kann weder NULL-Werte binden noch NULL-Einträge in gebundenen Objekten erstellen. Dies wird deutlich, wenn das Ergebnis der Bindung dieses Arrays an ein Objekt demonstriert wird.</span><span class="sxs-lookup"><span data-stu-id="04001-2224">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="04001-2225">In der Beispiel-App ist eine POCO-Klasse verfügbar, um die gebundenen Konfigurationsdaten zu enthalten:</span><span class="sxs-lookup"><span data-stu-id="04001-2225">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="04001-2226">Die Konfigurationsdaten werden an das Objekt gebunden:</span><span class="sxs-lookup"><span data-stu-id="04001-2226">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="04001-2227">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)-Syntax kann ebenfalls verwendet werden, was zu kompakteren Code führt:</span><span class="sxs-lookup"><span data-stu-id="04001-2227">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="04001-2228">Das gebundene Objekt, eine Instanz von `ArrayExample`, empfängt die Arraydaten aus der Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="04001-2228">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="04001-2229">`ArrayExample.Entries`-Index</span><span class="sxs-lookup"><span data-stu-id="04001-2229">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="04001-2230">`ArrayExample.Entries`-Wert</span><span class="sxs-lookup"><span data-stu-id="04001-2230">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="04001-2231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2232">'Blazor'</span></span>
- <span data-ttu-id="04001-2233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2233">'Identity'</span></span>
- <span data-ttu-id="04001-2234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2234">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2235">'Razor'</span></span>
- <span data-ttu-id="04001-2236">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2236">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2237">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2237">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2238">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2238">'Blazor'</span></span>
- <span data-ttu-id="04001-2239">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2239">'Identity'</span></span>
- <span data-ttu-id="04001-2240">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2240">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2241">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2241">'Razor'</span></span>
- <span data-ttu-id="04001-2242">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2242">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2243">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2243">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2244">'Blazor'</span></span>
- <span data-ttu-id="04001-2245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2245">'Identity'</span></span>
- <span data-ttu-id="04001-2246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2246">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2247">'Razor'</span></span>
- <span data-ttu-id="04001-2248">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2249">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2249">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2250">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2250">'Blazor'</span></span>
- <span data-ttu-id="04001-2251">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2251">'Identity'</span></span>
- <span data-ttu-id="04001-2252">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2252">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2253">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2253">'Razor'</span></span>
- <span data-ttu-id="04001-2254">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2254">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2255">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2255">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2256">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2256">'Blazor'</span></span>
- <span data-ttu-id="04001-2257">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2257">'Identity'</span></span>
- <span data-ttu-id="04001-2258">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2258">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2259">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2259">'Razor'</span></span>
- <span data-ttu-id="04001-2260">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2260">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2261">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2262">'Blazor'</span></span>
- <span data-ttu-id="04001-2263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2263">'Identity'</span></span>
- <span data-ttu-id="04001-2264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2264">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2265">'Razor'</span></span>
- <span data-ttu-id="04001-2266">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2266">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2267">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2268">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2268">'Blazor'</span></span>
- <span data-ttu-id="04001-2269">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2269">'Identity'</span></span>
- <span data-ttu-id="04001-2270">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2270">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2271">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2271">'Razor'</span></span>
- <span data-ttu-id="04001-2272">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2272">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2273">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2273">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2274">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2274">'Blazor'</span></span>
- <span data-ttu-id="04001-2275">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2275">'Identity'</span></span>
- <span data-ttu-id="04001-2276">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2276">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2277">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2277">'Razor'</span></span>
- <span data-ttu-id="04001-2278">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2278">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2279">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2280">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2280">'Blazor'</span></span>
- <span data-ttu-id="04001-2281">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2281">'Identity'</span></span>
- <span data-ttu-id="04001-2282">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2282">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2283">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2283">'Razor'</span></span>
- <span data-ttu-id="04001-2284">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2284">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2285">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2286">'Blazor'</span></span>
- <span data-ttu-id="04001-2287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2287">'Identity'</span></span>
- <span data-ttu-id="04001-2288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2288">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2289">'Razor'</span></span>
- <span data-ttu-id="04001-2290">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2291">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2292">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2292">'Blazor'</span></span>
- <span data-ttu-id="04001-2293">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2293">'Identity'</span></span>
- <span data-ttu-id="04001-2294">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2294">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2295">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2295">'Razor'</span></span>
- <span data-ttu-id="04001-2296">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2296">'SignalR' uid:</span></span> 

<span data-ttu-id="04001-2297">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2297">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2298">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2298">'Blazor'</span></span>
- <span data-ttu-id="04001-2299">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2299">'Identity'</span></span>
- <span data-ttu-id="04001-2300">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2300">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2301">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2301">'Razor'</span></span>
- <span data-ttu-id="04001-2302">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2302">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2303">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2304">'Blazor'</span></span>
- <span data-ttu-id="04001-2305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2305">'Identity'</span></span>
- <span data-ttu-id="04001-2306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2306">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2307">'Razor'</span></span>
- <span data-ttu-id="04001-2308">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2309">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2310">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2310">'Blazor'</span></span>
- <span data-ttu-id="04001-2311">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2311">'Identity'</span></span>
- <span data-ttu-id="04001-2312">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2312">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2313">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2313">'Razor'</span></span>
- <span data-ttu-id="04001-2314">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2314">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2315">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2316">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2316">'Blazor'</span></span>
- <span data-ttu-id="04001-2317">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2317">'Identity'</span></span>
- <span data-ttu-id="04001-2318">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2318">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2319">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2319">'Razor'</span></span>
- <span data-ttu-id="04001-2320">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2320">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2321">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2322">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2322">'Blazor'</span></span>
- <span data-ttu-id="04001-2323">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2323">'Identity'</span></span>
- <span data-ttu-id="04001-2324">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2324">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2325">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2325">'Razor'</span></span>
- <span data-ttu-id="04001-2326">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2326">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2328">'Blazor'</span></span>
- <span data-ttu-id="04001-2329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2329">'Identity'</span></span>
- <span data-ttu-id="04001-2330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2330">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2331">'Razor'</span></span>
- <span data-ttu-id="04001-2332">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2334">'Blazor'</span></span>
- <span data-ttu-id="04001-2335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2335">'Identity'</span></span>
- <span data-ttu-id="04001-2336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2336">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2337">'Razor'</span></span>
- <span data-ttu-id="04001-2338">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2340">'Blazor'</span></span>
- <span data-ttu-id="04001-2341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2341">'Identity'</span></span>
- <span data-ttu-id="04001-2342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2342">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2343">'Razor'</span></span>
- <span data-ttu-id="04001-2344">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2346">'Blazor'</span></span>
- <span data-ttu-id="04001-2347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2347">'Identity'</span></span>
- <span data-ttu-id="04001-2348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2348">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2349">'Razor'</span></span>
- <span data-ttu-id="04001-2350">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2352">'Blazor'</span></span>
- <span data-ttu-id="04001-2353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2353">'Identity'</span></span>
- <span data-ttu-id="04001-2354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2354">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2355">'Razor'</span></span>
- <span data-ttu-id="04001-2356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2358">'Blazor'</span></span>
- <span data-ttu-id="04001-2359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2359">'Identity'</span></span>
- <span data-ttu-id="04001-2360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2360">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2361">'Razor'</span></span>
- <span data-ttu-id="04001-2362">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2362">'SignalR' uid:</span></span> 

<span data-ttu-id="04001-2363">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value4                       | | 4                            | value5                       |</span><span class="sxs-lookup"><span data-stu-id="04001-2363">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value4                       | | 4                            | value5                       |</span></span>

<span data-ttu-id="04001-2364">Index &num;3 im gebundenen Objekt enthält die Konfigurationsdaten für den `array:4`-Konfigurationsschlüssel und die Wert für `value4`.</span><span class="sxs-lookup"><span data-stu-id="04001-2364">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="04001-2365">Beim Binden von Konfigurationsdaten, die ein Array enthalten, werden die Arrayindizes in den Konfigurationsschlüsseln lediglich zum Durchlaufen der Konfigurationsdaten beim Erstellen des Objekts verwendet.</span><span class="sxs-lookup"><span data-stu-id="04001-2365">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="04001-2366">Ein NULL-Wert kann in den Konfigurationsdaten nicht beibehalten werden, und ein NULL-Eintrag wird nicht in einem gebundenen Objekt erstellt, wenn ein Array in Konfigurationsschlüsseln mindestens einen Index überspringt.</span><span class="sxs-lookup"><span data-stu-id="04001-2366">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="04001-2367">Das fehlende Konfigurationselement für Index &num;3 kann vor dem Binden an die `ArrayExample`Instanz von jedem Konfigurationsanbieter bereitgestellt werden, der das richtige Schlüssel-Wert-Paar in der Konfiguration erzeugt.</span><span class="sxs-lookup"><span data-stu-id="04001-2367">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="04001-2368">Wenn das Beispiel einen zusätzlichen JSON-Konfigurationsanbieter mit dem fehlenden Schlüssel-Wert-Paar enthält, entspricht `ArrayExample.Entries` dem kompletten Konfigurationsarray:</span><span class="sxs-lookup"><span data-stu-id="04001-2368">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="04001-2369">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="04001-2369">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="04001-2370">In `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="04001-2370">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="04001-2371">Das Schlüssel-Wert-Paar in der Tabelle wird in die Konfiguration geladen.</span><span class="sxs-lookup"><span data-stu-id="04001-2371">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="04001-2372">Key</span><span class="sxs-lookup"><span data-stu-id="04001-2372">Key</span></span>             | <span data-ttu-id="04001-2373">Wert</span><span class="sxs-lookup"><span data-stu-id="04001-2373">Value</span></span>  |
| :---
<span data-ttu-id="04001-2374">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2374">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2375">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2375">'Blazor'</span></span>
- <span data-ttu-id="04001-2376">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2376">'Identity'</span></span>
- <span data-ttu-id="04001-2377">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2377">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2378">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2378">'Razor'</span></span>
- <span data-ttu-id="04001-2379">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2379">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2380">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2381">'Blazor'</span></span>
- <span data-ttu-id="04001-2382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2382">'Identity'</span></span>
- <span data-ttu-id="04001-2383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2383">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2384">'Razor'</span></span>
- <span data-ttu-id="04001-2385">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2386">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2387">'Blazor'</span></span>
- <span data-ttu-id="04001-2388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2388">'Identity'</span></span>
- <span data-ttu-id="04001-2389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2389">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2390">'Razor'</span></span>
- <span data-ttu-id="04001-2391">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2392">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2393">'Blazor'</span></span>
- <span data-ttu-id="04001-2394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2394">'Identity'</span></span>
- <span data-ttu-id="04001-2395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2395">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2396">'Razor'</span></span>
- <span data-ttu-id="04001-2397">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2397">'SignalR' uid:</span></span> 

<span data-ttu-id="04001-2398">-------: | :----: | | array:entries:3 | value3 |</span><span class="sxs-lookup"><span data-stu-id="04001-2398">-------: | :----: | | array:entries:3 | value3 |</span></span>

<span data-ttu-id="04001-2399">Wenn die `ArrayExample`-Klasseninstanz gebunden wird, nachdem der JSON-Konfigurationsanbieter den Eintrag für Index &num;3 enthält, enthält das `ArrayExample.Entries`-Array den Wert.</span><span class="sxs-lookup"><span data-stu-id="04001-2399">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="04001-2400">`ArrayExample.Entries`-Index</span><span class="sxs-lookup"><span data-stu-id="04001-2400">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="04001-2401">`ArrayExample.Entries`-Wert</span><span class="sxs-lookup"><span data-stu-id="04001-2401">`ArrayExample.Entries` Value</span></span> |
| :---
<span data-ttu-id="04001-2402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2402">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2403">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2403">'Blazor'</span></span>
- <span data-ttu-id="04001-2404">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2404">'Identity'</span></span>
- <span data-ttu-id="04001-2405">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2405">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2406">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2406">'Razor'</span></span>
- <span data-ttu-id="04001-2407">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2407">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2408">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2409">'Blazor'</span></span>
- <span data-ttu-id="04001-2410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2410">'Identity'</span></span>
- <span data-ttu-id="04001-2411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2411">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2412">'Razor'</span></span>
- <span data-ttu-id="04001-2413">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2414">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2415">'Blazor'</span></span>
- <span data-ttu-id="04001-2416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2416">'Identity'</span></span>
- <span data-ttu-id="04001-2417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2417">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2418">'Razor'</span></span>
- <span data-ttu-id="04001-2419">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2420">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2421">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2421">'Blazor'</span></span>
- <span data-ttu-id="04001-2422">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2422">'Identity'</span></span>
- <span data-ttu-id="04001-2423">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2423">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2424">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2424">'Razor'</span></span>
- <span data-ttu-id="04001-2425">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2425">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2426">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2427">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2427">'Blazor'</span></span>
- <span data-ttu-id="04001-2428">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2428">'Identity'</span></span>
- <span data-ttu-id="04001-2429">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2429">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2430">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2430">'Razor'</span></span>
- <span data-ttu-id="04001-2431">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2431">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2432">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2433">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2433">'Blazor'</span></span>
- <span data-ttu-id="04001-2434">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2434">'Identity'</span></span>
- <span data-ttu-id="04001-2435">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2435">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2436">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2436">'Razor'</span></span>
- <span data-ttu-id="04001-2437">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2437">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2438">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2439">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2439">'Blazor'</span></span>
- <span data-ttu-id="04001-2440">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2440">'Identity'</span></span>
- <span data-ttu-id="04001-2441">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2441">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2442">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2442">'Razor'</span></span>
- <span data-ttu-id="04001-2443">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2443">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2444">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2444">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2445">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2445">'Blazor'</span></span>
- <span data-ttu-id="04001-2446">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2446">'Identity'</span></span>
- <span data-ttu-id="04001-2447">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2447">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2448">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2448">'Razor'</span></span>
- <span data-ttu-id="04001-2449">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2449">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2450">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2450">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2451">'Blazor'</span></span>
- <span data-ttu-id="04001-2452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2452">'Identity'</span></span>
- <span data-ttu-id="04001-2453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2453">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2454">'Razor'</span></span>
- <span data-ttu-id="04001-2455">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2456">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2456">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2457">'Blazor'</span></span>
- <span data-ttu-id="04001-2458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2458">'Identity'</span></span>
- <span data-ttu-id="04001-2459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2459">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2460">'Razor'</span></span>
- <span data-ttu-id="04001-2461">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2462">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2462">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2463">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2463">'Blazor'</span></span>
- <span data-ttu-id="04001-2464">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2464">'Identity'</span></span>
- <span data-ttu-id="04001-2465">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2465">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2466">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2466">'Razor'</span></span>
- <span data-ttu-id="04001-2467">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2467">'SignalR' uid:</span></span> 

<span data-ttu-id="04001-2468">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2468">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2469">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2469">'Blazor'</span></span>
- <span data-ttu-id="04001-2470">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2470">'Identity'</span></span>
- <span data-ttu-id="04001-2471">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2471">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2472">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2472">'Razor'</span></span>
- <span data-ttu-id="04001-2473">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2473">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2474">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2474">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2475">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2475">'Blazor'</span></span>
- <span data-ttu-id="04001-2476">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2476">'Identity'</span></span>
- <span data-ttu-id="04001-2477">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2477">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2478">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2478">'Razor'</span></span>
- <span data-ttu-id="04001-2479">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2479">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2480">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2480">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2481">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2481">'Blazor'</span></span>
- <span data-ttu-id="04001-2482">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2482">'Identity'</span></span>
- <span data-ttu-id="04001-2483">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2483">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2484">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2484">'Razor'</span></span>
- <span data-ttu-id="04001-2485">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2485">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2486">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2486">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2487">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2487">'Blazor'</span></span>
- <span data-ttu-id="04001-2488">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2488">'Identity'</span></span>
- <span data-ttu-id="04001-2489">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2489">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2490">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2490">'Razor'</span></span>
- <span data-ttu-id="04001-2491">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2491">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2492">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2492">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2493">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2493">'Blazor'</span></span>
- <span data-ttu-id="04001-2494">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2494">'Identity'</span></span>
- <span data-ttu-id="04001-2495">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2495">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2496">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2496">'Razor'</span></span>
- <span data-ttu-id="04001-2497">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2497">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2498">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2498">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2499">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2499">'Blazor'</span></span>
- <span data-ttu-id="04001-2500">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2500">'Identity'</span></span>
- <span data-ttu-id="04001-2501">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2501">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2502">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2502">'Razor'</span></span>
- <span data-ttu-id="04001-2503">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2503">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2504">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2504">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2505">'Blazor'</span></span>
- <span data-ttu-id="04001-2506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2506">'Identity'</span></span>
- <span data-ttu-id="04001-2507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2507">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2508">'Razor'</span></span>
- <span data-ttu-id="04001-2509">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2510">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2510">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2511">'Blazor'</span></span>
- <span data-ttu-id="04001-2512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2512">'Identity'</span></span>
- <span data-ttu-id="04001-2513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2513">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2514">'Razor'</span></span>
- <span data-ttu-id="04001-2515">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2516">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2516">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2517">'Blazor'</span></span>
- <span data-ttu-id="04001-2518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2518">'Identity'</span></span>
- <span data-ttu-id="04001-2519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2519">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2520">'Razor'</span></span>
- <span data-ttu-id="04001-2521">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2522">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2522">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2523">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2523">'Blazor'</span></span>
- <span data-ttu-id="04001-2524">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2524">'Identity'</span></span>
- <span data-ttu-id="04001-2525">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2525">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2526">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2526">'Razor'</span></span>
- <span data-ttu-id="04001-2527">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2527">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2528">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2528">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2529">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2529">'Blazor'</span></span>
- <span data-ttu-id="04001-2530">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2530">'Identity'</span></span>
- <span data-ttu-id="04001-2531">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2531">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2532">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2532">'Razor'</span></span>
- <span data-ttu-id="04001-2533">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2533">'SignalR' uid:</span></span> 

<span data-ttu-id="04001-2534">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value3                       | | 4                            | value4                       | | 5                            | value5                       |</span><span class="sxs-lookup"><span data-stu-id="04001-2534">-------------: | | 0                            | value0                       | | 1                            | value1                       | | 2                            | value2                       | | 3                            | value3                       | | 4                            | value4                       | | 5                            | value5                       |</span></span>

<span data-ttu-id="04001-2535">**JSON-Arrayverarbeitung**</span><span class="sxs-lookup"><span data-stu-id="04001-2535">**JSON array processing**</span></span>

<span data-ttu-id="04001-2536">Wenn eine JSON-Datei ein Array enthält, werden Konfigurationsschlüssel für die Arrayelemente mit einem nullbasierten Abschnittsindex erstellt.</span><span class="sxs-lookup"><span data-stu-id="04001-2536">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="04001-2537">In der folgenden Konfigurationsdatei ist `subsection` ein Array:</span><span class="sxs-lookup"><span data-stu-id="04001-2537">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="04001-2538">Der JSON-Konfigurationsanbieter liest die Konfigurationsdaten in die folgenden Schlüssel-Wert-Paare:</span><span class="sxs-lookup"><span data-stu-id="04001-2538">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="04001-2539">Key</span><span class="sxs-lookup"><span data-stu-id="04001-2539">Key</span></span>                     | <span data-ttu-id="04001-2540">Wert</span><span class="sxs-lookup"><span data-stu-id="04001-2540">Value</span></span>  |
| ---
<span data-ttu-id="04001-2541">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2541">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2542">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2542">'Blazor'</span></span>
- <span data-ttu-id="04001-2543">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2543">'Identity'</span></span>
- <span data-ttu-id="04001-2544">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2544">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2545">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2545">'Razor'</span></span>
- <span data-ttu-id="04001-2546">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2546">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2547">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2547">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2548">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2548">'Blazor'</span></span>
- <span data-ttu-id="04001-2549">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2549">'Identity'</span></span>
- <span data-ttu-id="04001-2550">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2550">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2551">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2551">'Razor'</span></span>
- <span data-ttu-id="04001-2552">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2552">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2553">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2553">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2554">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2554">'Blazor'</span></span>
- <span data-ttu-id="04001-2555">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2555">'Identity'</span></span>
- <span data-ttu-id="04001-2556">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2556">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2557">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2557">'Razor'</span></span>
- <span data-ttu-id="04001-2558">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2558">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2559">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2559">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2560">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2560">'Blazor'</span></span>
- <span data-ttu-id="04001-2561">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2561">'Identity'</span></span>
- <span data-ttu-id="04001-2562">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2562">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2563">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2563">'Razor'</span></span>
- <span data-ttu-id="04001-2564">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2564">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2565">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2565">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2566">'Blazor'</span></span>
- <span data-ttu-id="04001-2567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2567">'Identity'</span></span>
- <span data-ttu-id="04001-2568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2568">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2569">'Razor'</span></span>
- <span data-ttu-id="04001-2570">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2571">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2571">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2572">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2572">'Blazor'</span></span>
- <span data-ttu-id="04001-2573">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2573">'Identity'</span></span>
- <span data-ttu-id="04001-2574">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2574">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2575">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2575">'Razor'</span></span>
- <span data-ttu-id="04001-2576">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2576">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2577">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2577">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2578">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2578">'Blazor'</span></span>
- <span data-ttu-id="04001-2579">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2579">'Identity'</span></span>
- <span data-ttu-id="04001-2580">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2580">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2581">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2581">'Razor'</span></span>
- <span data-ttu-id="04001-2582">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2582">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2583">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2583">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2584">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2584">'Blazor'</span></span>
- <span data-ttu-id="04001-2585">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2585">'Identity'</span></span>
- <span data-ttu-id="04001-2586">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2586">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2587">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2587">'Razor'</span></span>
- <span data-ttu-id="04001-2588">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2588">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2589">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2589">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2590">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2590">'Blazor'</span></span>
- <span data-ttu-id="04001-2591">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2591">'Identity'</span></span>
- <span data-ttu-id="04001-2592">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2592">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2593">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2593">'Razor'</span></span>
- <span data-ttu-id="04001-2594">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2594">'SignalR' uid:</span></span> 

<span data-ttu-id="04001-2595">------------ | :----: | | json_array:key          | valueA | | json_array:subsection:0 | valueB | | json_array:subsection:1 | valueC | | json_array:subsection:2 | valueD |</span><span class="sxs-lookup"><span data-stu-id="04001-2595">------------ | :----: | | json_array:key          | valueA | | json_array:subsection:0 | valueB | | json_array:subsection:1 | valueC | | json_array:subsection:2 | valueD |</span></span>

<span data-ttu-id="04001-2596">In der Beispiel-App können die Konfigurations-Schlüssel-Wert-Paare mit dieser POCO-Klasse gebunden werden:</span><span class="sxs-lookup"><span data-stu-id="04001-2596">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="04001-2597">Nach dem Binden enthält `JsonArrayExample.Key` den Wert `valueA`.</span><span class="sxs-lookup"><span data-stu-id="04001-2597">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="04001-2598">Die Unterabschnittwerte werden in der POCO-Arrayeigenschaft `Subsection` gespeichert.</span><span class="sxs-lookup"><span data-stu-id="04001-2598">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="04001-2599">`JsonArrayExample.Subsection`-Index</span><span class="sxs-lookup"><span data-stu-id="04001-2599">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="04001-2600">`JsonArrayExample.Subsection`-Wert</span><span class="sxs-lookup"><span data-stu-id="04001-2600">`JsonArrayExample.Subsection` Value</span></span> |
| :---
<span data-ttu-id="04001-2601">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2601">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2602">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2602">'Blazor'</span></span>
- <span data-ttu-id="04001-2603">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2603">'Identity'</span></span>
- <span data-ttu-id="04001-2604">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2604">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2605">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2605">'Razor'</span></span>
- <span data-ttu-id="04001-2606">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2606">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2607">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2607">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2608">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2608">'Blazor'</span></span>
- <span data-ttu-id="04001-2609">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2609">'Identity'</span></span>
- <span data-ttu-id="04001-2610">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2610">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2611">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2611">'Razor'</span></span>
- <span data-ttu-id="04001-2612">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2612">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2613">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2613">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2614">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2614">'Blazor'</span></span>
- <span data-ttu-id="04001-2615">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2615">'Identity'</span></span>
- <span data-ttu-id="04001-2616">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2616">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2617">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2617">'Razor'</span></span>
- <span data-ttu-id="04001-2618">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2618">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2619">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2619">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2620">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2620">'Blazor'</span></span>
- <span data-ttu-id="04001-2621">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2621">'Identity'</span></span>
- <span data-ttu-id="04001-2622">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2622">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2623">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2623">'Razor'</span></span>
- <span data-ttu-id="04001-2624">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2624">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2625">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2625">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2626">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2626">'Blazor'</span></span>
- <span data-ttu-id="04001-2627">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2627">'Identity'</span></span>
- <span data-ttu-id="04001-2628">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2628">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2629">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2629">'Razor'</span></span>
- <span data-ttu-id="04001-2630">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2630">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2631">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2631">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2632">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2632">'Blazor'</span></span>
- <span data-ttu-id="04001-2633">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2633">'Identity'</span></span>
- <span data-ttu-id="04001-2634">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2634">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2635">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2635">'Razor'</span></span>
- <span data-ttu-id="04001-2636">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2636">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2637">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2637">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2638">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2638">'Blazor'</span></span>
- <span data-ttu-id="04001-2639">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2639">'Identity'</span></span>
- <span data-ttu-id="04001-2640">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2640">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2641">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2641">'Razor'</span></span>
- <span data-ttu-id="04001-2642">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2642">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2643">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2643">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2644">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2644">'Blazor'</span></span>
- <span data-ttu-id="04001-2645">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2645">'Identity'</span></span>
- <span data-ttu-id="04001-2646">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2646">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2647">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2647">'Razor'</span></span>
- <span data-ttu-id="04001-2648">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2648">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2649">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2649">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2650">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2650">'Blazor'</span></span>
- <span data-ttu-id="04001-2651">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2651">'Identity'</span></span>
- <span data-ttu-id="04001-2652">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2652">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2653">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2653">'Razor'</span></span>
- <span data-ttu-id="04001-2654">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2654">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2655">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2655">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2656">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2656">'Blazor'</span></span>
- <span data-ttu-id="04001-2657">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2657">'Identity'</span></span>
- <span data-ttu-id="04001-2658">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2658">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2659">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2659">'Razor'</span></span>
- <span data-ttu-id="04001-2660">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2660">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2661">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2661">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2662">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2662">'Blazor'</span></span>
- <span data-ttu-id="04001-2663">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2663">'Identity'</span></span>
- <span data-ttu-id="04001-2664">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2664">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2665">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2665">'Razor'</span></span>
- <span data-ttu-id="04001-2666">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2666">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2667">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2667">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2668">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2668">'Blazor'</span></span>
- <span data-ttu-id="04001-2669">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2669">'Identity'</span></span>
- <span data-ttu-id="04001-2670">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2670">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2671">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2671">'Razor'</span></span>
- <span data-ttu-id="04001-2672">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2672">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2673">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2673">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2674">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2674">'Blazor'</span></span>
- <span data-ttu-id="04001-2675">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2675">'Identity'</span></span>
- <span data-ttu-id="04001-2676">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2676">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2677">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2677">'Razor'</span></span>
- <span data-ttu-id="04001-2678">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2678">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2679">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2680">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2680">'Blazor'</span></span>
- <span data-ttu-id="04001-2681">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2681">'Identity'</span></span>
- <span data-ttu-id="04001-2682">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2682">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2683">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2683">'Razor'</span></span>
- <span data-ttu-id="04001-2684">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2684">'SignalR' uid:</span></span> 

<span data-ttu-id="04001-2685">-----------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2685">-----------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2686">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2686">'Blazor'</span></span>
- <span data-ttu-id="04001-2687">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2687">'Identity'</span></span>
- <span data-ttu-id="04001-2688">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2688">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2689">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2689">'Razor'</span></span>
- <span data-ttu-id="04001-2690">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2690">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2691">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2692">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2692">'Blazor'</span></span>
- <span data-ttu-id="04001-2693">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2693">'Identity'</span></span>
- <span data-ttu-id="04001-2694">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2694">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2695">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2695">'Razor'</span></span>
- <span data-ttu-id="04001-2696">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2696">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2697">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2698">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2698">'Blazor'</span></span>
- <span data-ttu-id="04001-2699">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2699">'Identity'</span></span>
- <span data-ttu-id="04001-2700">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2700">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2701">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2701">'Razor'</span></span>
- <span data-ttu-id="04001-2702">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2702">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2703">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2703">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2704">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2704">'Blazor'</span></span>
- <span data-ttu-id="04001-2705">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2705">'Identity'</span></span>
- <span data-ttu-id="04001-2706">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2706">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2707">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2707">'Razor'</span></span>
- <span data-ttu-id="04001-2708">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2708">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2709">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2710">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2710">'Blazor'</span></span>
- <span data-ttu-id="04001-2711">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2711">'Identity'</span></span>
- <span data-ttu-id="04001-2712">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2712">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2713">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2713">'Razor'</span></span>
- <span data-ttu-id="04001-2714">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2714">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2715">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2715">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2716">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2716">'Blazor'</span></span>
- <span data-ttu-id="04001-2717">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2717">'Identity'</span></span>
- <span data-ttu-id="04001-2718">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2718">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2719">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2719">'Razor'</span></span>
- <span data-ttu-id="04001-2720">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2720">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2721">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2722">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2722">'Blazor'</span></span>
- <span data-ttu-id="04001-2723">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2723">'Identity'</span></span>
- <span data-ttu-id="04001-2724">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2724">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2725">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2725">'Razor'</span></span>
- <span data-ttu-id="04001-2726">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2726">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2728">'Blazor'</span></span>
- <span data-ttu-id="04001-2729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2729">'Identity'</span></span>
- <span data-ttu-id="04001-2730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2730">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2731">'Razor'</span></span>
- <span data-ttu-id="04001-2732">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2734">'Blazor'</span></span>
- <span data-ttu-id="04001-2735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2735">'Identity'</span></span>
- <span data-ttu-id="04001-2736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2736">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2737">'Razor'</span></span>
- <span data-ttu-id="04001-2738">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2740">'Blazor'</span></span>
- <span data-ttu-id="04001-2741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2741">'Identity'</span></span>
- <span data-ttu-id="04001-2742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2742">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2743">'Razor'</span></span>
- <span data-ttu-id="04001-2744">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2746">'Blazor'</span></span>
- <span data-ttu-id="04001-2747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2747">'Identity'</span></span>
- <span data-ttu-id="04001-2748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2748">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2749">'Razor'</span></span>
- <span data-ttu-id="04001-2750">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2752">'Blazor'</span></span>
- <span data-ttu-id="04001-2753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2753">'Identity'</span></span>
- <span data-ttu-id="04001-2754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2754">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2755">'Razor'</span></span>
- <span data-ttu-id="04001-2756">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2758">'Blazor'</span></span>
- <span data-ttu-id="04001-2759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2759">'Identity'</span></span>
- <span data-ttu-id="04001-2760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2760">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2761">'Razor'</span></span>
- <span data-ttu-id="04001-2762">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2762">'SignalR' uid:</span></span> 

-
<span data-ttu-id="04001-2763">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="04001-2763">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="04001-2764">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04001-2764">'Blazor'</span></span>
- <span data-ttu-id="04001-2765">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04001-2765">'Identity'</span></span>
- <span data-ttu-id="04001-2766">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04001-2766">'Let's Encrypt'</span></span>
- <span data-ttu-id="04001-2767">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04001-2767">'Razor'</span></span>
- <span data-ttu-id="04001-2768">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="04001-2768">'SignalR' uid:</span></span> 

<span data-ttu-id="04001-2769">-----------------: | | 0                                   | valueB                              | | 1                                   | valueC                              | | 2                                   | valueD                              |</span><span class="sxs-lookup"><span data-stu-id="04001-2769">-----------------: | | 0                                   | valueB                              | | 1                                   | valueC                              | | 2                                   | valueD                              |</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="04001-2770">Benutzerdefinierter Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="04001-2770">Custom configuration provider</span></span>

<span data-ttu-id="04001-2771">Die Beispiel-App veranschaulicht, wie ein Standardkonfigurationsanbieter erstellt wird, der Konfigurations-Schlüssel-Wert-Paare aus einer Datenbank mit [Entity Framework (EF)](/ef/core/) liest.</span><span class="sxs-lookup"><span data-stu-id="04001-2771">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="04001-2772">Der Anbieter weist die folgenden Merkmale auf:</span><span class="sxs-lookup"><span data-stu-id="04001-2772">The provider has the following characteristics:</span></span>

* <span data-ttu-id="04001-2773">Die EF-In-Memory-Datenbank wird zu Demonstrationszwecken verwendet.</span><span class="sxs-lookup"><span data-stu-id="04001-2773">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="04001-2774">Um eine Datenbank zu verwenden, die eine Verbindungszeichenfolge benötigt, implementieren Sie einen sekundären `ConfigurationBuilder`, um die Verbindungszeichenfolge aus einem anderen Konfigurationsanbieter anzugeben.</span><span class="sxs-lookup"><span data-stu-id="04001-2774">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="04001-2775">Der Anbieter liest eine Datenbanktabelle beim Start in die Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="04001-2775">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="04001-2776">Der Anbieter fragt die Datenbank nicht pro Schlüssel ab.</span><span class="sxs-lookup"><span data-stu-id="04001-2776">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="04001-2777">Das erneute Laden bei Änderung ist nicht implementiert. Das heißt, das Aktualisieren der Datenbank nach App-Start hat keine Auswirkungen auf die App-Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="04001-2777">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="04001-2778">Definieren Sie eine `EFConfigurationValue`-Entität zum Speichern von Konfigurationswerten in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="04001-2778">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="04001-2779">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="04001-2779">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="04001-2780">Fügen Sie `EFConfigurationContext` hinzu, um die konfigurierten Werte zu speichern und auf diese zugreifen.</span><span class="sxs-lookup"><span data-stu-id="04001-2780">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="04001-2781">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="04001-2781">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="04001-2782">Erstellen Sie eine Klasse, die das <xref:Microsoft.Extensions.Configuration.IConfigurationSource> implementiert.</span><span class="sxs-lookup"><span data-stu-id="04001-2782">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="04001-2783">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="04001-2783">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="04001-2784">Erstellen Sie den benutzerdefinierten Konfigurationsanbieter durch Vererbung von <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="04001-2784">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="04001-2785">Der Konfigurationsanbieter initialisiert die Datenbank, wenn diese leer ist.</span><span class="sxs-lookup"><span data-stu-id="04001-2785">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="04001-2786">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="04001-2786">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="04001-2787">Mit einer `AddEFConfiguration`-Erweiterungsmethode kann die Konfigurationsquelle `ConfigurationBuilder` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="04001-2787">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="04001-2788">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="04001-2788">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="04001-2789">Der folgende Code veranschaulicht die Verwendung des benutzerdefinierten Anbieters `EFConfigurationProvider` in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="04001-2789">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="04001-2790">Zugreifen auf die Konfiguration während des Starts</span><span class="sxs-lookup"><span data-stu-id="04001-2790">Access configuration during startup</span></span>

<span data-ttu-id="04001-2791">Fügen Sie `IConfiguration` in den `Startup`-Konstruktor ein, um auf Konfigurationswerte in `Startup.ConfigureServices` zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="04001-2791">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="04001-2792">Um auf die Konfiguration in `Startup.Configure` zuzugreifen, fügen Sie entweder `IConfiguration` direkt in die Methode ein, oder verwenden Sie die Instanz aus dem Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="04001-2792">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

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

<span data-ttu-id="04001-2793">Ein Beispiel für den Zugriff auf die Konfiguration mit den Starthilfsmethoden finden Sie unter [Anwendungsstart: Hilfsmethoden](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="04001-2793">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-razor-pages-page-or-mvc-view"></a><span data-ttu-id="04001-2794">Zugreifen auf die Konfiguration auf einer Razor Pages-Seite oder in einer MVC-Ansicht</span><span class="sxs-lookup"><span data-stu-id="04001-2794">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="04001-2795">Um auf die Konfigurationseinstellungen auf einer Razor Pages-Seite oder in einer MVC-Ansicht zuzugreifen, fügen Sie eine [using-Anweisung](xref:mvc/views/razor#using) ([C#-Referenz: using-Anweisung](/dotnet/csharp/language-reference/keywords/using-directive)) für den [Microsoft.Extensions.Configuration-Namespace](xref:Microsoft.Extensions.Configuration) hinzu, und fügen Sie <xref:Microsoft.Extensions.Configuration.IConfiguration> auf der Seite bzw. in der Ansicht ein.</span><span class="sxs-lookup"><span data-stu-id="04001-2795">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="04001-2796">Auf einer Razor Pages-Seite:</span><span class="sxs-lookup"><span data-stu-id="04001-2796">In a Razor Pages page:</span></span>

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

<span data-ttu-id="04001-2797">In einer MVC-Ansicht:</span><span class="sxs-lookup"><span data-stu-id="04001-2797">In an MVC view:</span></span>

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

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="04001-2798">Hinzufügen von Konfigurationen aus einer externen Assembly</span><span class="sxs-lookup"><span data-stu-id="04001-2798">Add configuration from an external assembly</span></span>

<span data-ttu-id="04001-2799">Eine <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>-Implementierung ermöglicht das Hinzufügen von Erweiterungen zu einer App beim Start von einer externen Assembly außerhalb der `Startup`-Klasse der App aus.</span><span class="sxs-lookup"><span data-stu-id="04001-2799">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="04001-2800">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="04001-2800">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="04001-2801">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="04001-2801">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
